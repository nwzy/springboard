# How to access Git LFS files
Nicolas Wong (Feb 21)

March 1st 2021

### NOTE: This is likely being phased out in the very near future and is here for archival/academic purposes 
Other notes
* This **only** works on a previous file (`cast.csv`) on a specific commit (`70d78f4508b71e84d282555c91d9b778bfb05027`)
* **All other files are broken**
* Source: [Github LFS repo instruction](https://github.com/git-lfs/git-lfs/wiki/Installation)

## 1. Open a new `Terminal` within JupyterLab in your Paperspace container

## 2. Execute `curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash`

* This will run the script automatically which updates `apt-get` with the correct information to allow downloading `git lfs` onto the Paperspace Debian

Output:
```
# curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash
Detected operating system as Ubuntu/xenial.
Checking for curl...
Detected curl...
Checking for gpg...
Detected gpg...
Running apt-get update... done.
Installing apt-transport-https... done.
Installing /etc/apt/sources.list.d/github_git-lfs.list...done.
Importing packagecloud gpg key... done.
Running apt-get update... done.

The repository is setup! You can now install packages.
```

## 3. Run `apt-get install git-lfs`
* Installs `git lfs`

Output:
```
# apt-get install git-lfs
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  git-lfs
0 upgraded, 1 newly installed, 0 to remove and 29 not upgraded.
Need to get 6540 kB of archives.
After this operation, 15.3 MB of additional disk space will be used.
Get:1 https://packagecloud.io/github/git-lfs/ubuntu xenial/main amd64 git-lfs amd64 2.13.2 [6540 kB]
Fetched 6540 kB in 0s (7266 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package git-lfs.
(Reading database ... 18610 files and directories currently installed.)
Preparing to unpack .../git-lfs_2.13.2_amd64.deb ...
Unpacking git-lfs (2.13.2) ...
Setting up git-lfs (2.13.2) ...
Git LFS initialized.
```

## 4. Pull the commits with the files storing the `git lfs` info
* `git lfs` works in the background and should automagically download the complete file based on the information within files using the LFS system

Output:
```
# git checkout 70d78f4508b71e84d282555c91d9b778bfb05027
Downloading mec-5.3.10-data-wranging-with-pandas-mini-project/cast.csv (227 MB)
Note: checking out '70d78f4508b71e84d282555c91d9b778bfb05027'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 70d78f4... Update data files in project (#3)
```

# Question: Why do the other files still only have a few lines and don't have the real information?
# Answer: The `oid` is likely broken

Once you successfully install `git lfs`, you can start executing LFS specific commands. 
It _knows_ which files are LFS files, which can be seen by running `git lfs ls-files`

```
# git lfs ls-files
841d85a7a4 - mec-12.4.2-logistic-regression-mini-project/data/01_heights_weights_genders.csv
5c45835db2 - mec-13.5.1-tree-based-algorithms-mini-project/GermanCredit.csv
a4f1ba5c26 - mec-17.4.1-recommendation-systems-mini-project/data/combined_data.csv
5c1b39b39e - mec-17.4.1-recommendation-systems-mini-project/data/movie_titles.csv
37ec322786 - mec-17.4.1-recommendation-systems-mini-project/data/movies_metadata.csv
21ac5471fa - mec-18.5.1-time-series-analysis-mini-project/IBM_2006-01-01_to_2018-01-01.csv
```

However, when actually trying to `git lfs pull` the files, the object-id's (`oid`) are broken

```
# git lfs pull
[a4f1ba5c26b6bb6522ab3a85125d8f41be1364f10125e11ccc832081abf916f5] Object does not exist on the server: [404] Object does not exist on the server
[37ec322786b136a24604564e55160be75f8937546f11cb1a990076971fa895fd] Object does not exist on the server: [404] Object does not exist on the server
[5c1b39b39ed1f5b58da872414a49b77e921fa8a069f15b3d36f78a0705e6a153] Object does not exist on the server: [404] Object does not exist on the server
[841d85a7a484633517d5d1060b77e6d994a6f0495c92dec6da0520cbf1fc8852] Object does not exist on the server: [404] Object does not exist on the server
[21ac5471fa68f438dbfc8bf2a9588816bbbdc6cc92e9f119b453459b54103219] Object does not exist on the server: [404] Object does not exist on the server
[5c45835db2cb78458ef06a86845b78fae0225797a2e8e935d82b5f6767e0f387] Object does not exist on the server: [404] Object does not exist on the server
error: failed to fetch some objects from 'https://github.com/springboard-curriculum/mec-mini-projects.git/info/lfs'
```


```python

```
