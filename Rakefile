require 'rake'
require 'rake/gempackagetask'
require 'rake/rdoctask'
require 'rake/testtask'
Dir[File.dirname(__FILE__) + "/tasks/*.rake"].each do |task|
  load task
end

require 'lib/retflix'

NAME = "retflix"
VERS = ENV['VERSION'] || Retflix::VERSION
PKG = "#{NAME}-#{VERS}"

RDOC_OPTS = [
  '--quiet',
  '--title', 'The Retflix Reader',
  '--main', 'README',
  '--inline-source'
]

README_FILES = FileList.new('*') do |list|
  list.exclude(/(^|[^.a-z])[a-z]+/)
  list.exclude('TODO')
end.to_a

PKG_FILES = FileList[
  'lib/**/*',
  'tasks/**/*',
  'test/**/*',
  'Rakefile',
  'init.rb'
].to_a + README_FILES

GEMSPEC = Gem::Specification.new do |s|
  s.name = NAME
  s.version = VERS
  s.platform = Gem::Platform::RUBY
  s.has_rdoc = true
  s.rdoc_options += RDOC_OPTS
  s.extra_rdoc_files = README_FILES
  s.summary = "A library for accessing the Netflix REST API in Ruby."
  s.description = s.summary
  s.author = "Kyle Wilkinson"
  s.email = "kai@wikyd.org"
  s.homepage = "http://github.com/wikyd/retflix"
  s.rubyforge_project = nil # REVISIT: Need to set this up.
  s.files = PKG_FILES
  s.require_paths = ["lib"]
  s.test_files = FileList['test/**/*_.rb'].to_a
  
  s.add_dependency 'actionpack', '>= 2.2.2' # OAuth requires this version, but does not specify it correctly
  s.add_dependency 'oauth', '>= 0.3.6'
  s.add_dependency 'nokogiri', '>= 1.4.0'
end

desc "Runs all the tests."
Rake::TestTask.new do |t|
  t.libs << "test"
  t.test_files = FileList['test/**/*_test.rb']
  t.verbose = true
end

desc "Generates all the rdoc docs."
Rake::RDocTask.new do |rdoc|
  rdoc.rdoc_dir = 'doc/rdoc'
  rdoc.options += RDOC_OPTS
  rdoc.main = "README"
  rdoc.rdoc_files.add ['README', 'CHANGELOG', 'COPYING', 'lib/**/*.rb']
end

desc "Generates the gem package."
Rake::GemPackageTask.new(GEMSPEC) do |p|
  p.need_tar = true
  p.gem_spec = GEMSPEC
end

desc "Releases a new Retflix package to RubyForge."
task :release => [:clean, :package] do
  raise "TODO: RubyForge release not yet implemented."
end

desc "Builds the package and installs it."
task :install => [:clean, :package] do
  sudo = RUBY_PLATFORM =~ /win32/ ? '' : 'sudo'
  sh %{#{sudo} gem install --no-ri pkg/#{NAME}-#{VERS}}
end

desc "Uninstalls the package."
task :uninstall do
  sudo = RUBY_PLATFORM =~ /win32/ ? '' : 'sudo'
  sh %{sudo gem uninstall #{NAME}}
end

desc "Cleans generated files."
task :clean => [:clobber_package, :clobber_rdoc] do
  Dir[File.dirname(__FILE__) + "/**/*~"].each do |f|
    sh %{rm -f #{f}}
  end
  sh %{rm -rf doc}
end

