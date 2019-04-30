---
title: OpenShift telepítés az Azure-ban – hibaelhárítás |} A Microsoft Docs
description: Végezzen hibaelhárítást az OpenShift telepítés az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: af6746e7246b8783e5bdbef34cf1b57427aa7ebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771277"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Az Azure-ban az OpenShift telepítés hibaelhárítása

Nem sikerült az OpenShift-fürt üzembe helyezése, ha az Azure portal nyújt hibakimenet. Lehet, hogy a kimenet nehezen olvasható, így nehéz a probléma meghatározásához. Ez a kimenet a kilépési kód 3, 4 vagy 5 gyors vizsgálat. A következő információkat tartalmazza az alábbi három kilépési kódot:

- 3. kilépési kód: A Red Hat-előfizetés felhasználónév / jelszó vagy a szervezeti azonosító / aktiválási kulcs helytelen
- 4. kilépési kód: A Red Hat-készlet azonosítója nem megfelelő, vagy a jogosultságok nem állnak rendelkezésre
- 5. kilépési kód: Nem sikerült kiépíteni a Docker vékony tárolókészlet kötetét

Más kilépési kódok csatlakozni a állomásokkal keresztül az ssh naplófájljainak megtekintéséhez.

**OpenShift Container Platform**

Az ansible forgatókönyv állomás ssh-KAPCSOLATOT. A sablon vagy a Piactéri ajánlat használja a bástyagazdagép. A megerősített SSH lehetővé minden más csomópontokhoz a fürtben (master, infra, CNS, számítás). A naplófájlok megtekintése gyökérkönyvtára kell. Legfelső szintű ezért ne használjon más csomópontokhoz legfelső szintű ssh alapértelmezés szerint nem végezhető el SSH-hozzáférést.

**OKD**

Az ansible forgatókönyv állomás ssh-KAPCSOLATOT. A OKD sablon (3.9 és korábbi verzió) a master-0 gazdagépet használnak. A OKD sablon (3.10 és újabb verzió) használata a bástyagazdagép. Az ansible forgatókönyv gazdagépről SSH minden más csomópontokhoz a fürtben (master, infra, CNS, számítás) is. Kell gyökérkönyvtára (sudo su-) megtekintéséhez a rendszernapló fájljaiban. Legfelső szintű ezért ne használjon más csomópontokhoz legfelső szintű ssh alapértelmezés szerint nem végezhető el SSH-hozzáférést.

## <a name="log-files"></a>Naplófájlok

Az állomás előkészítő parancsfájlok naplófájlok (stderr és stdout) található `/var/lib/waagent/custom-script/download/0` minden gazdagépre. Ha hiba történt a gazdagép előkészítése során, tekintse meg a naplófájlok megállapítani a hiba.

Ha az előkészítő parancsfájlok sikeresen lefutott, majd a naplófájlok a a `/var/lib/waagent/custom-script/download/1` a ansible-forgatókönyvek gazdagép directory kell vizsgálni. Ha a hiba történt az OpenShift tényleges telepítése során, a stdout fájlt a hiba jelenik meg. Ezen információk használatával további segítségért forduljon az ügyfélszolgálathoz.

Példa a kimenetre

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

A telepítés során a leggyakoribb hibák a következők:

1. Titkos kulcs jelszava van
2. A Key vault titkos kulcs és a titkos kulcs létrehozása nem lett megfelelően
3. Egyszerű szolgáltatás hitelesítő adatai nem megfelelően megadott
4. Egyszerű szolgáltatás nem rendelkezik az erőforráscsoporthoz közreműködői hozzáféréssel

### <a name="private-key-has-a-passphrase"></a>Titkos kulcsa jelszóval rendelkezik

Látni fogja, hogy az engedély megtagadva az ssh hiba. ssh az ansible forgatókönyv gazdagépre egy jelszót a titkos kulcs kereséséhez.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>A Key vault titkos kulcs és a titkos kulcs létrehozása nem lett megfelelően

A titkos kulcs átmásolja az ansible forgatókönyv gazdagép - ~/.ssh/id_rsa. Ellenőrizze, hogy ez a fájl helyes-e. Tesztelje az SSH-munkamenetből a fürtcsomópontok egy az ansible forgatókönyv gazdagépről nyissa meg.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Egyszerű szolgáltatás hitelesítő adatai nem megfelelően megadott

Ha megadta a bemenetet a sablonból vagy a Piactéri ajánlat, a helytelen információt lett megadva. Ellenőrizze, hogy az egyszerű szolgáltatás a helyes appId (clientId) és a jelszót (clientSecret) használja. Ellenőrizze a következő azure cli-parancs kiadásával.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Egyszerű szolgáltatás nem rendelkezik az erőforráscsoporthoz közreműködői hozzáféréssel

Ha az Azure-felhő szolgáltató engedélyezve van, a szolgáltatásnév az erőforráscsoport közreműködői hozzáféréssel kell rendelkeznie. Ellenőrizze a következő azure cli-parancs kiadásával.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>További eszközök

Néhány hiba a további információért is használhatja a következő parancsokat:

1. systemctl állapot \<service >
2. journalctl -xe
