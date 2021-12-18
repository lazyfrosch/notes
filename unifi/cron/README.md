# Unifi USG Cron

Copy the script to the USG:

    scp reconnect.sh router:/config/scripts/reconnect.sh

Copy `config.gateway.json` to the Unifi Controller:

* `/var/lib/docker/volumes/unifi_data/_data/sites/default/config.gateway.json`
* inside the container: `/var/lib/unifi/sites/default/config.gateway.json`

Links:

* https://community.ui.com/questions/USG-Cron-job-in-json/627c3445-1b6c-4cbe-b681-41acc240ab63
* https://help.ui.com/hc/en-us/articles/215458888
