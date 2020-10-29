Potpie: Pseudo Po Translations
==============================

###### The original author is `dstufft` and code was forked from [dstufft](https://github.com/dstufft/potpie). I'm using this place just for bug fixes for internal use.

Potpie is a GPL Licensed cli wrapper around the Psuedo Translations available
from Transifex_ pseudo translations. The available types are ``brackets``,
``unicode``, ``planguage``, ``extend``, and ``mixed``.

Installation
------------

To install potpie, simply: ::
###### install potpie with your_personal_token

    $ pip install potpie --extra-index-url https://__token__:<your_personal_token>@gitlab.com/api/v4/projects/17886059/packages/pypi/simple

Usage
-----

To create a pseudo translated po file, simply: ::

    $ potpie [--type] infile outfile

An example for a Django project might be: ::

    $ potpie locale/en/LC_MESSAGES/django.po locale/xx_pseudo/LC_MESSAGES/django.po

The default type is mixed, if you wish to use another type simplify specify it
as an option like: ::

    $ potpie --type brackets locale/en/LC_MESSAGES/django.po locale/xx_pseudo/LC_MESSAGES/django.po


Types
------

Brackets
    Adds square brackets around the string (e.g. [translated text])

Unicode
    Converts all characters into look alike unicode characters

PLanguage
    Increases the length of a string by around 30-50% by replacing the vowels with
    unicode chars that look alike.

    This is based on a P-language, which is a simple vowel-extending language.

Extend:
    Increases the length of a string by around 20-700% by appending special
    chars (Greek, Chinese, etc) to the end of the string.

Mixed:
    Combines Extend, Unicode and Brackets into one Type.

.. _Transifex: https://transifex.net/

# Once installed use the following script to extract and compile locale messages

```
current_dir=$(pwd)

# current_branch=$(git branch | grep \* | cut -d ' ' -f2)
current_branch=$(git branch --show-current)

cd smartcounting_web/locale

for locale in 'fr' 'en' 'es' 'pt'; do
  python manage.py makemessages -l $locale
  python manage.py makemessages -l $locale --domain djangojs --extension html

  if [[ "$locale" == 'en' ]]; then
  	potpie --type unicode smartcounting/locale/en/LC_MESSAGES/django.po smartcounting/locale/ts/LC_MESSAGES/django.po
  	potpie --type unicode smartcounting/locale/en/LC_MESSAGES/djangojs.po smartcounting/locale/ts/LC_MESSAGES/djangojs.po
  fi
done

zip -r ~/Downloads/$current_branch.zip smartcounting/locale -x "*.mo"

git checkout -- smartcounting/locale

rm -rf smartcounting/locale/pt/

cd $current_dir
```

## Next Steps (apart from potpie stuff)
1) A zip file of the `.po` files to translate should now be available at
 `~/Downloads` with the current branch name.

2) After, send the file the the person responsible for translations.

3) While waiting for the translations, documentation should be writen for the upcoming feature in
the stories of the board. Once documentation is done, stories should be tagged with the version
where the first appear and the `Doc` tag to notify user manual writer where to find the information.

4) Once the translated `.po` files are ready, update the code, build a new image and set a
system with the upcoming version, so that the user manual writer can take snapshots of the system
in different languages for the user manual.

5) Once this is done, wait for the new version of the user manual and once it is available, update
the code with the new file.

6) After this, the code can be merged into `master` and then, the CI/CD will take care of deploying
the image to the system in production.
