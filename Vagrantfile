# -*- mode: ruby -*-
# vi: set ft=ruby :
# tested with BAP 0.9.1

$cpus = 4
$memory = 6 * 1024

$script = <<ENDSCRIPT

# make apt-get slightly happier
export DEBIAN_FRONTEND=noninteractive

# root
sudo -Es <<ENDSUDO

# let Trusty use latest OCaml and opam
apt-get install -y software-properties-common
add-apt-repository -sy ppa:avsm/ocaml42+opam12
apt-get update
apt-get install -y camlp4-extra ocaml ocaml-native-compilers opam

# install BAP dependencies at the OS level
apt-get install -y clang libgmp-dev llvm m4 time

ENDSUDO

# vagrant
sudo -u vagrant -Es <<ENDVAGRANT

# set up opam and bashrc
export HOME=/home/vagrant
opam init -a
sed -ri 's/^(jobs:).*/\\1 #{$cpus}/' ~/.opam/config
sed -ri 's/^(download-jobs:).*/\\1 6/' ~/.opam/config
echo 'export OCAMLPARAM="_,annot=0,bin-annot=1,g=1,short-paths=1"' >> ~/.bashrc
echo 'export OPAMKEEPBUILDDIR="true"' >> ~/.bashrc

# open a new stack
time opam switch install bap-stack -A 4.02.1+PIC
source ~/.bashrc

# yay!
time opam install -b -y bap utop

ENDVAGRANT

ENDSCRIPT

Vagrant.configure('2') do |config|
  # can also use ubuntu/trusty64 (if you don't care about docker)
  config.vm.box = 'phusion/ubuntu-14.04-amd64'
  config.vm.provider 'virtualbox' do |vb|
    vb.cpus = $cpus
    vb.memory = $memory
  end
  config.vm.provision 'shell', inline: $script
end
