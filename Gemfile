source "https://rubygems.org"
# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!

# gem "jekyll", "~> 4.3.4"

gem "just-the-docs"
gem "github-pages", group: :jekyll_plugins

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1", :platforms => [:mingw, :x64_mingw, :mswin]

gem "kramdown", ">=2.3.1"  # for security reasons.
gem "addressable", ">= 2.8.0"  # for security reasons.
gem "rexml", ">= 3.2.5"  # for security reasons.
gem "nokogiri", ">= 1.11.4"  # for security reasons.

gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]