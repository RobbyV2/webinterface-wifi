webinterface-wifi with rm-persist

The upstream install script writes its unit into /etc/systemd/system, which on
the Paper Pro is a boot-wiped overlay and on every device is replaced by the
next firmware update. This directory carries the same unit, staged through
rm-persist so it survives both. The Rust code is unchanged; the aarch64
release binary runs on the Paper Pro as is.

    ssh root@10.11.99.1 "cat > /home/root/.local/bin/webinterface-wifi" < webinterface-wifi   # release binary
    ssh root@10.11.99.1 "cat > /home/root/units/webinterface-wifi.service" < remarkable/webinterface-wifi.service
    ssh root@10.11.99.1 "cat > /home/root/.local/bin/rm-persist" < remarkable/rm-persist
    ssh root@10.11.99.1 "chmod 755 /home/root/.local/bin/webinterface-wifi /home/root/.local/bin/rm-persist"
    ssh root@10.11.99.1 /home/root/.local/bin/rm-persist install

The unit runs `webinterface-wifi local-exec` with WEBINT_WIFI_RUN_ENV=DAEMON,
as upstream's unit does, and waits for /home and the network. Config stays in
/home/root/.config/webinterface-wifi/config.toml.

The proxy needs xochitl's own web interface listening on 10.11.99.1:80, which
is WebInterfaceEnabled=true in /home/root/.config/remarkable/xochitl.conf, a
file that survives updates on its own. rm-persist is the same script shipped
in the newt and MeshAgent forks; its comments describe the two root partitions,
the swupdate hook and the boot and shutdown re-staging.
