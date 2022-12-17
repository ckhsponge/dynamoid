# frozen_string_literal: true

require 'bundler/gem_tasks'

require 'bundler/setup'
begin
  Bundler.setup(:default, :development)
rescue Bundler::BundlerError => e
  warn e.message
  warn 'Run `bundle install` to install missing gems'
  exit e.status_code
end
load './lib/dynamoid/tasks/database.rake' if defined?(Rails)

require 'rspec/core/rake_task'
RSpec::Core::RakeTask.new(:spec) do |spec|
  spec.pattern = FileList['spec/**/*_spec.rb']
end
desc 'alias test task to spec'
task test: :spec

ruby_version = Gem::Version.new(RUBY_VERSION)
minimum_version = ->(version, engine = 'ruby') { ruby_version >= Gem::Version.new(version) && RUBY_ENGINE == engine }
linting = minimum_version.call('2.7')

if linting
  begin
    require 'rubocop/rake_task'
    RuboCop::RakeTask.new do |task|
      task.options = ['-D'] # Display the name of the failing cops
    end
  rescue LoadError
    desc 'rubocop task stub'
    task :rubocop do
      warn 'RuboCop is unexpectedly disabled. Have you run bundle install?'
    end
  end
else
  desc 'rubocop task stub (too old ruby)'
  task :rubocop do
    warn "RuboCop is disabled for #{RUBY_ENGINE}-#{RUBY_VERSION}"
  end
end


require 'yard'
YARD::Rake::YardocTask.new do |t|
  t.files   = ['lib/**/*.rb', 'README.md', 'LICENSE.txt'] # optional
  t.options = ['-m', 'markdown'] # optional
end

desc 'Publish documentation to gh-pages'
task :publish do
  Rake::Task['yard'].invoke
  `git add .`
  `git commit -m 'Regenerated documentation'`
  `git checkout gh-pages`
  `git clean -fdx`
  `git checkout master -- doc`
  `cp -R doc/* .`
  `git rm -rf doc/`
  `git add .`
  `git commit -m 'Regenerated documentation'`
  `git pull`
  `git push`
  `git checkout master`
end

task default: %i[test rubocop]
