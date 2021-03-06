require 'rake'
require 'coveralls/rake/task'
require 'rainbow'
require 'rubocop/rake_task'

require_relative 'rakefiles/reporting_tasks'
require_relative 'rakefiles/testing_tasks'

Rainbow.enabled = true

# rubocop:disable Metrics/BlockLength
namespace 'cuke_linter' do

  desc 'Check documentation with RDoc'
  task :check_documentation do
    output = `rdoc lib -C`
    puts output

    raise Rainbow('Parts of the gem are undocumented').red unless output =~ /100.00% documented/

    puts Rainbow('All code documented').green
  end

  # Creates coveralls:push task
  Coveralls::RakeTask.new

  desc 'The task that CI will run. Do not run locally.'
  task :ci_build => ['cuke_linter:test_everything', 'coveralls:push'] # rubocop:disable Style/HashSyntax

  desc 'Check that things look good before trying to release'
  task :prerelease_check do
    begin
      Rake::Task['cuke_linter:test_everything'].invoke
      Rake::Task['cuke_linter:check_documentation'].invoke
    rescue => e
      puts Rainbow("-----------------------\nSomething isn't right!").red
      raise e
    end

    puts Rainbow("-----------------------\nAll is well. :)").green
  end

  desc 'Generate a Rubocop report for the project'
  RuboCop::RakeTask.new(:rubocop) do |task|
    task.patterns   = ['./']
    task.formatters = ['fuubar', ['html', '--out', 'rubocop.html']]
    task.options    = ['-S']
  end

end
# rubocop:enable Metrics/BlockLength


task :default => 'cuke_linter:test_everything' # rubocop:disable Style/HashSyntax
