---
layout: post
title: "My dot files"
date: 2013-08-12 17:37
comments: true
categories: 
---

### What's the [dotfiles][1]?
* A dot-file is generally any file whose name begins with a full stop.

### Why we should use dot file?
* Keep environment consistensy
* Own settings, configurations, or themes

### Which dotfiles we need?  
* Git
    - .gitconfig  

        <pre><code>
            [user]
                name = jade.zhangjin
                email = jade.zhangjin@ef.com
            [gui]
                recentrepo = E:/Program Files/SmartGit 2/bin/blitz
                recentrepo = E:/EF_Work/cheetah
            [core]
                excludesfile = .gitignore
            [alias]
                st = status
                sts = status --short
                ci = commit -m
                cia = commit -am
                cie = commit --amend
                co = checkout
                br = branch
                sb = show-branch
                cp = cherry-pick
                rb = rebase
                rbc = rebase --continue
                rbs = rebase --skip
                rl = reflog
                rs = remote show
                rt = remote
                ru = remote update
                rp = remote prune
                sm = submodule
                add = add -v
                staged = diff --staged
                unstage = reset HEAD
                diff-all = diff HEAD
                diff-stat = diff -b --stat
                log-graph = log --all --graph --decorate
                log-refs = log --all --graph --decorate --simplify-by-decoration --no-merges
                log-timeline = log --oneline --format='%h %an %ar - %s'
                log-local = log --oneline origin..HEAD
                log-fetched = log --oneline HEAD..origin/master
                hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
                type = cat-file -t
                dump = cat-file -p
                codev = checkout team/school-pc/develop  
                codra = checkout team/school-dragon/develop
                pulldra= pull --rebase  origin team/school-dragon/develop
                pushdra= push origin team/school-dragon/develop
                corefac = checkout feature/school/ux/refactor  
                cosdev = checkout feature/school/develop  
                pulldev = pull --rebase  origin team/school-pc/develop  
                pushdev = push origin team/school-pc/develop
                pullsdev = pull --rebase  origin feature/school/develop  
                pushsdev = push origin feature/school/develop  
                pullrefac = pull --rebase origin feature/school/ux/refactor 
                pushrefac = push origin feature/school/ux/refactor
                udsub   = submodule update --recursive
                udinitsub = submodule update --recursive --init
                ls = log --pretty=format:"%C(yellow)%h%Cred%d\\ %Creset%s%C(yellow)\\ [%cn]" --decorate
                ll = log --pretty=format:"%C(yellow)%h%Cred%d\\ %Creset%s%C(yellow)\\ [%cn]" --decorate --numstat
                lnc = log --pretty=format:"%h\\ %s\\ [%cn]"
            [core]
                autocrlf = true
            [color]
                branch = auto
                status = auto
            [format]
                pretty = %h %an %ad - %s # default log --pretty format
            [log]
                date = iso # log use iso format for date
                decorate = short # decorate log with branch name
                abbrevCommit = true # show short SHA1 hash
            [merge]
                tool = p4merge
            [mergetool "p4merge"]
                path = C:\\Program Files\\Perforce\\p4merge.exe
                cmd = p4merge "$BASE" "$LOCAL" "$REMOTE" "$MERGED"
                keepTemporaries = false
                keepBackup = false
                trustExitCode = false
            [diff]
                tool = p4merge
            [difftool "p4merge"]
                path = C:\\Program Files\\Perforce\\p4merge.exe
                cmd = p4merge "$LOCAL" "$REMOTE"
                keepBackup = false
                trustExitCode = false
            [credential]
                helper = !'C:\\Users\\jade.zhangjin\\AppData\\Roaming\\GitCredStore\\git-credential-winstore.exe'
            [push]
                default = upstream
        </code></pre>

    - .gitignore  
        <pre><code>
          .min.js
          node_modules
          .min.css
        </code></pre>
