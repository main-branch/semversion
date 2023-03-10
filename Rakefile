# frozen_string_literal: true

desc 'Run the same tasks that the CI build will run'
task default: %w[spec rubocop yard yard:audit yard:coverage bundle:audit build]

# Bundler Audit

require 'bundler/audit/task'
Bundler::Audit::Task.new

# Bundler Gem Build

require 'bundler'
require 'bundler/gem_tasks'

begin
  Bundler.setup(:default, :development)
rescue Bundler::BundlerError => e
  warn e.message
  warn 'Run `bundle install` to install missing gems'
  exit e.status_code
end

CLEAN << 'pkg'
CLEAN << 'Gemfile.lock'

# RSpec

require 'rspec/core/rake_task'

RSpec::Core::RakeTask.new

CLEAN << 'coverage'
CLEAN << '.rspec_status'
CLEAN << 'rspec-report.xml'

# Rubocop

require 'rubocop/rake_task'

RuboCop::RakeTask.new do |t|
  t.options = %w[
    --format progress
    --format json --out rubocop-report.json
  ]
end

CLEAN << 'rubocop-report.json'

# YARD

require 'yard'
YARD::Rake::YardocTask.new do |t|
  t.files = %w[lib/**/*.rb examples/**/*]
end

CLEAN << '.yardoc'
CLEAN << 'doc'

# Yardstick

desc 'Run yardstick to show missing YARD doc elements'
task :'yard:audit' do
  sh "yardstick 'lib/**/*.rb'"
end

# Yardstick coverage

require 'yardstick/rake/verify'

Yardstick::Rake::Verify.new(:'yard:coverage') do |verify|
  verify.threshold = 100
end

# task :'argtest', [:build_metadata, :pre_release_prefix] do |_t, args|
#   build_metadata = args[:build_metadata] || ENV['SEMVER_BUILD_METADATA']
#   pre_release_prefix = args[:pre_release_prefix] || ENV['PRE_RELEASE_PREFIX']
#   puts "build_metadata: #{build_metadata.inspect}"
#   puts "pre_release_prefix: #{pre_release_prefix.inspect}"
# end
