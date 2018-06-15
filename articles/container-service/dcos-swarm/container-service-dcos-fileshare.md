---
title: Azure DC/OS-fürtről fájlmegosztásához
description: Hozzon létre, és azt csatlakoztatja a fájlmegosztást a DC/OS fürtben, az Azure Tárolószolgáltatásban
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
ms.locfileid: "26331202"
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Hozzon létre, és azt csatlakoztatja a fájlmegosztást a DC/OS-fürtről

Ez az oktatóanyag fájlmegosztás létrehozása az Azure-ban, és csatlakoztassa azt minden ügynök és a DC/OS-fürt fő részletezi. Fájlmegosztás beállítása megkönnyíti a fürt, például a konfigurációs, access, naplók és egyéb fájlok megosztása. Ebben az oktatóanyagban a következő műveleteket foglalja:

> [!div class="checklist"]
> * Azure-tárfiók létrehozása
> * Fájlmegosztás létrehozása
> * A DC/OS fürtben a megosztás csatlakoztatásához

Az ACS DC/OS-fürt az oktatóanyag lépéseinek végrehajtásához van szüksége. Ha szükséges, [a parancsfájl minta](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) hozhat létre egyet.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Fájlmegosztás létrehozása a Microsoft Azure

Az Azure fájlmegosztások használ egy ACS DC/OS-fürtről, mielőtt a storage-fiókot és -fájlmegosztást kell létrehozni. Az alábbi parancsprogrammal hozzon létre a tároló és a fájlmegosztást. Frissítse a paraméterek thoes a környezetből.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>A fürt a megosztás csatlakoztatásához

Ezután a fájlmegosztást kell csatlakoztatni kell a fürtben található összes virtuális gépen. Ez a feladat befejeződött, a cifs eszköz/protokoll használatával. A szalagcsatlakoztatási művelet manuálisan minden csomóponton, a fürt, vagy a fürt minden csomópontja elleni parancsfájl futtatásával is elvégezhető.

Ebben a példában két parancsfájlok, egy csatlakoztatása az Azure-fájlmegosztáshoz, és egy második, a parancsfájl futtatásához a DC/OS-fürt mindegyik csomópontján.

Először a az Azure storage-fiók neve és elérési kulcs van szükség. Ezek az információk beolvasása a következő parancsok futtatásával. Jegyezze fel az egyes, ezeket az értékeket használni egy későbbi lépésben.

Tárfiók nevét:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Tárfiók hozzáférési kulcsának:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

A következő beolvasása a DC/OS fő teljes Tartománynevét, és tárolható egy változóban.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Másolja a titkos kulcsot a fő csomópont. Ez a kulcs létrehozásához szükséges egy ssh-kapcsolatot a fürt összes csomópontján. Frissítse a felhasználó nevét, ha egy nem alapértelmezett érték lett megadva, a fürt létrehozásakor. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Az SSH-kapcsolat létrehozása a főkiszolgáló (vagy az első főkiszolgálójának) a DC/OS-alapú fürt. Frissítse a felhasználó nevét, ha egy nem alapértelmezett érték lett megadva, a fürt létrehozásakor.

```azurecli-interactive
ssh azureuser@$FQDN
```

Hozzon létre egy fájlt **cifsMount.sh**, és a következő tartalom másolása. 

Ez a parancsfájl Azure fájlmegosztás csatlakoztatásához használatos. Frissítés a `STORAGE_ACCT_NAME` és `ACCESS_KEY` korábban összegyűjtött változóit.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Hozzon létre egy második fájlt **getNodesRunScript.sh** és másolja az alábbiakat a fájlba. 

Ez a parancsfájl deríti fel a fürt összes csomópontján, és majd futtatja a **cifsMount.sh** parancsfájl minden egyes a fájlmegosztás csatlakoztatásához.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Futtassa a parancsfájlt a fürt összes csomópontján Azure fájlmegosztás csatlakoztatásához.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

A fájlmegosztás mostantól elérhető a `/mnt/share/dcosshare` a fürt mindegyik csomópontján.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure fájlmegosztás lett elérhetővé tenni az a DC/OS fürtben, a lépéseket követve:

> [!div class="checklist"]
> * Azure-tárfiók létrehozása
> * Fájlmegosztás létrehozása
> * A DC/OS fürtben a megosztás csatlakoztatásához

A következő oktatóanyag további információt az Azure-tároló beállításjegyzék integrálása az Azure-ban a DC/OS továbblépés.  

> [!div class="nextstepaction"]
> [Terheléselosztási alkalmazások](container-service-dcos-acr.md)
