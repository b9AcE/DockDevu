# DockDevu

A basic Devuan Linux OS image based on the developer's (dyne's) ceres Dockerfile,
but with some additions such as:
* A non-root user "duser", with password "devdev" and sudo-capabilities.
* youtube-dl (also callable as "ytdl" and "y") installed not via apt but direct from the upstream project to enable speedy updating, as apt's updates are slow for youtube-dl.
* python3 and 2, switchable through update-alternatives.
* A system-wide tmux.conf-file with some reasonable settings.
* The manpages are included, for easy reference.

Intended primarily as a basic isolated discardable interactive use environment.
