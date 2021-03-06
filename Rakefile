require 'yaml'

REPO = 'botanicus/docker-project-manager'
VERSION = YAML.load_file('shard.yml')['version']

namespace :docker do
  desc "Build the image"
  task :build, :tag do |_, args|
    tag = args[:tag] ? "#{REPO}:#{args[:tag]}" : REPO
    sh <<-EOF
      docker build --build-arg BUILD_DATE=`date -u +"%Y-%m-%dT%H:%M:%SZ"` \
                   --build-arg VCS_REF=`git rev-parse --short HEAD` \
                   --build-arg VERSION=#{VERSION} . -t #{tag}
    EOF
  end

  # The image is updated from Travis CI, this is just ad-hoc.
  desc "Push the image to DockerHub"
  task :push do
    sh "docker push #{REPO}"
  end

  desc "Run SH in the image"
  task :sh do
    sh "docker run -it --rm -v $PWD/tmp:/projects --entrypoint /bin/sh #{REPO}"
  end

  namespace :test do
    desc "Manually test the init command"
    task :init do
      sh "rm -rf tmp &> /dev/null; mkdir tmp"
      sh "docker run -it --rm -v $PWD/tmp:/projects botanicus/docker-project-manager init my-blog"
    end
  end
end

namespace :test do
  desc "Manually test the init command"
  task :init do
    # NOTE that we cannot change the current directory with crystal run, it won't find the shards.
    sh "rm -rf my-blog"
    sh "crystal run src/docker-project-manager.cr -- init my-blog"
  end
end

desc "Run the tests"
task test: 'docker:build' do
  sh "bundle exec rspec"
end

desc "Format the code based on the Crystal conventions"
task :format do
  sh "crystal tool format"
end
