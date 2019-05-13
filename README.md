# Guide to Setup a Vagrant Environment for Ubuntu 18.04, Rails 5 and PostgreSQL

Feel free to clone this repository, use it or fork it.

I know much of this can be automated. A previous version had things like RVM installed automatically via the Vagrantfile, but then some of the dependencies broke.

This guide can be used in lieu of a "vagrant init" as it has no puppet dependencies.

Hat tip to this helpful guide:

https://gorails.com/setup/ubuntu/18.04

## Git Checkout this repo -- the Vagrantfile and README

```
git clone git@github.com:sbraford/vagrant-bionic64-rails5-postgresql.git
cd vagrant-bionic64-rails5-postgresql
```

## Start the Vagrant VM and Login to It

```bash
vagrant up
vagrant ssh
```


## Ruby, Git, Node, Yarn and Dependencies

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update
```


## Install package dependencies, etc:

```bash
sudo apt-get install -y git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev nodejs yarn libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
```

## Install RVM and Ruby 2.6.3

```bash
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
curl -sSL https://get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm install 2.6.3
rvm use 2.6.3 --default
ruby -v
echo "gem: --no-document" > ~/.gemrc
```

## Configure Git

```bash
git config --global color.ui true
git config --global user.name "Your Name"
git config --global user.email "name@email.com"
ssh-keygen -t rsa -b 4096 -C "name@email.com"
```

## Setup Git Auth

```bash
cat ~/.ssh/id_rsa.pub
````

*Paste the public key into Github's SSH key settings.*

See if you can authenticate:

```bash
ssh -T git@github.com
```

If you see, *"Hi your-github-username! You've successfully authenticated, but GitHub does not provide shell access."* then you're good to go re: github.

## Install Rails and Bundler

```bash
gem install rails -v 5.2.3
rails -v
gem install bundler
```

## Setting up Postgresql 9.5

```bash
sudo sh -c "echo 'deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main' > /etc/apt/sources.list.d/pgdg.list"
wget --quiet -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y postgresql-common
sudo apt-get install -y postgresql-9.5 libpq-dev
ps aux | grep postgres
```

This probably isn't necessary (it should start automatically), but the command to start PostgreSQL is:

```bash
/usr/lib/postgresql/9.5/bin/pg_ctl -D /var/lib/postgresql/9.5/main -l logfile start
```

### Create a vagrant User for PostgreSQL

```bash
sudo su - postgres
vi /etc/postgresql/9.5/main/pg_hba.conf
# search for peer, change "local all postgres peer" to "local all postgres md5" or on local vagrant "local all all trust"

createuser vagrant -s --pwprompt
# You can just leave the password blank unless you're concerned about security.

exit
```

Restart PostgreSQL for the changes to take effect:

```bash
sudo service postgresql restart
```

```bash
sudo -u postgres psql
postgres=# ALTER USER vagrant CREATEDB LOGIN;
```

## Ready to Rock n Roll

You should now be ready to create your rails app or check an existing one out from GitHub.
