# CK: Collective Knowledge

This blog post gives a brief introduction into CK and its basic concepts. There is a ton of existing documentation out there in the [CK wiki on GitHub](https://github.com/ctuning/ck/wiki). All of this documentation can easily feel overwhelming. This is why I wrote this deliberately short and lightweight introduction into some of the fundamental basic concepts of CK, which helped me a lot in understanding CK.

I assume that you have the CK tool installed on your machine, which you can easily check by running `ck version`. If this returns an error you want to install CK by running `pip install ck` [^1].

[^1]: If you have troubles installing CK this way you find more information [in the CK wiki](https://github.com/ctuning/ck/wiki/Getting-started-guide#Quick_CK_installation).

## So what is CK?
To put it quite generic, CK is a tool which helps organise and work with _stuff_ you care about.
_Stuff_ can be a lot of different things, such as research data, programs or scripts analysing this data, as well as the resulting data obtained by the analysis -- just to give a typical research workflow as an example.

CK helps you to organise this stuff by assigning unique identifiers (so called 'UIDs') to every _entry_ registered with ck. Entries are stored in _repositories_ which facilitate sharing. A special type of entries are _modules_ which implement the functionality of CK. CK comes with a set of built-in modules, but you can also write custom modules yourself.
     
_Entries_, _repositories_, and _modules_ are the basic _vocabulary_ of CK. Let's start talking more about them.

## CK Entries
CK tracks entities by assigning them unique identifiers. Each entry is stored in a separate directory and CK also stores additional metadata in form of a couple of JSON files for each entry. These file are stored in the `.cm` subdirectory of the entry. There are three metadata files:

 - `.cm/info.json` stores information like, who is the author or what is the license of the entry, etc.
 - `.cm/meta.json` stores arbitrary meta information about the entry, which is used by the CK modules to process this entry. One important example are _tags_ which are identifying words which can be used to filter out common entries.
 - `.cm/desc.json` is indented for a documentary description of the entry, but currently mostly empty.

## CK Repositories
In CK a repository is a collection of entries which are meant to be shared with other people. CK uses a tool called `git` which makes it incredible easy to share repositories among team members or make them publicly available. Websites such as GitHub or Bitbucket can be used to host CK repositories online.

Ck stores all of the repositories in one central folder. On linux and macOS this is by default: `$HOME/CK_REPOS`.

## CK Modules
Modules in CK group entries as well as actions to operate on these entries. CK entries which are operated on by a particular module are put in a directory which has the same name as the module. For example:

- Programs, which are compiled and run by the `program` module, are put in a directory called `program`.
- Datasets, which are extended by the `dataset` module, are put in a directory called `dataset`.
- Experiments, which are added, browsed, and rerun by the `experiment` module, are put in a directory called `experiment`.

This leads to a familiar directory structure where at the top-level directories are called after CK modules, e.g., `program`, `dataset`, and `experiment`. At the second-level directories store the actual programs, datasets, and experiments you care about, e.g., `program/my-awesome-program`, `dataset/my-awesome-dataset`, and `experiment/my-awesome-experiment`. These are themselves CK entries with their own metadata and UIDs.

_Actions_ in CK are functionalities offered by modules to operate on CK entries. Let's make a few concrete examples:

- The `program` module offers actions for `compile`ing and `run`ing programs.
- The `dataset` module offers an action for adding new files into an existing dataset (`add_file_to`).
- The `experiment` module offers actions for `add`ing new experiments, `browse` existing once, or `rerun` experiments.

Every command line in CK has the same basic form to perform an action of a particular module:
<pre>
ck <i>action</i> <i><b>module</b></i>
</pre>

Therefore, we write: `ck compile program`, `ck add_file_to dataset`, `ck rerun experiment`, and so on.

This style is deliberately designed so that the commands read like sentences. I call this <code>ck <i>action</i> <i><b>module</b></i></code> structure the _grammar_ of CK.

CK commands which talk about particular entries specify them by using the following notation:
<pre>
ck <i>action</i> <i><b>module</b></i>:<i><b>entry</b></i>
</pre>

Sometimes it is required to help CK distinguish between entries in different repositories. In these cases we have to write:
<pre>
ck <i>action</i> <i><b>repository</b></i>:<i><b>module</b></i>:<i><b>entry</b></i>
</pre>

Many modules allow to specify additional options as command line flags. You can get a full list of supported actions by calling on a particular module:
<pre>
ck help <i><b>module</b></i>
</pre>

## CK modules for managing repositories and modules

There exists CK modules for managing repositories and modules themselves. These are called `repo` and `module` and are briefly described here.

### `repo`
Repositories are a central concept in CK (as we have seen above) which are managed by the `repo` module.

Here are some things one can do with this module:

- `ck info repo` lists information about the `repo` module itself
- `ck help repo` lists all possible actions one can perform with a CK repository
- `ck list repo` lists all installed repositories

There are a number of things one can do with a _particular_ repository. We take the [`ck-autotuning`](https://github.com/ctuning/ck-autotuning) repository as an example:

- `ck pull repo:ck-autotuning` installs or updates the `ck-autotuning` repository to the latest version on the remote server (It is performing a `git pull` on the GitHub repository: https://github.com/ctuning/ck-autotuning)
- `ck info repo:ck-autotuning` lists information about the `ck-autotuning` repository
- `ck find repo:ck-autotuning` lists the path where the `ck-autotuning` repository is installed


### `module`
Modules are managed by a module called `module`.

Similarly to the actions on repositories one can:

- `ck info module` lists information about the `module` module itself
- `ck help module` lists all possible actions one can perform with a CK module
- `ck list module` list all installed modules, across all installed repositories

To list only the modules of a particular repository, for example `ck-autotuning` one can execute:
<pre>
ck list module --repo_uoa=ck-autotuning
</pre>

The `--repo_uoa=ck-autotuning` part is an _input argument_ passed to the `list` action of the module `module`. To list all the possible input arguments of an action call:
<code>ck <i>action</i> <i><b>module</b></i> --help</code>.
So for example: `ck list module --help`. This will print a description of the action and which input arguments it will process and what output it will return.


## Common CK actions
There are some actions which can be used on every module. These are called _common_ actions. You can list all common actions by running: `ck help`.

Furthermore, you can always call `ck action module --help` to get learn about the input arguments and return values of an action.

Many of the common actions are for managing ck entries, the most important of them are:

- <code>ck add <i><b>module</b></i>:<i><b>entry</b></i></code> adds a new ck entry called ***`entry`*** to the module named ***`module`***.
- <code>ck cp <i><b>module1</b></i>:<i><b>entry1</b></i> <i><b>module2</b></i>:<i><b>entry2</b></i></code> copies ck entry called ***`entry1`*** from ***`module1`*** into ***`entry2`*** in ***`module2`***.
- <code>ck find ***module***:***entry***</code> prints the path of the ck entry named ***`entry`*** from module ***`module`***.
- <code>ck mv <i><b>module1</b></i>:<i><b>entry1</b></i> <i><b>module2</b></i>:<i><b>entry2</b></i></code> moves ck entry called ***`entry1`*** from ***`module1`*** to ***`entry2`*** in ***`module2`***.
- <code>ck rm <i><b>module</b></i>:<i><b>entry</b></i></code> removes (deletes) an existing ck entry called ***`entry`*** from the module named ***`module`***.

## Where to go from here?
I only scratched the surface of CK. I haven't talked about the meta data format (which is `JSON`) and the implementation of your own custom modules (which is commonly done in Python).

As I said in the beginning, there is plenty of documentation available on the [CK wiki](https://github.com/ctuning/ck/wiki/). It is incredible useful to keep the _vocabulary_ (_entries_, _repositories_, _modules_) and the _grammar_ (<code>ck <i>action</i> <i><b>module</b></i></code>) of CK in mind while reading these documents and start playing around with CK.

The two most appropriate starting points are the [Getting Started Guide](https://github.com/ctuning/ck/wiki/Getting-started-guide) and the [Portable Workflows](https://github.com/ctuning/ck/wiki/Portable-workflows) page.

For seeing how to implement you own workflow with CK following an example, read the [Getting Started Guide](https://github.com/ctuning/ck/wiki/Getting-started-guide).

For learning how to implement portable workflows with CK, by

 - Describing and detecting existing software
 - Setting up software environment
 - Automating installation of a missing software
 - and more ...

read the corresponding sections in the [Portable Workflows](https://github.com/ctuning/ck/wiki/Portable-workflows) page.

Also, ask questions on the [CK mailing list](https://groups.google.com/forum/#!forum/collective-knowledge). The community is very much open to answer your questions!

