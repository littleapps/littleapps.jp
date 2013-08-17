require 'nanoc3/tasks'

desc 'Generate site from Travis CI and publish site to GitHub Pages'
task :travis do
  repo = %x(git config remote.origin.url).gsub(/^git@github.com:/, 'https://github.com/').gsub(/^git:/, 'https:')
  deploy_branch = 'gh-pages'
  if repo.match(/github\.com\.git$/)
    deploy_branch = 'master'
  end
  system <<-COMMAND
    cd public && rm -rf *
    git remote add https #{repo}
    git config user.name '#{ENV['GIT_NAME']}'
    git config user.email '#{ENV['GIT_EMAIL']}'
    git config credential.helper "store --file=.git/credentials"
    git config push.default matching
  COMMAND
  File.open('.git/credentials', 'w') do |f|
    f.write("https://#{ENV['GH_TOKEN']}:@github.com")
  end
  system "git branch #{deploy_branch} https/#{deploy_branch}"
  system 'cd ../'
  system "bundle exec nanoc compile && cd public && git add -A && git commit -m 'Update from travis-ci' && git push https #{deploy_branch} && cd ../"
  File.delete '.git/credentials'
end

task :default => [:travis]

