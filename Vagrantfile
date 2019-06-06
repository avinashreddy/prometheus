Vagrant.configure("2") do |config|
  
 # Use the same key for each machine  config.ssh.insert_key = false
 config.ssh.insert_key = false
 config.vbguest.auto_update = false
 config.vm.define "prometheus" do |cfg|
  cfg.vm.provider :virtualbox do |vb|
        vb.name = "prometheus"
        vb.memory = 2048
        vb.cpus = "4"
  end
  cfg.vm.hostname = "prometheus"
  cfg.vm.box = "ubuntu/xenial64"
  
  cfg.vm.network "private_network", ip: "10.200.1.10"
  
 cfg.vm.provision "shell", inline: <<-SHELL 
    apt-get update
    apt-get install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
    apt-get update
    apt-cache policy docker-ce
    apt-get install -y docker-ce
    usermod -aG docker vagrant
    
    mkdir tmp 
    
    #### Alertmanager
    useradd --no-create-home --shell /bin/false alertmanager
    sudo mkdir /etc/alertmanager
    pushd tmp
    wget https://github.com/prometheus/alertmanager/releases/download/v0.17.0/alertmanager-0.17.0.linux-amd64.tar.gz
    tar -xvf alertmanager-0.17.0.linux-amd64.tar.gz
    pushd alertmanager-0.17.0.linux-amd64 
    mv alertmanager /usr/local/bin/
    mv amtool /usr/local/bin/
    chown alertmanager:alertmanager /usr/local/bin/alertmanager
    
        
    chown alertmanager:alertmanager /usr/local/bin/amtool
    mv alertmanager.yml /etc/alertmanager/
    chown -R alertmanager:alertmanager /etc/alertmanager/
    cp /vagrant/alertmanager.service /etc/systemd/system/alertmanager.service
    systemctl daemon-reload
    systemctl start alertmanager 
    systemctl enable alertmanager
    popd
    popd
    
    ### Grafana
    sudo apt-get install -y libfontconfig
    pushd tmp
    wget https://dl.grafana.com/oss/release/grafana_6.2.1_amd64.deb 
    sudo dpkg -i grafana_6.2.1_amd64.deb 
    sudo systemctl enable --now grafana-server
    popd 
    
    
    ####Node Exporter
    useradd --no-create-home --shell /bin/false node_exporter
    pushd tmp    
    wget https://github.com/prometheus/node_exporter/releases/download/v0.18.0/node_exporter-0.18.0.linux-amd64.tar.gz
    tar -xvf node_exporter-0.18.0.linux-amd64.tar.gz
    pushd node_exporter-0.18.0.linux-amd64
    mv node_exporter /usr/local/bin/
    chown node_exporter:node_exporter /usr/local/bin/node_exporter
    cp /vagrant/node_exporter.service /etc/systemd/system/node_exporter.service
    systemctl daemon-reload
    systemctl start node_exporter
    systemctl enable node_exporter
    popd
    popd
    
    #### Prometheus
    useradd --no-create-home --shell /bin/false prometheus
    mkdir /etc/prometheus/ /var/lib/prometheus/
    chown prometheus:prometheus /var/lib/prometheus/    
    pushd tmp
    #Make sure to get the latest version. 
    wget https://github.com/prometheus/prometheus/releases/download/v2.10.0/prometheus-2.10.0.linux-amd64.tar.gz
    gunzip prometheus-2.10.0.linux-amd64.tar.gz
    tar -xvf prometheus-2.10.0.linux-amd64.tar
    pushd prometheus-2.10.0.linux-amd64
    mv console* /etc/prometheus/
    cp /vagrant/prometheus.yml /etc/prometheus/
    chown -R prometheus:prometheus /etc/prometheus/
    mv prometheus /usr/local/bin
    mv promtool /usr/local/bin
    chown prometheus:prometheus /usr/local/bin/prometheus
    chown prometheus:prometheus /usr/local/bin/promtool
    cp /vagrant/prometheus.service /etc/systemd/system/prometheus.service
    systemctl daemon-reload
    systemctl start prometheus 
    systemctl enable prometheus
    popd
    popd
    
    
  SHELL
 end
end
