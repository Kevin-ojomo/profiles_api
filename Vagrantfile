Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"  # Specify a valid Vagrant box
  config.vm.network "forwarded_port", guest: 8000, host: 8000
  config.vm.provision "shell", inline: <<-SHELL
    # Stop automatic updates to prevent lock issues
    sudo systemctl stop apt-daily.service
    sudo systemctl stop apt-daily.timer
    sudo systemctl stop unattended-upgrades.service

    # Wait for any dpkg or apt processes to finish
    while sudo fuser /var/lib/dpkg/lock-frontend /var/lib/dpkg/lock >/dev/null 2>&1; do
      echo "Waiting for dpkg lock..."
      sleep 5
    done

    # Update and install packages
    sudo apt-get update
    sudo apt-get install -y software-properties-common build-essential libssl-dev libffi-dev python3.13-venv zip

    # Ensure Python 3.13 is installed
    if ! command -v python3.13 &> /dev/null; then
      echo "Python 3.13 not found, installing..."
      sudo add-apt-repository ppa:deadsnakes/ppa -y
      sudo apt-get update
      sudo apt-get install -y python3.13 python3.13-venv python3.13-dev
    fi

    # Set Python alias
    touch /home/vagrant/.bash_aliases
    if ! grep -q PYTHON_ALIAS_ADDED /home/vagrant/.bash_aliases; then
      echo "# PYTHON_ALIAS_ADDED" >> /home/vagrant/.bash_aliases
      echo "alias python='python3.13'" >> /home/vagrant/.bash_aliases
    fi
  SHELL
end
