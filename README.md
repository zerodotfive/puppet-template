***NOTE: If creating puppet for new project, download this as template, push to new git repo, then uncomment `Vagrantfile` in .gitignore.***

- Install PyCharm/RubyMine https://www.jetbrains.com/products.html
- Install PyCharm/RubyMine puppet plugin.
- Install plugin dependencies
```
gem install puppet librarian-puppet
```
- Update modules from Puppetfile context menu or with librarian-puppet cli
```
librarian-puppet update
librarian-puppet install
```
- Install Vagrant https://www.vagrantup.com/downloads.html
- Install Vegrant plugins
```
vagrant plugin install vagrant-hostmanager vagrant-vbguest
```
- Change node hostname in Vagrantfile to name of node you want to test, then run
```
vagrant up
vagrant rsync
vagrant ssh node -c 'sudo puppet agent -t --server master.local'
vagrant ssh master -c 'sudo puppet cert sign --all'
vagrant ssh node -c 'sudo puppet agent -t --server master.local'
```
