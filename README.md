# Custom Paths

A module for ProcessWire CMS/CMF. Allows any page to have a custom path/URL.

## Screenshot

![custom-paths](https://user-images.githubusercontent.com/1538852/80270665-3bc0bb80-870e-11ea-80cc-5fe8a8202079.png)

## Usage

The module creates a field named `custom_path` on install. Add the `custom_path` field to the template of any page you want to set a custom path for. Whatever path is entered into this field determines the path and URL of the page (`$page->path` and `$page->url`). Page numbers and URL segments are supported if these are enabled for the template, and previous custom paths are managed by PagePathHistory if that module is installed.

The `custom_path` field appears on the Settings tab in Page Edit by default but there is an option in the module configuration to disable this if you want to position the field among the other template fields.

If the `custom_path` field is populated for a page it should be a path that is relative to the site root and that starts with a forward slash. The module prevents the same custom path being set for more than one page.

The `custom_path` value takes precedence over any ProcessWire path. You can even override the Home page by setting a custom path of "/" for a page.

It is highly recommended to set access controls on the `custom_path` field so that only privileged roles can edit it: superuser-only is recommended.

It is up to the user to set and maintain suitable custom paths for any pages where the module is in use. Make sure your custom paths are compatible with ProcessWire's `$config` and `.htaccess` settings, and if you are basing the custom path on the names of parent pages you will probably want to have a strategy for updating custom paths if parent pages are renamed or moved.

### Example hooks to Pages::saveReady

You might want to use a `Pages::saveReady` hook to automatically set the custom path for some pages. Below are a couple of examples.

1\. In this example the start of the custom path is fixed but the end of the path will update dynamically according to the name of the page:

```php
$pages->addHookAfter('saveReady', function(HookEvent $event) {
    $page = $event->arguments(0);
    if($page->template == 'my_template') {
        $page->custom_path = "/some-custom/path-segments/$page->name/";
    }
});
```

2\. The Custom Paths module adds a new `Page::realPath` method/property that can be used to get the "real" ProcessWire path to a page that might have a custom path set. In this example the custom path for news items is derived from the real ProcessWire path but a parent named "news-items" is removed:

```php
$pages->addHookAfter('saveReady', function(HookEvent $event) {
    $page = $event->arguments(0);
    if($page->template == 'news_item') {
        $page->custom_path = str_replace('/news-items/', '/', $page->realPath);
    }
});
```

## Caveats

The custom paths will be used automatically for links created in CKEditor fields, but if you have the "link abstraction" option enabled for CKEditor fields (Details > Markup/HTML (Content Type) > HTML Options) then you will see notices from MarkupQA warning you that it is unable to resolve the links.

## Installation

[Install](http://modules.processwire.com/install-uninstall/) the Custom Paths module.

## Uninstallation

The `custom_path` field is not automatically deleted when the module is uninstalled. You can delete it manually if the field is no longer needed.