# DigitalOcean Spaces Sync
This WordPress plugin syncs your media library with [DigitalOcean 
Spaces](https://goo.gl/SX2UwH).

## Current changes
This fork carries PHP 8.x compatibility fixes on top of the original 2.2.1 
release:

- Guarded the `RejectedPromise` reason check in `vendor/guzzlehttp/promises` 
with `is_object()` before `method_exists()` (verbatim upstream guzzle/promises 
1.4.0 fix). On PHP 8+, `method_exists()` with an array throws a `TypeError`; 
the AWS SDK rejects failed S3 requests with an array payload, so every upload 
fataled inside `wp_unique_filename()` before any image processing ran.
- Wrapped `filter_wp_unique_filename()` in `dos_class.php` in a `try`/`catch` 
(`\Throwable`), mirroring the author's existing fail-safe pattern in 
`file_upload()`/`file_delete()`. A Spaces outage now degrades to a log line 
plus the original filename instead of killing the upload.

These changes are also captured in `do-spaces-sync-php85.patch` for 
reapplication.

## Description
This WordPress plugin syncs your media library with [DigitalOcean 
Spaces](https://goo.gl/SX2UwH). It allows you to simuntaneously upload 
and delete files, replacing public media URL with relative cloud storage links. 
Cchoose between two options: to keep local copy of the files, or to 
delete them and keep files only in cloud storage.

In order to use this plugin, you have to create a DigitalOcean Spaces API key.

You may now define constants in order to configure the plugin. If the constant 
is defined, it overwrites the value from settings page.
Contants description:
- `DOS_KEY` - DigitalOcean Spaces key
- `DOS_SECRET` - DigitalOcean Spaces secret,
- `DOS_ENDPOINT` - DigitalOcean Spaces endpoint,
- `DOS_CONTAINER` - DigitalOcean Spaces container,
- `DOS_STORAGE_PATH` - The path to the file in the storage, will appear as a 
prefix,
- `DOS_STORAGE_FILE_ONLY` - Keep files only in DigitalOcean Spaces or not, 
values (true|false),
- `DOS_STORAGE_FILE_DELETE` - Remove files in DigitalOcean Spaces on delete or 
not, values (true|false),
- `DOS_FILTER` - A Regex filter,
- `UPLOAD_URL_PATH` - A full url to the files, WP Constant,
- `UPLOAD_PATH` - A path to the local files, WP Constant

There is a known issue with the built in Wordpress Image Editor, it will not 
upload changed images. Know how to fix this, PR welcome.

## Installation

1. Upload plugin directory to `/wp-content/plugins/`
2. Activate plugin through 'Plugins' menu in WordPress
3. Go to `Settings -> DigitalOcean Spaces Sync` and set up plugin

If plugin has been downloaded from GitHub, you have to install vendor 
components via `composer update`.
