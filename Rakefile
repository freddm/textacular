require 'rubygems'

require 'rake'
require 'yaml'
require 'pg'
require 'active_record'
require 'benchmark'

require 'pry'

$LOAD_PATH.unshift File.expand_path(File.dirname(__FILE__) + '/spec')

task :default do
  Rake::Task["db:setup"].invoke
  Rake::Task["test"].invoke
end

file 'spec/config.yml' do |t|
  sh 'erb spec/config.yml.example > spec/config.yml'
end

desc 'Fire up an interactive terminal to play with'
task :console => :'db:connect' do
  Pry.start
end

task :test do
  require 'textacular_spec'
  require 'textacular/searchable_spec'
  require 'textacular/full_text_indexer_spec'
end

namespace :db do

  task :connect => 'spec/config.yml' do |t|
    ActiveRecord::Base.establish_connection \
      YAML.load_file 'spec/config.yml'
  end

  task :disconnect do
    ActiveRecord::Base.clear_all_connections!
  end

  desc 'Create the test database'
  task :create do
    sh 'createdb textacular'
  end

  desc 'Drop the test database'
  task :drop => :disconnect do
    sh 'dropdb textacular'
  end

  namespace :migrate do

    desc 'Run the test database migrations'
    task :up => :'db:connect' do
      ActiveRecord::Migrator.up 'db/migrate'
    end

    desc 'Reverse the test database migrations'
    task :down => :'db:connect' do
      ActiveRecord::Migrator.down 'db/migrate'
    end
  end
  task :migrate => :'migrate:up'

  desc 'Create and configure the test database'
  task :setup => [ :create, :migrate ]

  desc 'Drop the test tables and database'
  task :teardown => [ :'migrate:down', :drop ]
end
