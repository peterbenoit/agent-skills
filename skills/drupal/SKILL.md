---
name: drupal
description: Builds and maintains Drupal sites and modules. Use when working on Drupal theming, custom modules, hooks, Twig templates, config management, content types, Views, or migrations. Use when the user mentions Drupal, drush, Twig, hook_*, config:export, or CMS-specific tasks.
---

# Drupal

## Overview

Drupal is a structured CMS with a strict hook system, config management workflow, and Twig-based theming. Work with Drupal's conventions — fighting them creates technical debt that compounds fast.

## When to Use

- Theming: Twig templates, CSS/JS libraries, theme hooks
- Custom module development: hooks, services, plugins, forms
- Config management: exporting, importing, deployment
- Content architecture: content types, fields, Views
- Migrations: migrate API, source plugins
- Drush commands and site maintenance

## Project Structure

```
web/
  modules/custom/      # Custom modules
  themes/custom/       # Custom theme
  sites/default/
    settings.php
    settings.local.php  # Never commit
config/
  sync/                # Exported config — commit this
```

## Drush Essentials

```bash
# Cache
drush cr                          # Cache rebuild (most common fix-first step)

# Config
drush config:export               # Export active config to sync directory
drush config:import               # Import config from sync directory
drush config:status               # See what's changed

# Updates
drush updatedb                    # Run pending database updates
drush updb -y && drush cr         # Common deploy sequence

# Users
drush uli                         # One-time login link
drush user:password admin newpass

# Modules
drush pm:enable module_name
drush pm:uninstall module_name
```

## Config Management Workflow

All config changes go through the sync directory — never make config changes in prod that you haven't exported.

```bash
# Development cycle
# 1. Make changes in UI or code
# 2. Export
drush config:export

# 3. Review the diff
git diff config/sync/

# 4. Commit
git add config/sync/
git commit -m "Add media library to article content type"

# 5. Deploy and import
drush config:import -y && drush cr
```

Split configs for environment overrides in `settings.php`:

```php
$config['system.performance']['css']['preprocess'] = TRUE;
$config['system.performance']['js']['preprocess'] = TRUE;
```

## Twig Theming

### Template suggestions

Drupal auto-generates template suggestions based on context. To find which template is active and what suggestions are available, enable Twig debug:

```php
// settings.local.php
$settings['twig_debug'] = TRUE;
$settings['cache']['bins']['render'] = 'cache.backend.null';
$settings['cache']['bins']['page'] = 'cache.backend.null';
```

HTML comments in source will show `FILE NAME SUGGESTIONS` and `BEGIN OUTPUT FROM`.

### Common template locations

```
templates/
  node/
    node--article.html.twig
    node--article--teaser.html.twig
  field/
    field--field-hero-image.html.twig
  views/
    views-view--my-view.html.twig
    views-view-unformatted--my-view--block-1.html.twig
  block/
    block--system-branding-block.html.twig
```

### Twig patterns

```twig
{# Print a field safely #}
{{ content.field_body }}

{# Dump available variables (debug only) #}
{{ dump(attributes) }}

{# Conditionals #}
{% if content.field_image %}
  <div class="hero">{{ content.field_image }}</div>
{% endif %}

{# Loop over items #}
{% for item in items %}
  <li{{ item.attributes }}>{{ item.content }}</li>
{% endfor %}

{# Modify attributes #}
<div{{ attributes.addClass('my-class').setAttribute('data-foo', 'bar') }}>

{# Translate #}
{{ 'Published'|t }}
{{ 'Hello @name'|t({'@name': name}) }}

{# URL generation #}
<a href="{{ path('entity.node.canonical', {'node': node.id}) }}">Read more</a>
```

## Hook System

Hooks are function name conventions — `hook_` is replaced with `MODULENAME_`:

