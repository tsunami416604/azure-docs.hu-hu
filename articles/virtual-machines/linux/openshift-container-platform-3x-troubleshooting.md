---
title: Az OpenShift Container Platform 3.11 üzembe helyezése az Azure-ban
description: Az OpenShift Container Platform 3.11 üzembe helyezésének hibaelhárítása az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: bd83a1ca731d81edb76a3c1bc07113ce96adb9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066591"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Az OpenShift Container Platform 3.11 üzembe helyezése az Azure-ban

Ha az OpenShift-fürt nem telepíti sikeresen, az Azure Portal on error output. A kimenet nehezen olvasható, ami megnehezíti a probléma azonosítását. Gyorsan átkezhet a kimeneten a 3-as, 4-es vagy 5-ös kilépési kódra. Az alábbiakban a következő három kilépési kódról nyújt tájékoztatást:

- 3.-as kilépési kód: Helytelen a Red Hat előfizetési felhasználóneve / jelszava vagy szervezeti azonosítója / aktiválási kulcsa
- Kilépési kód 4: A Red Hat pool azonosítója helytelen, vagy nincsenek elérhető jogosultságok
- 5.-es kilépési kód: Nem lehet kiépíteni a Docker vékonykészlet-kötetet

Az összes többi kilépési kód esetén csatlakozzon az állomás(ok)hoz ssh-n keresztül a naplófájlok megtekintéséhez.

**Az OpenShift-tárolóplatform 3.11-es verziója**

SSH az ansible forgatókönyv gazdagép. A sablon vagy a Marketplace-ajánlat, használja a megerősített gazdagép. A bástya, akkor SSH az összes többi csomópont a fürtben (master, infra, CNS, számítási). A naplófájlok megtekintéséhez root nak kell lennie. Root alapértelmezés szerint le van tiltva az SSH-hozzáféréshez, ezért ne használja a root-ot az SSH-hoz más csomópontokhoz.

**OKD**

SSH az ansible forgatókönyv gazdagép. Az OKD sablonhoz (3.9-es vagy korábbi verzió) használja a master-0 állomást. Az OKD sablon (3.10-es és újabb verzió) esetén használja a megerősített állomást. Az ansible forgatókönyv-gazdagép gazdagép, akkor SSH az összes többi csomópont a fürtben (master, infra, CNS, számítási). A naplófájlok megtekintéséhez root (sudo su -) kell lennie. Root alapértelmezés szerint le van tiltva az SSH-hozzáféréshez, ezért ne használja a root-ot az SSH-hoz más csomópontokhoz.

## <a name="log-files"></a>Naplófájlok

A gazdagép-előkészítési parancsfájlok naplófájljai (stderr és `/var/lib/waagent/custom-script/download/0` stdout) az összes állomáson találhatók. Ha hiba történt az állomás előkészítése során, tekintse meg ezeket a naplófájlokat a hiba meghatározásához.

Ha az előkészítési parancsfájlok sikeresen `/var/lib/waagent/custom-script/download/1` futottak, akkor az ansible playbook host könyvtárában lévő naplófájlokat meg kell vizsgálni. Ha a hiba az OpenShift tényleges telepítése során történt, az stdout fájl megjeleníti a hibát. Ezen információk alapján további segítségért forduljon az ügyfélszolgálathoz.

Példa kimenetre

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

A telepítés során leggyakrabban előforduló hibák a következők:

1. A személyes kulcs hoz jelszót
2. A titkos kulcs titkos kulcsa nem megfelelően lett létrehozva
3. A szolgáltatásnév hitelesítő adatai helytelenül lettek megadva
4. Az egyszerű szolgáltatás nem rendelkezik közreműködői hozzáféréssel az erőforráscsoporthoz

### <a name="private-key-has-a-passphrase"></a>A személyes kulcshoz jelszó van

Megjelenik egy hiba, hogy az engedélyt megtagadták az ssh. ssh az ansible forgatókönyv-gazdagép állomás, hogy ellenőrizze a jelszót a személyes kulcsot.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>A titkos kulcs titkos kulcsa nem megfelelően lett létrehozva

A titkos kulcs az ansible forgatókönyv-gazdagép - ~/.ssh/id_rsa. Ellenőrizze, hogy a fájl helyes-e. Tesztelje egy SSH-munkamenet megnyitásával az ansible forgatókönyv-gazdagép egyik fürtcsomópontja számára.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>A szolgáltatásnév hitelesítő adatai helytelenül lettek megadva

A sablon vagy a Marketplace-ajánlat bemenetének megadásakor a helytelen adatokat adták meg. Győződjön meg arról, hogy a megfelelő alkalmazásazonosítót (clientId) és jelszót (clientSecret) használja a szolgáltatásnévhez. Ellenőrizze a következő azure cli parancs kiadásával.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Az egyszerű szolgáltatás nem rendelkezik közreműködői hozzáféréssel az erőforráscsoporthoz

Ha az Azure-felhőszolgáltató engedélyezve van, akkor az egyszerű szolgáltatás nak közreműködői hozzáféréssel kell rendelkeznie az erőforráscsoporthoz. Ellenőrizze a következő azure cli parancs kiadásával.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>További eszközök

Bizonyos hibák esetén a következő parancsokkal is kaphat további információkat:

1. systemctl \<állapotszolgáltatás>
2. journalctl -xe
