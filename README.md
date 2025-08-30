# deploy_new_server

## Install miniconda
``` bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```


After installing, close and reopen your terminal application or refresh it by running the following command:
``` bash
source ~/miniconda3/bin/activate
```
Then, initialize conda on all available shells by running the following command:
``` bash
conda init --all
```
Using conda init modifies some of your shell configuration files, such as .bash_profile or .zshrc. To test which files conda init is going to modify on your system, run the command with the --dry-run flag.
``` bash
conda init --all --dry-run
```
Including --dry-run prevents conda from making any actual file updates.