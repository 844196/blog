# vim:set filetype=ruby:

guard :shell do
  watch(%r{css/.+\.less}) { `lessc --clean-css css/style.less css/style.css` }
end

guard 'jekyll-plus', :server => true do
  watch(%r{.*})
  ignore(%r{^_site/})
end

guard :livereload do
  watch(%r{.*})
end
