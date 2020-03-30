---
title: NFS (Network File System) Ubuntu Server létrehozása az Azure Kubernetes szolgáltatás (AKS) podjai számára
description: Megtudhatja, hogyan hozhat létre manuálisan egy NFS Ubuntu Linux Server-kötetet az Azure Kubernetes szolgáltatás (AKS) podjaihoz való használatra.
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: e5676710bc47557318f3e2adcf36ec0ed13d47de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596623"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>NFS (Network File System) Linux Server-kötet manuális létrehozása és használata az Azure Kubernetes szolgáltatással (AKS)
A tárolók közötti adatok megosztása gyakran a tárolóalapú szolgáltatások és alkalmazások szükséges összetevője. Általában különböző podok, amelyek hozzáférést igényelnek ugyanazokhoz az információkhoz egy külső állandó köteten.    
Míg az Azure-fájlok egy lehetőség, nfs-kiszolgáló létrehozása egy Azure virtuális gép egy másik formája az állandó megosztott tárolás. 

Ez a cikk bemutatja, hogyan hozhat létre NFS-kiszolgálót egy Ubuntu virtuális gépen. És az AKS-tárolók nak is hozzáférést biztosít ehhez a megosztott fájlrendszerhez.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürt. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az AKS-fürtnek ugyanabban a vagy társviszonyban lévő virtuális hálózatokban kell élnie, mint az NFS-kiszolgálónak. A fürtöt egy meglévő virtuális hálózatban kell létrehozni, amely ugyanaz lehet a virtuális hálózat, mint a virtuális gép.

A meglévő virtuális hálózattal való konfigurálás lépéseit a dokumentáció ismerteti: [AKS-fürt létrehozása a meglévő virtuális hálózatban,][aks-virtual-network] valamint [virtuális hálózatok csatlakoztatása a virtuális hálózat társviszony-létesítésével][peer-virtual-networks]

Azt is feltételezi, hogy létrehozott egy Ubuntu Linux virtuális gépet (például 18.04 LTS). A beállítások és a méret tetszés szerint alkalmazhatók, és az Azure-on keresztül telepíthetők. Linux-gyorsindításról a [Linux virtuális gép kezeléséről.][linux-create]

Ha először telepíti az AKS-fürtöt, az Azure automatikusan feltölti a virtuális hálózati mezőt az Ubuntu-gép üzembe helyezésekor, így azok ugyanazon a virtuális hálózaton belül maradnak. Ha azonban inkább társviszony-létesített hálózatokkal szeretne dolgozni, olvassa el a fenti dokumentációt.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Az NFS-kiszolgáló telepítése virtuális gépre
Itt van a szkript, hogy hozzanak létre egy NFS-kiszolgáló az Ubuntu virtuális gép:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
A kiszolgáló újraindul (a parancsfájl miatt), és csatlakoztathatja az NFS-kiszolgálót az AKS-hez.

>[!IMPORTANT]  
>Győződjön meg arról, hogy a **AKS_SUBNET** a megfelelővel helyettesíti a fürtből, különben a "*" megnyitja az NFS-kiszolgálót az összes port és kapcsolat számára.

Miután létrehozta a virtuális gép, másolja a fenti parancsfájlt egy fájlba. Ezután áthelyezheti a helyi gépről, vagy bárhol is legyen a parancsfájl, a virtuális gépbe a következők használatával: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Miután a parancsfájl a virtuális gép, ssh a virtuális gép, és végrehajtja azt a parancs segítségével:
```console
sudo ./nfs-server-setup.sh
```
Ha a végrehajtás sikertelen, mert egy engedély megtagadva hiba, állítsa végrehajtási engedélyt a parancsot:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Az AKS-fürt csatlakoztatása az NFS-kiszolgálóhoz
Az NFS-kiszolgálót a fürthöz egy állandó kötet és egy állandó kötetjogcím kiépítésével tudjuk csatlakoztatni, amely meghatározza a kötet elérésének módját.

A két szolgáltatás csatlakoztatása ugyanabban a vagy társviszonyban lévő virtuális hálózatok ban szükséges. A fürt ugyanazon virtuális hálózatban való beállítására vonatkozó utasítások itt találhatók: [AKS-fürt létrehozása a meglévő virtuális hálózatban][aks-virtual-network]

Ha ugyanabban a virtuális hálózatban vannak (vagy társviszonyt adnak), állandó kötetet és állandó kötetjogcímet kell létesíteniaz AKS-fürtben. A tárolók ezután csatlakoztathatják az NFS-meghajtót a helyi címtárhoz.

Íme egy példa kubernetes definíció az állandó kötet (Ez a definíció feltételezi, hogy a fürt és a virtuális gép ugyanabban a virtuális hálózatban):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Cserélje le **a NFS_INTERNAL_IP**, **NFS_NAME** és **NFS_EXPORT_FILE_PATH** NFS-kiszolgáló adataira.

Állandó kötetjogcímfájlra is szüksége lesz. Íme egy példa arra, hogy mit kell tartalmaznia:

>[!IMPORTANT]  
>**A "storageClassName"** karakterláncnak üres karakterláncnak kell maradnia, különben a jogcím nem fog működni.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Hibaelhárítás
Ha fürtből nem tud csatlakozni a kiszolgálóhoz, lehet, hogy az exportált könyvtár vagy a szülője nem rendelkezik a kiszolgáló eléréséhez szükséges engedélyekkel.

Ellenőrizze, hogy mind az exportkönyvtár, mind a szülőkönyvtár 777-es engedéllyel rendelkezik-e.

Ellenőrizheti engedélyek futtatásával a parancsot az alábbi és a könyvtárakat kell *"drwxrwxrwx"* engedélyek:
```console
ls -l
```

## <a name="more-information"></a>További információ
A teljes forgatókönyv höz vagy az NFS-kiszolgáló telepítésének hibakereséséhez az alábbiakban részletes encikázst talál:
  - [NFS-oktatóanyag][nfs-tutorial]

## <a name="next-steps"></a>További lépések

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a tároláshoz és a biztonsági mentések az AKS-ben.][operator-best-practices-storage]

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
