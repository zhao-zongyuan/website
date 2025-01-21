---
title: "Multiple Github accounts setup in Windows"
date: 2025-01-21
---

# Description

| Item | Value |
|--------------|----------------|
| OS     | Windows 11 |
| IDE    | VSCode |
| personal account name |zhao-zongyuan |
| personal account email |  zzy198700@hotmail.com |
| work account name | wcq |
| work account email | |
| authentication type | SSH |
| local repo directory for personal | c:\repo\zzy198700\ |
| local repo directory for work | c:\repo\wcq\ |
| local git directory | C:\Users\John Zhao\ |
| local ssh directory | C:\Users\John Zhao\.ssh\ |


# Multiple SSH (optional)
Follow [this](https://www.baeldung.com/linux/ssh-private-key-git-command#:~:text=One%20way%20we%20can%20specify,ssh%2Fconfig%20file.)


# Multiple account for Github

Use  global gitconfig file 

1. Follow the step above, generate SSH keys for your different GitHub/Gitlab accounts and configure them at GitHub/GitLab.

2. You can see all your generated keys at ~/.ssh (in my case C:\Users\John Zhao\.ssh\ )
    - id_ed25519
    - zzy198700

3. The only thing to do now is to configure Git to use the correct SSH key, we are going to use different ``.gitconfig`` files for each account and link them to our root source code directory using the [``includeif``](https://git-scm.com/docs/git-config#_includes) command.
    - The global one is located at ``C:\Users\John Zhao\``. This is now its content. Be very careful of the ``/i`` for case-insensitive 
    ```
    [includeIf "gitdir/i:c:/Repo/zzy198700/"]
    path = c:/Repo/zzy198700/.gitconfig
    [includeIf "gitdir/i:c:/Repo/wcq/"]
    path = c:/Repo/wcq/.gitconfig
    ```
    
    - We need to add a ``.gitconfig`` in both personal and work repo directoies:
    ```
    \repo
        \zzy198700
            .gitconfig
            \repo1
            \repo2
            ...
        \wcq
            .gitconfig
            \repo1
            \repo2
            ...
        ...
    ``` 

    - Example of personal ``.gitconfig``
    ```
    [core] 
        sshCommand = ssh -i ~/.ssh/zzy198700
    [user]
        name = zhao-zongyuan
        email = zzy198700@hotmail.com
    ```

    - Example of work ``.gitconfig``
    ```
    [core] 
        sshCommand = ssh -i ~/.ssh/id_ed25519
    [user]
        name = John-Zhao-WCQ
        email = john.zhao@workcoverqld.com.au
        signingkey = xxxxxxxxx
    [safe]
        directory = C:/Repo/wcq
    [commit]
        gpgsign = true
    ```

4. Now we can use VSCode Github extension or PowerShell for all git actions.