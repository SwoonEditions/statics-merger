# Statics Merger

A composer plugin aimed to simplify the workflow between the frontend and backend development teams. Static repositories that the frontend team use are added as a composer dependency to the project as type ```static```.

The plugin hooks onto two composer commands ```install``` and ```update``` in which on completion will symlink all static packages as defined in their ```composer.json``` file.

## Installation

This module is installable via ```Composer```. If you have used the Magento Skeleton as a base module then you can just require this project and all the rest is done for you.

```
$ cd project-root
$ ./composer.phar require "jhhello/statics-merger"
```

*__Note:__ As these repositories are currently private and not available via a public pakage list like Packagist Or Firegento you need to add the repository to the projects composer.json before you require the project.*

```
"repositories": [
    {
        "type": "git",
        "url": "git@bitbucket.org:jhhello/statics-merger.git"
    }
]
```

### Upgrading 1.x to 2.x ?

It's recommended to first run `composer --no-plugins` after updating your composer.json and then run a `composer update` to run a cleanup and map the new configuration.

*__Note:__ Depending on the configuration changes you may also have to manually cleanup any remaining symlinks from the old mappings*

## Configuration

### Statics project

*If the ```composer.json``` file is not already there, create one with relevant information and commit it to the repo.*

For this to work the statics repository requires the ```composer.json``` to have the ```type``` set to ```static```.


### Magento project

Within your projects ```composer.json``` you will need to ensure you have a few configurations set up.

In your ```require``` you will need to add any statics that you want and if private also add the repo.

```
"require": {
    "jhhello/static-repo": "dev-master"
},
"repositories": [
    {
        "type": "git",
		"url": "git@jh.git.beanstalkapp.com:/jh/static-repo.git"
    }
]
```

While in your ```extra``` you need the ```magento-root-dir``` set correctly and have defined the ```static-map``` for each static repository.

```
"extra":{
    "magento-root-dir": "htdocs/",
    "static-map" : {
        "jhhello/static-repo": {
            "package/theme": [
                {
                    "src": "favicon*",
                    "dest": ""
                },
                {
                    "src": "assets/images/catalog",
                    "dest": "images/catalog"
                },
                {
                    "src": "assets/js/varien",
                    "dest": "js/varien"
                }
            ]
        }
    }
}
```

The first key is the name of the repository which you have used in the ```require``` section, while inside there each key is the ```package/theme``` and the example would map to ```skin/frontend/package/theme``` within your ```magento-root-dir```.

The ```package/theme``` array contains several objects defining the ```src``` and ```dest``` of the files. The ```src``` value is relevant to the __root__ of the __statics__ repository while the ```dest``` is relevant to the ```package/theme``` defined in the __Magento project__ such as ```skin/frontend/package/theme/``` within your ```magento-root-dir```.

__Need to map a static repo to more than 1 package or theme?__ No problem just add another `package/theme` array to your repos mappings, of course make sure you use a different name to any others to avoid overwriting.

You can also use globs which makes it pretty awesome! A great use case for this is favicons where you could have multiple at different resolutions with a set naming convention. To target them all you would simply use ```favicon*``` like in the default example below.

*__Note:__ Globs require the ```dest``` to be a folder and not a file, whereas files and directories need to point to there corresponding full path which allows you to rename them if required.*

#### Examples
All favicons to root dir ```skin/frontend/package/theme/```

```
{
    "src": "favicon*",
    "dest": ""
}
```

Link an image into a different directory structure and rename

```
{
    "src": "assets/images/awesome/cake.gif",
    "dest": "images/newcake.gif"
}
```

#### Default

```
...
{
    "src": "assets",
    "dest": "assets"
},
{
    "src": "favicon*",
    "dest": ""
},
{
    "src": "assets/images/catalog",
    "dest": "images/catalog"
}
...
```

#### Final Note

Don't forget to add the `package/theme` dir to your `.gitignore` otherwise our going to be adding the statics repo back into your Magento repo.

## Problems?

If you find any problems or edge cases which may need to be accounted for within this composer plugin just open up an issue with as much detail as possible so it can be recreated.

## Running Tests

```
$ cd vendor/jhhello/statics-merger
$ php composer.phar install
$ ./vendor/bin/phpunit
```
