# SSH Proxy to GitLab

Auf dem Proxy System:

    apt install simpleproxy

Dienst mit systemd laufen lassen: /etc/systemd/system/simpleproxy-sshd.service

    [Unit]
    Description=Proxy SSH Connections to target
    After=network.target auditd.service

    [Service]
    Restart=always
    Type=simple
    User=root
    ExecStart=/usr/bin/simpleproxy -L 192.0.2.1:23 -R 10.1.2.4:22 -v
    KillMode=process

    [Install]
    WantedBy=multi-user.target

Oder mit upstart: /etc/init/simpleproxy-sshd.conf

    description "Proxy SSH Connections to target"

    start on runlevel [2345]
    stop on runlevel [!2345]

    respawn

    exec /usr/bin/simpleproxy -L 192.0.2.1:23 -R 10.1.2.4:22 -v

Mit upstart starten:

    start simpleproxy-sshd
    status simpleproxy-sshd

Auf dem GitLab System wird ein weiterer SSH Dienst gestartet:

/etc/ssh/sshd_git_config

    AcceptEnv GIT_PROTOCOL
    ChallengeResponseAuthentication no
    UsePAM yes
    Port 2222
    AllowGroups git
    UseDns no
    GssapiAuthentication no
    Protocol 2
    PermitRootLogin no
    PasswordAuthentication no
    ClientAliveInterval 30
    MaxStartups 100:30:200
    PubkeyAcceptedKeyTypes +ssh-dss
    AuthorizedKeysCommand /opt/gitlab/embedded/service/gitlab-shell/bin/gitlab-shell-authorized-keys-check git %u %k
    AuthorizedKeysCommandUser git

/etc/systemd/system/sshd_git.service

    [Unit]
    Description=OpenBSD Secure Shell server for GitLab
    After=network.target auditd.service
    ConditionPathExists=!/etc/ssh/sshd_not_to_be_run

    [Service]
    ExecStart=/usr/sbin/sshd -D -f /etc/ssh/sshd_git_config
    ExecReload=/bin/kill -HUP $MAINPID
    KillMode=process
    Restart=on-failure
    RestartPreventExitStatus=255
    Type=notify

    [Install]
    WantedBy=multi-user.target

Danach:

    systemctl daemon-reload
    systemctl enable --now sshd_git.service

Referenzen:
* https://gitlab-com.gitlab.io/runbooks/howto/haproxy/
* https://gitlab.com/gitlab-cookbooks/gitlab-haproxy/-/blob/master/templates/default/haproxy-frontend.cfg.erb
* https://gitlab.com/gitlab-cookbooks/gitlab-openssh/-/blob/master/attributes/sshd_git.rb
* https://gitlab.com/gitlab-cookbooks/gitlab-openssh/-/blob/master/recipes/sshd_git.rb
