require 'rake'
require 'yaml'

SCRIPTS_WITH_RAKE = %w(Command-T)
FOLDERS = %w(colors ftdetect ftplugin indent syntax doc plugin autoload snippets macros after ruby)
SCRIPTS = %w(personal tabular nerdtree vim-cucumber vim-rails vim-fugitive vim-haml ack.vim snipmate.vim vim-spec tcomment_vim vim-bufonly vim-endwise vim-surround vim-yankring vim-fuzzyfinder supertab rvm.vim vim-unimpaired Slimv vimclojure vim-rake vim-javascript vim-autoclose vim-ruby-refactoring matchit vim-l9 cscope) + SCRIPTS_WITH_RAKE
DOTVIM = "#{ENV['HOME']}/.vim"

desc "Get latest on all plugins"
task :preinstall do
  FileUtils.mkdir_p('plugins')
  in_directory('plugins') do
    SCRIPTS.each do |f|
      if File.directory?(f)
        in_directory(f) { update_current_dir }
      elsif script_urls[f]
        clone_project(f, script_urls[f])
      end
    end
  end
end

def clone_project(name, script_url_yaml)
  system("git clone #{script_urls[name]['git']} #{name}") if script_url_yaml['git']
  system("hg clone #{script_urls[name]['hg']} #{name}") if script_url_yaml['hg']
end

def update_current_dir
  system('git pull origin master') if File.directory?('.git')
  system('hg pull && hg update') if File.directory?('.hg')
end

def in_directory(directory)
  original_dir = Dir.pwd
  Dir.chdir directory
  yield
  Dir.chdir original_dir
end

def script_urls
  yaml_file = File.open(File.join(File.dirname(__FILE__), 'plugins.yml'))
  yaml = YAML.load(yaml_file)
  @scripts ||= yaml['plugins']
end

desc "Install the files into ~/.vim"
task :install do
  FileUtils.mkdir_p FOLDERS.map{|f| "#{DOTVIM}/#{f}" }
  FileUtils.mkdir_p "#{DOTVIM}/tmp"

  in_directory('plugins') do
    SCRIPTS_WITH_RAKE.each do |s|
      if !File.directory?(s)
        puts "#{s} doesn't exist. Please run 'rake preinstall'"
      else
        puts "making #{s}"
        in_directory(s) { system 'rake make' }
      end
    end
  end
  in_directory('plugins') do
    SCRIPTS.each do |s|
      if !File.directory?(s)
        puts "#{s} doesn't exist. Please run 'rake preinstall'"
      else
        puts "installing #{s}"
        FOLDERS.each do |f|
          in_directory(s) { FileUtils.cp_r Dir["#{f}/*"], "#{DOTVIM}/#{f}" }
        end
      end
    end
  end
  FileUtils.cp "dotvimrc", "#{ENV['HOME']}/.vimrc"
  FileUtils.cp "dotgvimrc", "#{ENV['HOME']}/.gvimrc"
end

task :default => :install

desc "Remove everything in ~/.vim"
task :uninstall do
  FileUtils.rm_rf DOTVIM
end

desc "Blow everything out and try again."
task :reinstall => [:uninstall, :install]

desc "Get latest on all plugins"
task :update => :preinstall