```php
// mymodule.module

/**
 * Implements hook_node_presave().
 */
function mymodule_node_presave(NodeInterface $node): void {
  if ($node->bundle() === 'article') {
    $node->set('field_computed', computeValue($node));
  }
}

/**
 * Implements hook_theme().
 */
function mymodule_theme(): array {
  return [
    'my_template' => [
      'variables' => ['title' => NULL, 'items' => []],
      'template' => 'my-template',
    ],
  ];
}

/**
 * Implements hook_form_FORM_ID_alter().
 */
function mymodule_form_node_article_form_alter(array &$form, FormStateInterface $form_state, string $form_id): void {
  $form['field_custom']['#access'] = \Drupal::currentUser()->hasPermission('edit custom field');
}
```

## Services and Dependency Injection

```yaml
# mymodule.services.yml
services:
  mymodule.my_service:
    class: Drupal\mymodule\MyService
    arguments: ['@entity_type.manager', '@logger.factory']
```

```php
// src/MyService.php
namespace Drupal\mymodule;

use Drupal\Core\Entity\EntityTypeManagerInterface;
use Drupal\Core\Logger\LoggerChannelFactoryInterface;

final class MyService {
  public function __construct(
    private readonly EntityTypeManagerInterface $entityTypeManager,
    private readonly LoggerChannelFactoryInterface $loggerFactory,
  ) {}
}
```

Retrieve from the container only at the module level (procedural hooks) — use constructor injection everywhere else:

```php
// Acceptable in hooks (procedural context)
$service = \Drupal::service('mymodule.my_service');

// Preferred everywhere else
// Constructor injection via services.yml
```

## Custom Module Structure

```
web/modules/custom/mymodule/
  mymodule.info.yml
  mymodule.module
  mymodule.routing.yml
  mymodule.services.yml
  mymodule.libraries.yml
  src/
    Controller/
    Form/
    Plugin/
    Service/
  templates/
  config/
    install/    # Default config shipped with module
    schema/     # Config schema
  tests/
    src/
      Unit/
      Kernel/
      Functional/
```

```yaml
# mymodule.info.yml
name: My Module
type: module
description: 'Description of what it does.'
core_version_requirement: ^10 || ^11
package: Custom
dependencies:
  - drupal:node
  - drupal:field
```

## JS Libraries

```yaml
# mymodule.libraries.yml
my-library:
  version: 1.x
  css:
    theme:
      css/mymodule.css: {}
  js:
    js/mymodule.js: {}
  dependencies:
    - core/drupalSettings
    - core/once
```

Attach in a hook or template:

```php
// In a hook
$build['#attached']['library'][] = 'mymodule/my-library';
```

```twig
{# In a template #}
{{ attach_library('mymodule/my-library') }}
```

Use `once` (Drupal's replacement for jQuery `once`) to prevent double-initialization:

```js
// ES5 (Drupal behavior pattern)
(function (Drupal, once) {
  Drupal.behaviors.myBehavior = {
    attach(context) {
      once('myBehavior', '[data-my-component]', context).forEach(el => {
        initComponent(el);
      });
    },
  };
})(Drupal, once);
```

## Performance

- Enable CSS/JS aggregation in production (config or `settings.php`)
- Use render cache tags on custom render arrays
- Avoid `entity_load` in loops — use `loadMultiple`
- Views exposed filters should use AJAX only when pagination requires it
- Use `drush php:eval` to test logic without full page requests

## Common Pitfalls

| Pitfall | Fix |
|---|---|
| Editing config in prod without exporting | Always export and commit config |
| Forgetting `drush cr` after code changes | Add it to deploy scripts |
| Using `global $user` | Use `\Drupal::currentUser()` or inject `AccountProxyInterface` |
| Node load in a View field | Use Views relationships or a computed field |
| `node_load()` in a loop | Use `\Drupal::entityTypeManager()->getStorage('node')->loadMultiple($ids)` |
| Storing state in module variable | Use State API (`\Drupal::state()`) or config |
| Hardcoding paths | Use `Url::fromRoute()` or `\Drupal::request()->getBasePath()` |

## Deploy Checklist

- [ ] Config exported and committed
- [ ] `composer.lock` committed
- [ ] No settings.local.php committed
- [ ] `drush updatedb -y` in deploy script
- [ ] `drush config:import -y` in deploy script
- [ ] `drush cr` after import
- [ ] Twig debug disabled in production
- [ ] CSS/JS aggregation enabled
