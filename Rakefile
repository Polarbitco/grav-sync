require "yaml"

conf = YAML.load_file(".grav-sync.yml")

task default: %w[sync]

task :sync do
  puts "Creating remote backup..."
  move_to_address = ' "cd ' + conf["remote"]["path"]
  create_backup = ' && bin/grav backup"'
  ssh = "ssh -t " + conf["ssh"]["username"] + "@" + conf["ssh"]["host"] +
    " -p " + conf["ssh"]["port"] + move_to_address + create_backup
  sh(ssh)

  # Remove existing backups
  puts "Remove existing backups..."
  sh("rm -rf backup")
  sh("rm -rf user")
  sh("rm -rf images")
  sh("rm -rf cache")

  # Download backup
  puts "Downloading backup..."
  scp = "scp -P 4000 " + conf["ssh"]["username"] + "@" + conf["ssh"]["host"] +
    ":" + conf["remote"]["path"] + "/backup/* ."
  sh(scp)

  # Remove backup
  puts "Removing backup..."
  move_to_backup = ' "cd ' + conf["remote"]["path"] + '/backup'
  remove_backup = ' && rm *"'
  ssh = "ssh -t " + conf["ssh"]["username"] + "@" + conf["ssh"]["host"] +
    " -p " + conf["ssh"]["port"] + move_to_backup + remove_backup
  sh(ssh)

  # Extracting backup
  puts "Extracting backup..."
  sh("mkdir backup")
  sh("find . -depth -name '*.zip' -exec unzip -d backup '{}' ';'")
  sh("find . -depth -name '*.zip' -exec rm -rf '{}' ';'")

  # Copy over user data
  puts "Copying remote data..."
  sh("mkdir user")
  sh("cd backup && cp -rf user ../ && cp -rf images ../ && cp -rf cache ../")

  # Remove the backup
  puts "Removing backup..."
  sh("find . -depth -name 'backup' -exec rm -rf '{}' ';'")

  # Migration completed
  puts "Migration completed!"
end
