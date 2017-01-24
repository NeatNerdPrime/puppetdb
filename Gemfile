source ENV['GEM_SOURCE'] || "https://rubygems.org"
puppet_branch = ENV['PUPPET_VERSION'] || "latest"
oldest_supported_puppet = "3.8.1"
beaker_version = ENV['BEAKER_VERSION']

def location_for(place, fake_version = nil)
  if place =~ /^(git:[^#]*)#(.*)/
    [fake_version, { :git => $1, :branch => $2, :require => false }].compact
  elsif place =~ /^file:\/\/(.*)/
    ['>= 0', { :path => File.expand_path($1), :require => false }]
  else
    [place, { :require => false }]
  end
end

gem 'facter'

case RUBY_VERSION
when '1.8.7'
  gem 'rake', '<= 10.1.1'
  # activesupport calls in the latest i18n, which drops 1.8.7. This pins to
  # a lower version
  gem 'i18n', '~> 0.6.11'
else
  gem 'rake'
end

group :test do
  # Add test-unit for ruby 2.2+ support (has been removed from stdlib)
  gem 'test-unit'

  # Pinning for Ruby 1.9.3 support
  gem 'json_pure', '~> 1.8'
  # Pinning for Ruby < 2.2.0 support
  gem 'activesupport', '~> 4.2'

  # Pinning to work-around an incompatiblity with 2.14 in puppetlabs_spec_helper
  gem 'rspec', '2.13.0'
  gem 'puppetlabs_spec_helper', '0.4.1', :require => false

  # docker-api 1.32.0 requires ruby 2.0.0
  gem 'docker-api', '1.31.0'

  case puppet_branch
  when "latest"
    gem 'puppet', ">= #{oldest_supported_puppet}", :require => false
  when "oldest"
    gem 'puppet', oldest_supported_puppet, :require => false
  else
    gem 'puppet', :git => 'git://github.com/puppetlabs/puppet.git',
      :branch => puppet_branch, :require => false
  end

  gem 'mocha', '~> 1.0'

  # Since newer versions of rake are not supported, we pin
  case RUBY_VERSION
  when '1.8.7'
    # No activerecord or sqlite for you
  else
    gem 'sqlite3'
  end
end

group :acceptance do
  if beaker_version
    #use the specified version
    gem 'beaker', *location_for(beaker_version)
  else
    # TEMP: this is so we can include the duplicate aws keys fix, until released
    gem 'beaker', :git => 'git://github.com/puppetlabs/beaker.git',
      :ref => '38873baa8f7b17d5ffdfaa92803d5a6beb21256f'
  end
end
