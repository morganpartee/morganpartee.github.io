source "https://rubygems.org"

# Use the github-pages gem so local `bundle exec jekyll serve` matches what
# GitHub Pages runs in production. You don't need this to deploy — GH Pages
# builds the site server-side — but it's handy for previewing.
gem "github-pages", group: :jekyll_plugins

group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
  gem "jekyll-sitemap"
end

# Windows / JRuby quirks — harmless elsewhere.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end
gem "wdm", "~> 0.1.1", platforms: [:mingw, :x64_mingw, :mswin]
