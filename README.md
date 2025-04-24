# 🚀 Understanding Bash Profile and Linux Startup

## What is a Bash Profile? 🖥️

A **Bash profile** is a configuration file that customizes your Bash shell environment when you start a terminal. It defines:
- **Environment variables** (e.g., `PATH`, `HOME`).
- **Aliases** (e.g., `alias ll='ls -la'`).
- **Functions** or scripts for automation.
- **Shell settings** (e.g., prompt colors, history size).

The key files are `~/.bash_profile` and `~/.bashrc`, and their use depends on the shell session type (login vs. non-login).

## 🆚 .bashrc vs .bash_profile

Here’s how they differ:

| **Aspect**            | **.bashrc**                                  | **.bash_profile**                          |
|-----------------------|----------------------------------------------|--------------------------------------------|
| **Path**              | `~/.bashrc`                                  | `~/.bash_profile`                          |
| **Purpose**           | Configures **non-login shells** (e.g., new terminal tab). | Configures **login shells** (e.g., SSH login). |
| **When it Runs**      | Interactive, non-login sessions (e.g., terminal window). | Login sessions (e.g., GUI login, SSH).     |
| **Typical Content**   | Aliases, functions, shell options (e.g., `PS1`). | Environment variables (`PATH`), startup scripts. |
| **Example**           | `alias k='kubectl'`                          | `export PATH="$HOME/bin:$PATH"`            |

**Notes**:
- Many users source `.bashrc` in `.bash_profile` to unify settings:
  ```bash
  # In ~/.bash_profile
  if [ -f ~/.bashrc ]; then
      source ~/.bashrc
  fi
  ```
- If `.bash_profile` exists, Bash skips `.bashrc` in login shells unless sourced.
- Some distros (e.g., Ubuntu) use `.profile` instead of `.bash_profile` if it’s absent.

## 🥾 How Linux Loads Bash Files at Startup

Linux loads Bash configs based on the shell session type. Here’s the process:

1. **System-Wide Configs** 📜
   - Loads `/etc/profile` (login shells) and `/etc/bash.bashrc` (non-login shells, if present).
   - Sets system-wide defaults (e.g., `PATH`).

2. **Login Shell Startup** 🔑
   - For login sessions (e.g., SSH, GUI login, `bash --login`), Bash checks:
     1. `~/.bash_profile` (if present).
     2. `~/.bash_login` (if no `.bash_profile`).
     3. `~/.profile` (if neither exists).
   - `.bash_profile` typically sets environment variables and sources `.bashrc`.

3. **Non-Login Shell Startup** 💻
   - For new terminal windows or `bash` (non-login), Bash loads `~/.bashrc`.
   - Applies aliases, functions, and prompt settings.

4. **Graphical Environments** 🖼️
   - GUI terminals (e.g., GNOME Terminal) often start non-login shells, loading only `.bashrc`.
   - Some distros source `.profile` during GUI login for environment variables.

5. **Custom Behavior** 🛠️
   - Users can source files (e.g., `.bash_profile` sourcing `.bashrc`).
   - Distro quirks (e.g., Ubuntu’s `.profile`) may change behavior.

## 🛑 Common Gotchas
- **No `.bash_profile`**: Bash falls back to `.profile`, which may not source `.bashrc`.
- **PATH Overwrites**: Avoid duplicating `PATH` in both files.
- **Terminal Settings**: Some terminals (e.g., VS Code) skip `.bash_profile`.

## 🎉 Example Setup
Unify settings across shells:
- **~/.bash_profile**:
  ```bash
  # Environment variables
  export PATH="$HOME/bin:$PATH"
  export EDITOR="vim"

  # Load .bashrc
  if [ -f ~/.bashrc ]; then
      source ~/.bashrc
  fi
  ```
- **~/.bashrc**:
  ```bash
  # Aliases
  alias ll='ls -la'
  alias g='git'

  # Custom prompt
  PS1='\u@\h:\w\$ '
  ```

