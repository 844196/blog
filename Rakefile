desc "Create new post"
task :new do
  today    = { date: Date.today.strftime('%Y-%m-%d'),
               time: Time.now.strftime('%Y-%m-%d %H:%M') }
  title    = ARGV.last
  filename = "#{today[:date]}-#{title}.md"
  path     = File.join('.', '_posts', filename)

  abort "rake aborted: '#{path}' already exists" if File.exist? path
  File.open(path, 'w') do |post|
    post.puts '---'
    post.puts 'layout: post'
    post.puts "title: #{title}"
    post.puts "date: #{today[:time]}"
    post.puts '---'
  end

  STDOUT.puts path

  ARGV.slice(1, ARGV.size).each {|v| task v.to_sym {} }
end

# alias
task :post => [:new]

desc "Live preview"
task :preview do
  `bundle exec guard start`
end
