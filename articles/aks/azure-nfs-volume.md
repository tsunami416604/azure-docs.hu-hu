---
title: Az NFS (NFS) Ubuntu Server podok Azure Kubernetes Service (AKS) általi használatra
description: Ismerje meg, hogyan hozhat létre manuálisan egy NFS Ubuntu Linux Server kötetet podok Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468499"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Manuális módszerrel hozzon létre, és az NFS (NFS) Linux-kiszolgáló köteten az Azure Kubernetes Service (AKS)
Tárolók közötti adatmegosztás legtöbbször a tároló-alapú szolgáltatások és alkalmazások egy szükséges összetevő. Általában rendelkeznek különböző podok, amely ugyanazt az információt egy külső tartós kötet hozzáférésre van szükségük.    
Az Azure files-beállítás, amelyek állandó megosztott tároló egy másik formája NFS-kiszolgáló létrehozása az Azure virtuális gép. 

Ebből a cikkből megtudhatja, hogyan hozhat létre az NFS-kiszolgáló egy Ubuntu virtuális gépen. És az AKS-tárolók hozzáférést is adhat a megosztott fájlrendszert.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Az AKS-fürt kell él, az NFS-kiszolgáló ugyanabban vagy két társviszonyban álló virtuális hálózatok. A fürt létre kell hozni egy meglévő virtuális hálózatban, amely ugyanabban a virtuális Hálózatban, mint a virtuális gép lehet.

Egy meglévő virtuális hálózattal konfigurálásának lépéseit ismerteti a dokumentáció: [AKS-fürt létrehozása meglévő virtuális hálózatban] [ aks-virtual-network] és [a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok összekapcsolása][peer-virtual-networks]

Azt is feltételezi, hogy létrehozott egy Ubuntu Linux virtuális gép (például 18.04 LTS). Beállítások és a méret tetszőlegesen a és Azure-on keresztül telepíthető. Linuxos rövid útmutatóval, lásd: [Linux rendszerű virtuális gépek felügyelete][linux-create].

Ha először telepíti az AKS-fürt, Azure automatikusan feltölti a virtuális hálózat mező az Ubuntu-gép üzembe helyezésekor teszi őket a élő ugyanazon a VNETEN belül. Azonban ha azt szeretné, ehelyett társviszonyban lévő hálózatok dolgozhat, tekintse át a fenti dokumentációt.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Az NFS-kiszolgáló virtuális gépen való üzembe helyezése
Íme a szkript állíthatja be az NFS-kiszolgáló, az Ubuntu virtuális gépen:
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
A kiszolgáló újraindul (miatt a parancsfájl), és akkor is csatlakoztathatja az NFS-kiszolgáló az aks-ben

>[!IMPORTANT]  
>Cserélje le a **AKS_SUBNET** a megfelelőt a fürtből a vagy "*" megnyílik az NFS-kiszolgáló összes portokra és kapcsolatokat.

A virtuális gép létrehozása után másolja a fenti a parancsfájlt egy fájlba. Ezt követően továbbléphet a helyi gépen, vagy a bárhol is legyenek a parancsfájlt, azokat a virtuális Gépet az: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Miután a szkript a virtuális gépen, lehet ssh a virtuális géppel és hajtható végre, a parancs segítségével:
```console
sudo ./nfs-server-setup.sh
```
A végrehajtási engedély megtagadását jelző hiba miatt nem sikerül, ha állítsa be a végrehajtási engedélyt a parancs segítségével:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>NFS-kiszolgáló csatlakozó AKS-fürt
Az NFS-kiszolgáló azt kapcsolódjon a fürthöz, tartós kötet és tartós kötet jogcímet, amely meghatározza, hogy a kötet eléréséhez üzembe helyezése.  
Az azonos vagy társviszonyban lévő virtuális hálózatok a két szolgáltatás összekapcsolása szükség. Itt van a fürt ugyanazon a vneten beállításával kapcsolatos útmutatás: [AKS-fürt létrehozása meglévő virtuális hálózaton][aks-virtual-network]

Ha azok az azonos virtuális hálózatban lévő (vagy társviszonyban álló), meg kell üzembe helyezni egy tartós kötet és a egy tartós kötet az AKS-fürt. A tárolók is csatlakoztathatja az NFS-meghajtó a helyi könyvtárba.

Íme egy példa kubernetes-definíciót a tartós kötet (Ez a definíció feltételezi, hogy a fürt és a virtuális gép ugyanazon a vneten vannak):

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
Cserélje le **NFS_INTERNAL_IP**, **NFS_NAME** és **NFS_EXPORT_FILE_PATH** NFS-kiszolgáló adataival.

Tartós kötet jogcím fájl is szüksége lesz. A következő példa bemutatja, mit tartalmazza:

>[!IMPORTANT]  
>**"storageClassName"** frissülnie kell egy üres karakterlánc vagy a jogcím nem fog működni.

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
A fürthöz a kiszolgáló nem tud csatlakozni, ha a probléma lehet, hogy az exportált könyvtár vagy a fölérendelt, nem rendelkezik megfelelő engedélyekkel a kiszolgálóhoz való hozzáféréshez.

Ellenőrizze, hogy az exportálás és a szülő könyvtárat is 777 engedélyekkel rendelkezzen.

Engedélyek az alábbi parancs futtatásával ellenőrizheti, és tartalmaznia kell a könyvtárak *"drwxrwxrwx"* engedélyek:
```console
ls -l
```

## <a name="more-information"></a>További információ
A teljes forgatókönyv vagy segítséget nyújtanak az NFS-kiszolgáló beállítása hibakeresése, Íme egy részletes oktatóanyagot:
  - [NFS-oktatóanyag][nfs-tutorial]

## <a name="next-steps"></a>További lépések

További kapcsolódó ajánlott eljárások: [ajánlott eljárások a storage és az aks-ben biztonsági mentések][operator-best-practices-storage].

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