* Shell (bash/zsh/csh)
    - .bashrc   
        <pre><code>
            # Common Alias
            alias cls='clear'
            alias g='git'
            alias open='explorer .'
            alias stash='cd $work'
            alias ui='cd $work/school-ui'
            alias act='cd $work/school-ui-activity'
            alias cont='cd $work/school-ui-activity-container'
            alias school='cd $work/school'
            alias shared='cd $work/school-ui-shared'
            alias study='cd $work/school-ui-study'
            alias blitz='cd /e/Project/blitz'
            alias dist='cd $shared_work/ui-shared-dist'
            alias st='git st'
            alias co='git co'
            alias diff='git difftool'
            alias merge='git mergetool'
            alias codcr='git $codra'
            alias pulldcr='git $pulldra'
            alias pushdcr='git $pushdra'
            alias gpp='git pull origin; git push origin'
        </code></pre>
* System Env config (Ruby/Python/NodeJS...)
    - .editorconfig  
        <pre><code>
        ; EditorConfig is awesome: http://EditorConfig.org

        ; top-most EditorConfig file
        root = true

        ; 4 space indentation
        [*.py]
        indent_style = space
        indent_size = 4

        ; Tab indentation (no size specified)
        [*.js]
        indent_style = space
        indent_size = 4
        trim_trailing_whitespace = true
        </code>
        </pre>
    - .jshintrc  
        <pre><code>
        {
          "undef": true,
          "unused": true,
          "globals": { 
            "jQuery": false,
            "define": false,
            "browser": false
          }
        }
        </code></pre>