## 📝 Some Examples

```bash
# Get ip info without typing
ipinfo(){
curl http://ipinfo.io/$1
}

# Install App just typing install appname
install() {
    sudo apt install "$@" -y
}

# Install basic app
install_basic() {
    sudo apt install -y vim nano tldr zip unzip vim jq screen htop atop iotop sysstat ioping tcptrack traceroute dnsutils pinfo speedtest-cli net-tools iptraf iptraf-ng ncdu gdu aptitude
}


# Get basic info without typing multiple commands, you can add or remove according to your need.

info() {
    echo "Check Failed Login Attempts"
    cat /var/log/auth.log | grep "Failed password"
    
    echo -e "\nSystem-related Error and Information Messages:"
    cat /var/log/syslog | grep -E "error|warning|info"

    echo -e "\nSystem Information:"
    last -n 5  # Show last 5 logins
    uptime     # Display system uptime
    df -h      # Show disk space usage
}

# Get basic info without typing multiple commands, you can add or remove according to your need.

alias home='clear && cd /home/user && ls -alsh'  
alias topcpu='ps -eo pcpu,args --sort=-pcpu | head -n 50 | awk '"'"'{printf("%13.2f%%\t", $1); print $2}'"'"''
alias topmem='ps -eo "rss,args" --sort=-rss | head -n 50 | awk "{hr=\$1/1024; printf(\"%13.2fM\t\", hr); print \$2}"'

# Extract any type of compressed file
extract () {
    if [ -f $1 ] ; then
      case $1 in
        *.tar.bz2)   tar xjf $1     ;;
        *.tar.gz)    tar xzf $1     ;;
        *.bz2)       bunzip2 $1     ;;
        *.rar)       unrar e $1     ;;
        *.gz)        gunzip $1      ;;
        *.tar)       tar xf $1      ;;
        *.tbz2)      tar xjf $1     ;;
        *.tgz)       tar xzf $1     ;;
        *.zip)       unzip $1       ;;
        *.Z)         uncompress $1  ;;
        *.7z)        7z x $1        ;;
        *)     echo "'$1' cannot be extracted via extract()" ;;
         esac
     else
         echo "'$1' is not a valid file"
     fi
}

# Restart Nginx after validating of it, it will print information of action.
nt(){
# Perform Nginx configuration test
sudo nginx -t > /dev/null 2>&1
# Check if the Nginx test failed
if [ $? -ne 0 ]; then
    echo "Nginx test failed. Exiting script."
    exit 1
fi
echo "Nginx test Passed."
# If the test passed, reload Nginx
sudo service nginx reload
# Check if Nginx reload was successful
if [ $? -eq 0 ]; then
    echo "Nginx service reloaded successfully."
else
    echo "Failed to reload Nginx service."
fi
}
```

## 🐳 Bash Profile to Manage Docker in anywhere

Here are the my customized commands to manage docker in production, have a look and customize it if required.

[👉 My Bash Profile](./bash_profile_examples/bash_profile)

##### 🧠 Bash Profile Alias Notes

> ⚠️ **Note:** I will add more aliases in this bash profile based on my YouTube video topics.  
> Keeping this file updated will help automate repetitive terminal tasks and keep my workflow efficient! 🚀


### 💼 Connect with me 👇👇 😊

- 🔥 [**Youtube**](https://www.youtube.com/@DevOpsinAction?sub_confirmation=1)
- ✍ [**Blog**](https://ibraransari.blogspot.com/)
- 💼 [**LinkedIn**](https://www.linkedin.com/in/ansariibrar/)
- 👨‍💻 [**Github**](https://github.com/meibraransari?tab=repositories)
- 💬 [**Telegram**](https://t.me/DevOpsinActionTelegram)
- 🐳 [**Docker**](https://hub.docker.com/u/ibraransaridocker)

# Hit the Star! ⭐
***If you are planning to use this repo for learning, please hit the star. Thanks!***
