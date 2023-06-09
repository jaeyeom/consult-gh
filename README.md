<a href="https://github.com/armindarvish"><img alt="Armin Darvish" src="https://img.shields.io/static/v1?label=Armin%20Darvish&message=consult-gh&color=00A8B0&logo=github"/></a>

<a href="https://www.gnu.org/software/emacs/"><img alt="GNU Emacs" src="https://img.shields.io/static/v1?label=Made%20For&message=GNU%20Emacs&color=7a5aba&logo=gnuemacs&logoColor=white"/></a>


# The  [GitHub CLI](https://cli.github.com/) integration for GNU Emacs using [Consult](https://github.com/minad/consult).

Consult-GH provides an interface to interact with github repositories (search, view files and issues, clone, fork, &#x2026;) from inside emacs. It uses the awesome package [consult](https://github.com/minad/consult) by [Daniel Mendler](https://github.com/minad) and [GitHub CLI](https://cli.github.com/) and optionally [Embark](https://github.com/oantolin/embark) by [Omar Antolín Camarena](https://github.com/oantolin), and provides an intuitive UI using minibuffer completion familiar to emacs users.

Consult-GH offers the following "MAIN" interactive commands

1.  `consult-gh-orgs`: for looking up github users/organizations including and loading their repositories including looking up multiple accounts at the same time.
2.  `consult-gh-search-repos`: for looking up repositories including multiple repositories at the same time.
3.  `consult-gh-browse-repo-files`: for looking up files within any branch of one or more repositories. This can include looking at different branches within the same repository.
4.  `consult-gh-issue-list`: for looking up issues of one or more repositories

In addition Consult-GH provides interactive functions for "ADDITIONAL" useful quick actions:

1.  `consult-gh-default-repos`: This calls consult-gh-org on a defined default list of orgs stored in variable `consult-gh-default-orgs-list` for quick access to frequently used repositories. This can for example be useful for accessing your own repositories quickly.
2.  `consult-gh-repo-clone`: Quickly clone a repository with just typing the name of the repository. You can also clone multiple repositories at the same time (see examples below).
3.  `consult-gh-repo-fork`: Quickly fork one or more repositories.

Furthermore Consult-GH, also provides a bunch of useful [Embark](https://github.com/oantolin/embark) actions (see examples below.) You can see a full list of commands here:


# Why Use Consult-GH?

While there are several packages for interacting with Github API such as [gh.el](https://github.com/sigma/gh.el), [magit/ghub](https://github.com/magit/ghub) and [magit/forge](https://github.com/magit/forge), [git-link](https://github.com/sshaw/git-link), [browse-at-remote](https://github.com/rmuslimov/browse-at-remote), and &#x2026; in my opinion none of these packages provide an intuitive UI to interact with github repositories. Some of these are a collection of low-level API calls without user-friendly interactive commands and others simply retrieve a URL in the browser instead of providing github interface inside emacs. As a result the user either has to implement their own functions to use the api calls or simply jump to the browser to interact with GitHub. Consult-GH aims to fill this gap and provide a tool that allows the user to interact with GitHub from within emacs.

Note that currently Consult-GH does not provide interactive commands to manage issues or pull request mainly because those functionalities are already available in other packages. Personally, I like to use Consult-GH for searching repositories, browsing issues or view/download files without cloning entire repositories, etc. and use a package like [magit/forge](https://github.com/magit/forge) for managing issues, pull requests, etc.


# Getting Started

-   Installation

    -   Requirements
    
        In order to use Consult-GH, you need the following requirements:
        
        -   [GitHub CLI](https://github.com/cli/cli)
        
            To install GitHub Cli, follow the official documentations here: [GitHub CLI Installation](https://github.com/cli/cli#installation).
            
            **Why use gh instead of other emacs packages?** While there are other emacs packages to interact with github api, we use "gh" commandline tool as the backend instead of direct calls to REST API or GraphQL inside emacs and this is very much intentional. By leaving api functionalities we simplify usage (no need to setup authentication within emacs), reduce security risks (no risk of exposing authentication tokens, &#x2026;) and increase maintainability (no need to keep compatibility with API changes).
        
        -   [Consult](https://github.com/minad/consult)
        
            To install consult follow the official instructions here: [Configuration of Consult.](https://github.com/minad/consult#configuration)
            
            Also, make sure you review Consult's README since it recommends some other packages and useful configurations for different settings. Some of those may improve your experience of Consult-GH as well.
        
        -   [json library](https://www.gnu.org/software/emacs/manual/html_node/elisp/Parsing-JSON.html) in emacs
        
            As of Emacs 27, json is available as built-in **if emacs is compiled with json.** You can run the command `json-available-p` to see if it is available in your version of Emacs. If json is not available you may still be able to use Consult-GH with limited functionlity (you cannot view file contents).
    
    -   Recommended (but not requires) packages
    
        The following packages are not strictly required for Consult-GH to work but it can improve your experience depending on your use-case.
        
        -   [markdown-mode](https://github.com/jrblevin/markdown-mode)
        
            Since "gh" returns information in markdown, installing markdown-mode can significantly improve your experience (e.g. readability of previews, etc.). When "markdown-mode" is available, by default Consult-GH will use it as major mode to show previews of READMEs, otherwise everything it will be in raw text in "fundamental mode".
    
    -   Installing Consult-GH Package
    
        Consult-GH is not currently on [ELPA](https://elpa.gnu.org/packages/consult.html) or [MELPA](https://melpa.org/#/consult). Therefore you need ton install it using an alternative non-standard package managers such as [straight.el](https://github.com/radian-software/straight.el) or manual installation.
        
        -   straight.el
        
            To install Consult-GH with straight.el you can use the following command
            
            ```emacs-lisp
            (straight-use-package
             '(consult-gh :type git :host github :repo "armindarvish/consult-gh" :branch "main"))
            ```
            
            or if you use `use-package` macro with straight, you can do:
            
            ```emacs-lisp
            (use-package consult-gh
            	:straight (consult-gh :type git :host github :repo "armindarvish/consult-gh")
            )
            ```
            
            You can also fork this repository and use your own repo.
        
        -   manual installation
        
            Clone this repo and make sure the files are on your load path, as described on [EmacsWiki](https://www.emacswiki.org/emacs/LoadPath).

-   Configuration

    Consult-GH is built with the idea that the user should be able to customize everything based on their use-case. In fact, the default configurations are minimal and prioritize performance over usability, therefore for optimal use, the user is very much expected to configure Consult-GH according to their use case. For example, with the default configuration, when selecting a repository, Consult-GH opens the link in a browser, but you can configure that to show the README inside Emacs or clone the repository, etc. Therefore I recommend you read through this section and understand how to configure the package according to your needs and use-case, but if you just want a drop-in minimal config, look at the snippet below and make sure you replace "armindarvish" with your GitHub username:
    
    ```emacs-lisp
    (use-package consult-gh
      :straight (consult-gh :type git :host github :repo "armindarvish/consult-gh" :branch "develop")
    
      :config
      ;;add your main GitHub account (replace "armindarvish" with your user or org)
      (add-to-list 'consult-gh-default-orgs-list "armindarvish")
    
      ;;use "gh org list" to get a list of all your organizations and adds them to default list
      (setq consult-gh-default-orgs-list (append consult-gh-default-orgs-list (remove "" (split-string (consult-gh--command-to-string "org" "list") "\n"))))
    
      ;; set the default folder for cloning repositories, By default Consult-GH will confirm this before cloning
      (setq consult-gh-default-clone-directory "~/")
    )
    
    ```
    
    The configuration above adds "armindarvish" to the default orgs (used by `consult-gh-default-repos` so you can quickly see all your repositories.
    
    -   Custom Variables
    
        -   `consult-gh-tempdir`
        
            This is where temporary files are saved. By default this is set to your system temporary directory (`temporary-file-directory` variable in emacs. Consult-GH uses this folder to store temporary files (such as files you are previewing).
        
        -   `consult-gh--default-maxnum`
        
            This is the limit passed to "gh" as "&#x2013;limit" argument to "gh" commands and defines the number of results you get when searching or listing. By degault it is set to 30 following the default values of "gh" itself, but you can change it you want to see more or fewer results.
        
        -   `consult-gh-crm-separator`
        
            This is used as `crm-separator` when selecting multiple values in completing read. By default, this is set to default value of `crm-separator` (e.g. ",") but if you want to use a different character, you can change this for example by setting it to "[\s]" for using space as separator.
        
        -   `consult-gh-preview-buffer-mode`
        
            This is the major-mode used to show repository previews. By default it is set to `markdown-moe` because "gh repo view" returns the README contents (commonly in markdown syntax). But if you prefer to see the contents in other `org-mode`, you can set this variable to 'org-mode. Note that currently, the org-mode conversion is done with some simple regex replacement and while the performance is decent and the converted version is perfectly understandable, the org conversion may cause some inaccuracies. If you want to try this you can do:
            
            ```emacs-lisp
            (setq consult-gh-preview-buffer-mode 'org-mode)
            ```
        
        -   `consult-gh-default-orgs-list`
        
            This is the list default organizations you want to access frequently. I set this variable to organizations I am part of (a.k.a. to look at my own repositories) but you can add any account you want to look at frequently to this list. Here is an example of setting the default list to "alphapapa" and "systemcrafters":
            
            ```emacs-lisp
            (setq consult-gh-default-orgs-list '("alphapapa" "systemcrafters"))
            ```
            
            or if you want to append to the list:
            
            ```emacs-lisp
            (setq consult-gh-default-orgs-list (append consult-gh-default-orgs-list '("alphapapa" "systemcrafters")))
            ```
        
        -   `consult-gh-show-preview`
        
            This variable is boolean determining whether Consult-GH shows previews or not. By default this is set to nil because loading repository views or file contents can be resource-heavy depending on the size of the content. Therefore it is left to the user to decide whether showing previews is suitable for their use-case and their setup or not.
            
            Note that getting previews makes an api call to github downloads contents of a file. When you `consult-gh-show-preview` is set nil (default setting), this api call is not made (no resources used) but no preview will be available inside emacs either. This might be a useful configuration if you only want to see the name of repositories or issues and do actions such as cloning, forking or getting links, or jumping to urls in the browser but not for viewing files within emacs. .
            
            **If you want to be able to get a preview on demand without turning previews on globally, then look at `consult-gh-preview-key` below.**
        
        -   `consult-gh-preview-key`
        
            This is similar to `consult-preview-key` but only for `consult-gh`. By default it is set to the value of consult-preview-key to keep consistent experience across different consult packages, but you can set this variable explicitly for consult-gh For example if you have turned preview on by setting `consult-gh-show-preview` to t, but you still only want to see previews on demand, you can set `consult-gh-preview-key` to the key binding that shows the preview. Here is an example using "M-o" as preview key. With this setting, previews are shown only when you hit "M-o".
            
            ```emacs-lisp
            (setq consult-gh-show-preview t)
            (setq consult-gh-preview-key "M-o")
            ```
            
            Note that getting previews is resource heavy since it has to make an api call to github and get contents of a file. If you set a specific key for `consult-gh-preview-key`, this api call and downloading the content is done only when you hit the key binding.
        
        -   `consult-gh-confirm-before-clone`
        
            This is a boolean determining whether Consult-GH asks for path and directory name before cloning a repository. By default it is set to `t`, meaning that Consult-GH asks the user for the path to clone a repository and the name to give the directory. However, if you don't want consult-gh to ask you every time you clone a repository, you can set this variable to `nil`, in which case Consult-GH clones repositories at `consult-gh-default-clone-directory` (see below) with the name of the repository itself. Note that setting this variable to `nil` along with setting `consult-gh-default-clone-directory` to a directory where you keep all your repositories, allows quick cloning of multiple repositories (possibly even from different accounts on GitHub).
        
        -   `consult-gh-default-clone-directory`
        
            This variable points to the directory where repositories are cloned by default. By default it is set to `"~/"` (a.k.a. user's home directory). By default Consult-GH asks for confirmation of the location, so no matter what this variable is, you can still chose the right path to clone each repository, but this allows you set the default directory at a convinient location where you keep all your repositories (e.g. `"~/code/"` or `"~/projects/"`).
            
            Keep in mind that if you do clone multiple repositories at the same time, it would be convenient to have this variable set to the right path so you don't have to navigate to it for each repository especially if you turn `consult-gh-confirm-before-clone` to nil (see above).
        
        -   `consult-gh-default-save-directory`
        
            This variable stores the default path where files are saved and by default is set to `"~/Downloads"`. Note that this is used for saving individual files and not for cloning entire repositories, which uses `consult-gh-default-clone-directory` (see above).
        
        -   `consult-gh-default-branch-to-load`
        
            This variable is used for choosing a branch when viewing files within a repository. By default it is set to `"ask"` meaning that Consult-GH will ask the user whether it should show the "HEAD" of the repository or the user wants to select a different branch. If you set this to nil, then Consult-GH will load the "HEAD" of the git repository. if you set this to any other string, then Consult-GH uses that string as the name of the branch and will try to load this branch.
        
        -   `consult-gh-repo-action`
        
            This variable stores the function that is called when a repository is selected. By default it is bound to `#'consult-gh--repo-browse-url-action` which opens the homepage of the repository in a browser. You can change it to other provided functions such as `#'consult-gh--repo-view-action` which instead fetches the README of the repository within an emacs buffer (using ) or `#' consult-gh--repo-browse-files-action` which shows the file tree of the repository (after choosing a branch) for the user to view the files. Other provided built-in actions include `#'consult-gh--repo-clone-action` and `#'consult-gh--repo-fork-action`. You can also set this variable to any custom function as long as it follows the patterns of the built-in functions for input ARGs,&#x2026;
        
        -   `consult-gh-issue-action`
        
            Similar to `consult-gh-repo-action` but for issues. This variable stores the default function that is called, when an issue is selected. By default it is bound to `#'consult-gh--issue-browse-url-action` which opens the issue page in a browser. Alternatively you can bind it to `consult-gh--issue-view-action` if you want to see the issue inside an emacs buffer. Note that this is only for viewing issues and not interacting (adding posts, filing new issues, etc.) with them. This is mainly due to the fact that the "gh" backend itself is also limited when it comes to managing issues (interactive command line queries are not that useful!). In the future I may add more features here, but keep in mind that for managing issues (and pull requests), we can use other packages like [magit/forge](https://github.com/magit/forge) that cover sources other than GitHub as well and therefore this is not high in priority right now.
        
        -   `consult-gh-file-action`
        
            Similar to `consult-gh-repo-action` and `consult-gh-issue-action` but for files. This variable stores the default function that is called, when a file is selected. By default it is bound to `#' consult-gh--files-browse-url-action` which opens the file page in a browser. Alternative you can bind it to other provided action functions for files such as `consult-gh--files-view-action` which opens the file in an emacs buffer (in the right major mode as well) or `consult-gh--files-save-file-action` which allows you to save a file without cloning the entire repository. If you select multiple files using [`consult-gh-crm-separator`](#org1f75033) you can even save multiple files possibly from different repositories.


# Features with Examples

To be completed


# Bug reports

To report bug, first check if it is already reported in the [**issue tracker**](https://github.com/armindarvish/consult-gh/issues) and see if there is an existing solution or add relevant comments, discussion under the same issue. If not file a new issue following these steps:

1.  Make sure the dependencies are installed and you are logged in with "gh auth login".

2.  Make sure that dependencies (i.e. `consult` and `gh`) work properly independent of Consult-GH. You can for example run some other consult commands (`consult-buffer`) to make sure the problem is not form consult and run some gh commands in the shell (i.e. `gh repo list`) to see if they are working properly. If the problem is from consult or gh, please refer to their manuals and documentation.

3.  Remove the package and install the latest version (along with dependencies) and see if the issue persists.

4.  In a bare bone vanilla Emacs (>=28) (e.g. emacs -Q), install the latest version of Consult-GH (and its dependencies) without any configuration or other packages and see if the issue still persists.

5.  File an issue and provide important information and context in as much detail as possible in your bug report. Important information can include:
6.  Your operating system, version of Emacs (or the version of emacsen you are using), version of gh (run `gh --version` in a shell), version of consult (see [pkg-info](https://github.com/emacsorphanage/pkg-info)).
7.  The installation method and the configuration you are using with your Consult-GH.
8.  If there is an error message, turn debug-on-error on (by `M-x toggle-debug-on-error`) and include the backtrace content in your report.
9.  If the error only exists when you have some other packages installed, list those packages (e.g. peoblem happens when evil is installed)


# Contributions

This is an open source package, and I appreciate feedback, suggestions, ideas,&#x2026; If you want to contribute to the code, please open a pull request.


# Acknowledgments

Obviously this package would not have been possible without the fabulous [Consult](https://github.com/minad/consult) package. It also took inspiration some of the auxillary packages of consult as well as [gh.el](https://github.com/sigma/gh.el), [magit/forge](https://github.com/magit/forge) and some other github related work. Special thanks to the maintainer of consult package, [Daniel Mendler](https://github.com/minad), for useful advice, feedback and discussion.