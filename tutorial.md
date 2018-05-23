# Datalad Tutorial

## Install Datalad

If you can - use a python virtual environment !  [See this link](https://docs.python.org/3/tutorial/venv.html)
Also, be bold - use the virtualenv wrapper [here](https://virtualenvwrapper.readthedocs.io/en/latest/)

This allows you to do thing like:

~$ mkvirtualenv --python=python3 datalad-tutorial
~$ which python # what is your python ?
~$ deactivate # quit the virtualenv
~$ which python # what is your python ?
~$ workon datalad-tutorial

Then

'''
A DataLad dataset is a Git repository that may or may not have a data annex that is used to manage data referenced in a dataset. In practice, most DataLad datasets will come with an annex.
'''

~$ mkdir try-datalad
~$ cd try-datalad
~$ datalad create demo
~$ cd demo
~$ ls -al 

'''
drwxr-xr-x 4 jb jb 4.0K May 22 23:02 .
drwxrwxr-x 3 jb jb 4.0K May 22 22:58 ..
drwxrwxr-x 2 jb jb 4.0K May 22 23:02 .datalad
drwxrwxr-x 9 jb jb 4.0K May 22 23:02 .git
-rw-rw-r-- 1 jb jb   21 May 22 23:02 .gitattributes
'''

In the .datalad directory : the config file has the uuid of the dataset, the .gitattribute is an annex thing telling which backend is used (default MD5E)

The creation of a subdataset :
~$ datalad create -d . sub1
~$ ls -al 

drwxrwxr-x 2 jb jb 4.0K May 22 23:02 .datalad
drwxrwxr-x 9 jb jb 4.0K May 22 23:40 .git
-rw-rw-r-- 1 jb jb   21 May 22 23:02 .gitattributes
-rw-rw-r-- 1 jb jb   46 May 22 23:40 .gitmodules
drwxr-xr-x 4 jb jb 4.0K May 22 23:40 sub1
(datalad-tutorial) jb-think:~/code/mni/conp/try-da

Here - we see a new .gitmodules which contains:

~$ cat .gitmodules
[submodule "sub1"]
	path = sub1
	url = ./sub1

basically the place where the new (sub)dataset is

~$ git submodules
git[master] $ git submodule 
 162e1d3b7050159d5e0d34c5b9ac2f9aeb474861 sub1 (heads/master)

## a little detour with gitannex

Ok, let's add a file to the top level dataset. 
~$ echo "a new file with loads of content" > a-new-file.big
~$ datalad add a-new-file.big
add(ok): /home/jb/code/mni/conp/try-datalad/demo/a-new-file.big (file)                                                          
save(ok): /home/jb/code/mni/conp/try-datalad/demo (dataset)
action summary:
  add (ok: 1)
  save (ok: 1)

What happened? 
~$ ll
a-new-file.big -> .git/annex/objects/Jq/w5/MD5E-s33--2877e87f718db6f48d2554d0dbdf60e2.big/MD5E-s33--2877e87f718db6f48d2554d0dbdf60e2.big

We now have a link, this one is not broken (yet ...) and guess what: 

~$ md5sum a-new-file.big

2877e87f718db6f48d2554d0dbdf60e2  a-new-file.big

Does this hash rings a bell ? a certain air of deja vu ? yes, this is the gitannex way: hashes are the key, the big files the objects, the filenames are links in the working tree.

(datalad-tutorial) jb-think:~/code/mni/conp/try-datalad/demo
git[master] $ git stat
On branch master
nothing to commit, working directory clean

Datalad has taken care of the commit. Thank you, datalad.

But wait, I see that I have now a branch "git-annex". What's there ? 
~$ git checkout git-annex
~$ cat eb5/dc6/MD5E-s33--2877e87f718db6f48d2554d0dbdf60e2.big.log 
1527047735.312195641s 1 67220ebc-5dbd-4609-8ff5-ea16120325a9

Interesting: this is the infamous hash, and the hash.log file contains information about that file (timestamp, is the file present in the repo (1) or not (0), and uuid of the repository. 










 
