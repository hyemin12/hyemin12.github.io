# frozen_string_literal: true

source "https://rubygems.org"

gemspec


gem "jekyll-coffeescript"
gem "jekyll-default-layout"
gem "jekyll-github-metadata"
gem "jekyll-optional-front-matter"
gem "jekyll-readme-index"
gem "jekyll-titles-from-headings"
gem "jekyll-relative-links"

group :test do
  gem "html-proofer", "~> 4.4"
end

gem 'csv'
gem 'base64'
gem 'bigdecimal'

gem 'rake', '>= 0.a'

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]