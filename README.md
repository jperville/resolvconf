# resolvconf Cookbook

This cookbook maintains /etc/resolv.conf using the resolvconf package, which is installed by default on Debian/Ubuntu.

## Requirements

A system that supports resolvconf.

- Ubuntu >= 10.04
- Debian >= 6.0

Furthermore you need to add the following line to your metadata.rb

    depends 'resolvconf'


## Attributes

This section describes the supported attributes, as well as their default settings.

Use the following attributes to specify your nameserver(s) to use, the search domain(s) and additional options.
Each attribute supports strings, as well as arrays with multiple elements.

```ruby
node['resolvconf']['nameserver'] = [ '208.67.222.222', '208.67.220.220' ] # Set nameserver(s) to use
node['resolvconf']['search'] = node['domain'] # Set domains to search
node['resolvconf']['options'] = [] # Set options
```


These attributes specify strings that are included in /etc/resolv.conf at head/body/tail.

```ruby
node['resolvconf']['head'] = [
  '# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)',
  '#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN'
]
node['resolvconf']['base'] = []
node['resolvconf']['tail'] = []
```


By default, the recipe removes any dns-* configuration lines from /etc/network/interfaces,
as they might interferre with the configured settings.

```ruby
node['resolvconf']['clear-dns-from-interfaces'] = true
```


## Provider

### resolvconf

The LWRP basically supports all options that can be set via attributes, and uses the same defaults.
It will do the following

- It will create the necessary files in /etc/resolvconf/resolv.conf.d/
- Remove dns-* lines from /etc/network/interfaces (unless clear_dns_form_interfaces is specified)
- Run 'resolvconf -u'

Before using the provider, you probably want to ensure that the resolvconf package is installed.

```ruby
include_recipe 'resolvconf::install'
```

Example:

```ruby
resolvconf 'default'
```

```ruby
resolvconf 'custom' do
  nameserver '8.8.8.8'
  search     'mydomain.com'
  options    'rotate'

  head       "# Don't touch this configuration file!"
  base       "# Will be added after nameserver, search, options config items"
  tail       "# This goes to the end of the file."

  # do not touch my interface configuration plz!
  clear_dns_from_interfaces false
end
```

If you do not want to use resolvconf, you can still use this recipe to maintain your /etc/resolv.conf using the "fallback" provider.

```ruby
resolvconf 'custom' do
  provider   Chef::Provider::ResolvconfFallback

  nameserver '8.8.8.8'
  search     'mydomain.com'
  options    'rotate'

  head       "# Don't touch this configuration file!"
  base       "# Will be added after nameserver, search, options config items"
  tail       "# This goes to the end of the file."
end

## Recipes

### resolvconf:install

Installs the resolvconf package.


### resolvconf::default

Includes resolvconf::install, then configures the node using the specified attributes / defaults.



# Contributing

Contributions are very welcome!

1. Fork the repository on Github
2. Create a named feature branch (like `add_component_x`)
3. Write you change
4. Write tests for your change (if applicable)
5. Run the tests, ensuring they all pass
6. Submit a Pull Request using Github


# License and Authors

Authors: Chris Aumann <me@chr4.org>

License: GPLv3