### Bootstrap.sh
* Auto install develop software
* Auto config environment (hostFile/auto startup)
* Intial all repos project source
* Auto run local web server 
<pre><code>
    #!/bin/bash
    # init-Xnix-env.sh
    # A tool used to config front-end developer's environment.

    # Notes:
    # 1. Please use "WorkSpace" as develop directory
    # 2. If you got error 'Error: listen EADDRINUSE', then you need check apps runs at net port 80
    # 3. This command automatically setes host file
    # 4. Please use cns-etuat-[9,18,19,21].localnet.englishtown.com to access website
    # 5. 'school-ui' repo need checkout "team/school-pc/dev-server-template" branch for dev develop
    root="Stash"
    config="config.json"

    # Util Functions
    function detectEnv() {
        if [ command -v $1 >/dev/null 2>&1 ]
        then 
            return 1
        else 
            return 0
        fi
    }

    function installNodeJS() {
        git clone git://github.com/ry/node.git
        cd node
        ./configure
        make
        sudo make install
        cd ..

        git clone http://github.com/isaacs/npm.git
        cd npm
        sudo make install
        
        cd ..
        export NODE_PATH="/usr/local/lib/node"
    }

    function installGit {
        version=1.8.2.1
        curl -O https://git-core.googlecode.com/files/git-$version.tar.gz
        tar xzvf git-$version.tar.gz cd git-$version
        
        tar xzvf git-1.7.7.tar.gz cd git-1.7.7
        ./configure --prefix=/usr/local
        make 
        sudo make install
        cd ..
    }

    function setEnv {
        export PATH="/usr/local/bin:/usr/local/sbin:usr/local/lib;/usr/local/mysql/bin:$PATH"
        source ~/.bashrc
    }

    function createDir() {
        for i in $@
        do echo $i && mkdir $i
        done
    }

    function ensureDir() {
        for i in $@
        do
            if [ -d "$i" ]
            then
                echo $i exists.
            else
                echo Ensure Dir $i
                createDir "$i"
            fi
        done
    }

    function clone() {
        shared_repo=ui-shared-dist
        framework=ef-troopjs
        # Note: must put school-ui at last, cause we have special logic for this 
        school="school-ui-shared school-ui-activity school-ui-activity-container school-ui-study school-ui"
        blue="et-ui-headerfooter tooltips-ui"
        repo_server="https://stash.englishtown.com/scm"
        branch="team/school-pc/develop"
        must_have="school-ui school-ui-activity school-ui-study school-ui-shared"
        # $1 is type, $2 is repo name

        if [ $1 = COMM ]
            then repo_type=$blue && branch=develop
        elif [ $1 = FRAM ] 
            then repo_type=$framework && branch=develop
        elif [ $1 = TSCHOOL ] 
            then repo_type=$school
        elif [ $1 = SHARE ] 
            then repo_type=$shared_repo
        fi

        for i in $repo_type
        do 
            echo Start to clone $i now...  
            if [ ! -d $i ]; then git clone -b $branch $repo_server/$1/$i.git; fi;
            cd $i && git submodule update --init --recursive && cd ..
        done

        if [ $1 = SHARE ] 
            then cd ui-shared-dist && ensureDir $must_have && cd ..
        fi  
    }

    function mkSoftLink() {
      for i in $@;
      do 
        ln -s ~/$root/Workspace/labs-school/$i/src ~/$root/Server/root/localnet.englishtown.com/_shared/$i/snapshot 
      done
    }

    cd ~
    # Steps to setup dev environment in Xnix:
    # 1. Detect NodeJS/Git is installed or not
     detectEnv node || installNodeJS
     detectEnv git || installGit 
     setEnv
    # 2. Check node-devserver is installed or not, if not, auto install it
     detectEnv npm && sudo -- sh -c "npm install -g devserver"
    # 3. Create structure dir of Project Server and work space
    ensureDir $root && cd ~/$root
    path="Server Server/root Server/root/localnet.englishtown.com Workspace"
    createDir $path
    # 4. Generate Dev Server config file

    cd ~/$root/Server 

    echo [{                                                                   > $config
    echo         \"module\" : \"devserver.frontend\",                             >> $config
    echo         \"arguments\" : [ \"root\" ]                                     >> $config
    echo }, {                                                                 >> $config
    echo         \"module\" : \"devserver.backend\",                              >> $config
    echo         \"arguments\" : [{                                             >> $config
    echo                 "\"regexp\" : \"^(?<uat>(cns-etuat|cnshhq|cns)-\\\\d+)\\\\.(?<vhost>.+)\"", >> $config
    echo                 \"proxy\" : {                                          >> $config
    echo                         \"host\" : \"\$\{uat\}.ef.com\",                    >> $config
    echo                         \"port\" : 80                                 >> $config
    echo                 }                                                    >> $config
    echo         }]                                                           >> $config
    echo }]                                                                   >> $config
    # 5. Make system start up service for startup dev server
    # 6. Indicate whether is the first time to use Git, if it is, then remind to config git
    git config --list --global | grep user.name || git config --global user.name 
    git config --list --global | grep user.email || git config --global user.email 
    # 7. Get school related code from stash, checkout 'team/school-pc/develop' branch , init and update submodule
    cd ~/$root/Workspace
    repo_ctg="Frameworks labs-blue labs-school labs-shared"
    createDir $repo_ctg

    cd ~/$root/Workspace/labs-shared && clone SHARE
    cd ~/$root/Workspace/labs-blue && clone COMM
    cd ~/$root/Workspace/Frameworks && clone FRAM 
    cd ~/$root/Workspace/labs-school && clone TSCHOOL

    # 8. Make snapshot soft link 
    ln -s ~/$root/Workspace/labs-shared/ui-shared-dist/ ~/$root/Server/root/localnet.englishtown.com/_shared
    ln -s ~/$root/Workspace/Frameworks/ef-troopjs/src ~/$root/Server/root/localnet.englishtown.com/_shared/troopjs-ef/snapshot
    ln -s ~/$root/Workspace/labs-blue/et-ui-headerfooter/src ~/$root/Server/root/localnet.englishtown.com/_shared/headerfooter/snapshot

    repos="school-ui school-ui-study school-ui-shared school-ui-activity school-ui-activity-container"
    mkSoftLink $repos
    # 9. Config Host File (cns-etuat-9,cns-etuat-18,cns-etuat-19,cns-etuat-21)
    host_file="/etc/hosts"
    host="cns-etuat-9 cns-etuat-18 cns-etuat-19 cns-etuat-21"
    sudo  -- sh -c "echo >> /etc/hosts"
    echo Start to config host file now...
    for i in $host 
    do 
       cat $host_file | grep $i.localnet.englishtown.com || sudo -- sh -c "echo 127.0.0.1 $i.localnet.englishtown.com >> /etc/hosts"
    done   
    # 10.Start server at 88 port 
    cd ~/$root/Server && node-devserver -c $config
</code></pre>

### ***Todo***: continue integration dotfiles to this blog

[1]: http://dotfiles.org/
[2]: http://dotfiles.github.io/