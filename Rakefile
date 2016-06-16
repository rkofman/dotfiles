require 'rake'
require 'erb'

desc "install the dot files into user's home directory"
task :install do
  replace_all = false
  Dir['*'].each do |file|
    next if %w[Rakefile README.rdoc LICENSE].include? file
    
    if File.exist?(target(file))
      puts "file: #{file}"
      if File.identical? file, target(file)
        puts "identical #{target(file)}"
      elsif replace_all
        replace_file(file)
      else
        print "overwrite #{target(file)}? [ynaq] "
        case $stdin.gets.chomp
        when 'a'
          replace_all = true
          replace_file(file)
        when 'y'
          replace_file(file)
        when 'q'
          exit
        else
          puts "skipping ~/.#{file.sub('.erb', '')}"
        end
      end
    else
      link_file(file)
    end
  end
end

def target(file)
  if file == "bin"
    # do not add a preceding '.' for the 'bin' folder.
    File.join(ENV['HOME'], "#{file.sub('.erb', '')}")
  else
    File.join(ENV['HOME'], ".#{file.sub('.erb', '')}")
  end
end

def replace_file(file)
  system %Q{rm "$HOME/.#{file.sub('.erb', '')}"}
  link_file(file)
end

def link_file(file)
  if file =~ /.erb$/
    puts "generating #{target(file)}"
    File.open(target(file), 'w') do |new_file|
      new_file.write ERB.new(File.read(file)).result(binding)
    end
  else
    puts "linking #{target(file)}"
    system %Q{ln -s "$PWD/#{file}" "#{target(file)}"}
  end
end
