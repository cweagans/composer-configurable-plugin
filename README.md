# cweagans/composer-configurable-plugin

[![Build Status](https://travis-ci.org/cweagans/composer-configurable-plugin.svg?branch=master)](https://travis-ci.org/cweagans/composer-configurable-plugin)
[![Coverage Status](https://coveralls.io/repos/github/cweagans/composer-configurable-plugin/badge.svg?branch=master)](https://coveralls.io/github/cweagans/composer-configurable-plugin?branch=master)

Provides a lightweight configuration system for Composer plugins. You probably
don't need to install this unless a plugin requires it.

A given configuration value can come from one of three places:

1. A default value declared by the plugin.
2. The value set in `composer.json`
3. An environment variable.

This code was extracted from the 2.x branch of [cweagans/composer-patches](https://github.com/cweagans/composer-patches).

## Usage

```php
<?php

class MyPlugin implements PluginInterface {
    // Step 1: Use the trait.
    use \cweagans\Composer\ConfigurablePlugin;
    
    
    public function activate(Composer $composer, IOInterface $io)
    {
        // Step 2: Define your configuration.
        $this->configuration = [
            // You can have any number of blocks like this, provided the keys
            // are all unique.
            'name-of-configuration-value' => [
                // Required: This allows ConfigurablePlugin to accurately determine
                // values from environment variables. This can be string, int, bool, or list.
                'type' => 'string',
                // Required: In the event that the users of your plugin don't bother
                // to configure your plugin, you need to provide sane defaults.
                'default' => 'asdf',
            ],
            'another-config-value' => [
                'type' => 'bool',
                'default' => TRUE,
            ],
        ];
        
        // Step 3: Call configure().
        // The second argument here needs to be something unique across all Composer plugins.
        // If in doubt, use the name of your plugin package (i.e. the argument here for
        // cweagans/composer-patches would be composer-patches). This is used for pulling
        // values from composer.json and for constructing the name of the environment var to
        // check for config.
        $this->configure($composer->getPackage()->getExtra(), 'unique-key-for-your-plugin');
        
        // Step 4: Get a config value. In increasing order of priority:
        //    - Default value provided in $configuration
        //    - Value provided in composer.json
        //    - Environment variable
        $this->getConfig('another-config-value');
    }
}
```

## Why?

Because this code didn't belong in cweagans/composer-patches.

## No, I mean why are all the methods public?

Because I didn't want to spend the time to write all the test code for
protected functions. Patches welcome.
