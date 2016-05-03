require 'fileutils'

remote_name = ENV.fetch("REMOTE_NAME", "origin")
branch_name = ENV.fetch("BRANCH_NAME", "master")

PROJECT_ROOT = `git rev-parse --show-toplevel`.strip
BUILD_DIR    = File.join(PROJECT_ROOT, "build")
GH_PAGES_REF = File.join(BUILD_DIR, ".git/refs/remotes/#{remote_name}/#{branch_name}")

directory BUILD_DIR

file GH_PAGES_REF => BUILD_DIR do
  repo_url = nil

  cd PROJECT_ROOT do
    repo_url = `git config --get remote.#{remote_name}.url`.strip
  end

  cd BUILD_DIR do
    sh "git init"
    sh "git remote add #{remote_name} #{repo_url}"
    sh "git fetch #{remote_name}"

    if `git branch -r` =~ /#{branch_name}/
      sh "git checkout #{branch_name}"
    else
      sh "git checkout --orphan #{branch_name}"
      sh "touch index.html"
      sh "git add ."
      sh "git commit -m 'initial #{branch_name} commit'"
      sh "git push #{remote_name} #{branch_name}"
    end
  end
end

desc "Prepare for build"
task :prepare => GH_PAGES_REF

desc "Watch"
task :watch do
  sh "bundle exec jekyll serve --watch"
end

desc "Build static files"
task :build do
  cd PROJECT_ROOT do
    if File.exist?('_config_prod.yml')
      sh "bundle exec jekyll build --destination #{BUILD_DIR} --config _config.yml,_config_prod.yml"
    else
      sh "bundle exec jekyll build --destination #{BUILD_DIR}"
    end
  end
end

desc "Deploy static files to gh-pages branch"
task :deploy => [:build] do
  message = nil
  suffix = ENV["COMMIT_MESSAGE_SUFFIX"]

  cd PROJECT_ROOT do
    head = `git log --pretty="%h" -n1`.strip
    message = ["Site updated to #{head}", suffix].compact.join("\n\n")
  end

  cd BUILD_DIR do
    sh 'git add --all'
    if /nothing to commit/ =~ `git status`
      puts "No changes to commit."
    else
      sh "git commit -m \"#{message}\""
    end
    sh "git push #{remote_name} #{branch_name}"
  end
end
