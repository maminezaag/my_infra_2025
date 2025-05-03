Vagrant.configure("2") do |config|
  # Configuration commune pour toutes les VMs
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "890"
    vb.cpus = 1
  end

  # Configuration SSH commune pour toutes les VMs
  config.vm.provision "shell", inline: <<-SHELL
    # Définir le mot de passe root
    echo "root:loploplop" | chpasswd
    
    # Activer l'authentification par mot de passe pour SSH
    sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
    
    # Permettre la connexion root via SSH (pour workshop uniquement)
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
    
    # Redémarrer le service SSH pour appliquer les changements
    systemctl restart sshd
    
    
  SHELL
  
  
 
  
  
  

  # VM principale
  config.vm.define "dottn", primary: true do |dottn|
    dottn.vm.box = "almalinux/8"
    # Configuration réseau NAT (par défaut) et réseau privé
    dottn.vm.network "private_network", ip: "192.168.56.60"
    # Ajout d'une interface NAT supplémentaire si nécessaire
    # dottn.vm.network "nat", auto_config: true
    dottn.vm.hostname = "dottnint"
    dottn.vm.provider "virtualbox" do |vb|
      vb.name = "dottnVMname"
    end
  end

  # Premier clone
  config.vm.define "dot1" do |dot1|
    dot1.vm.box = "almalinux/8"
    dot1.vm.network "private_network", ip: "192.168.56.61"
    dot1.vm.hostname = "dot1"
    dot1.vm.provider "virtualbox" do |vb|
      vb.name = "dot1VMname"
      vb.linked_clone = true
      #vb.clone_from_machine = "dottnVMname"
	  vb.linked_clone = true
    end
  end

  # Deuxième clone
  config.vm.define "dot2" do |dot2|
    dot2.vm.box = "almalinux/8"
    dot2.vm.network "private_network", ip: "192.168.56.62"
    dot2.vm.hostname = "dot2"
    dot2.vm.provider "virtualbox" do |vb|
      vb.name = "dot2VMname"
      vb.linked_clone = true
      #vb.clone_from_machine = "dottnVMname"
	  vb.linked_clone = true
    end
	 # Provision spécifique pour dot2
  dot2.vm.provision "shell", inline: <<-SHELL
    # Installer PostgreSQL
    dnf install -y postgresql-server postgresql-contrib

    # Initialiser la base de données PostgreSQL
    postgresql-setup --initdb

    # Démarrer PostgreSQL et l'activer au démarrage
    systemctl start postgresql
    systemctl enable postgresql

    # Modifier le fichier de configuration pour ajouter "hello world"
    echo "# hello world" >> /var/lib/pgsql/data/postgresql.conf

    # Redémarrer PostgreSQL pour appliquer les changements
    systemctl restart postgresql
  SHELL
  end
end
