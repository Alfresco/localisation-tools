Localisation Tools
==================

This is a bash script that is designed to work with [Alfresco](https://github.com/Alfresco/community-edition) and Alfresco's modules e.g. [RM](https://github.com/Alfresco/records-management) and projects e.g. [Aikau](https://github.com/Alfresco/Aikau).

It enables easy management of translation bundles, with the ability to generate (export) a bundle of properties files, import them, as well as perform some automated QA on the files.

For individual projects, it makes use of a l10n.properties file to override configuration, e.g. for the search path used to determine which files should be translated (see [example](https://github.com/Alfresco/records-management/blob/master/l10n.properties))

Set up
======
This script needs to know where the code lives. It can make some assumptions (e.g. current directory), but it works best if you set $WORK_DIR first: 
```
export WORK_DIR=/full/path/to/code/root
```

(Alfresco engineers: our old internal Dev Env takes care of that if you use 'create-project' and 'workon')

Configuration
=============

See here for project specific overrides: https://github.com/Alfresco/localisation-tools/wiki/Localisation-Properties-Configuration

Commands
========

## Generating bundles:

These commands use the _MESSAGE_SEARCH_PATH_ and _EXCLUDED_FILES_ config vars to whitelist and then filter the list of _.properties_ files to include

```
l10n-bundle files
```
returns a list of the files (in the default locale) that are eligable for translation. 

```
l10n-bundle files <locale>
```
As above, but returns a list of the files in the specified locale that already exist. If _locale_ is "all", then all locales (inc. default) will be returned.

```
l10n-bundle export <path>
```
generates a zip file containing all the properties files for each supported language. These zip files are written to _path_ and are named with the locale and the working copy revision number (svn) or current time stamp (git).

```
l10n-bundle install <path>
```
overlays a directory of properties files from _path_ to _$WORK_DIR_. Used to install a bundle following translation.

##Checking installed bundles
```
l10n-bundle count
```
runs a quick count of all the files and strings for each of the supported locales.


```
l10n-bundle check <path>
```
runs a rudimentary QA script that checks each translation for completeness and technical (but not linguistic) correctness. Reports are written to _path_. One report per locale is generated, containing: 
* strings missing from translated bundle
* files that contain missing strings
* strings in translated bundle but not in default one (redundant strings)
* files containing redundant strings
* duplicated string definitions
* translated strings that match the default locale
* strings with incorrectly escaped unicode characters
* strings with potentially incorrect quoting

It also generates one summary report with counts so you can quickly review what the status of a translation is.

##String Encoding

Alfresco and related modules require properties files to contain only ASCII characters, anytihng outside of that character set must be encoded as a unicode escape sequence. I've included several shortcuts to assist with decoding already escaped strings, or encoding strings that aren't escaped.

In the commands below, _encoding_ is usually UTF8, but supports anything that [native2ascii](http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/native2ascii.html) supports.

```
l10n-bundle decode <path> <encoding>
```
Converts all properties files located at _path_ (recursively) from unicode escaped ASCII to _encoding_. This is useful when exporting bundles and sending them to a translator.

```
l10n-bundle decode-string <string> <encoding>
```
Converts the string from unicode escaped ASCII to _encoding_.

```
l10n-bundle encode <path> <encoding>
```
Converts all properties files located at _path_ (recursively) to unicode escaped ASCII from _encoding_. This is useful when installing bundles from translators.

```
l10n-bundle encode-string <string> <encoding>
```
Converts the string to unicode escaped ASCII from _encoding_.


