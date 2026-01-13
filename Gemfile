source 'https://rubygems.org'

ruby '3.4.6'  # match workflow Ruby version

# Core Jekyll gem
gem 'jekyll'

# Plugins compatible with GitHub Actions (Ubuntu)
group :jekyll_plugins do
  gem 'jekyll-archives-v2'
  gem 'jekyll-email-protect'
  gem 'jekyll-feed'
  gem 'jekyll-get-json'
  gem 'jekyll-imagemagick'   # requires imagemagick installed in workflow
  gem 'jekyll-jupyter-notebook'
  gem 'jekyll-link-attributes'
  gem 'jekyll-minifier'
  gem 'jekyll-paginate-v2'
  gem 'jekyll-regex-replace'
  gem 'jekyll-sitemap'
  gem 'jekyll-tabs'
  gem 'jekyll-terser', git: 'https://github.com/RobertoJBeltran/jekyll-terser.git'  # needs Node.js
  gem 'jekyll-toc'
  gem 'jekyll-twitter-plugin'
  gem 'jemoji'
  gem 'classifier-reborn'
end

# Development / external data fetching
group :other_plugins do
  gem 'css_parser'
  gem 'feedjira'
  gem 'httparty'
  gem 'observer'
  gem 'ostruct'
end

# Platform-specific gems (skip on Ubuntu)
gem 'wdm', '>=0.1.0', platforms: [:mswin, :mingw, :x64_mingw]
