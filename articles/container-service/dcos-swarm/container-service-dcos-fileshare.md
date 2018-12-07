---
title: (ELAVULT) Azure DC/OS fürt fájlmegosztás
description: Fájlmegosztás létrehozása és csatlakoztatása DC/OS-fürthöz az Azure Container Service-ben
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 728f8ddbda35f27c0f89e1d72e98fd2d7669320e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999862"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(ELAVULT) Létrehozása és csatlakoztatása DC/OS fürt fájlmegosztás

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ez az oktatóanyag részletesen ismerteti, hogyan hozhat létre fájlmegosztást az Azure-ban, és hogyan csatlakoztathatja azt a DC/OS-fürtök egyes ügynökein és főkiszolgálóin. Fájlmegosztás beállítása megkönnyíti a fájlok, például a konfigurációk, hozzáférési jogosultságok, naplók és egyebek megosztását a fürtben. Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Azure-tárfiók létrehozása
> * Fájlmegosztás létrehozása
> * A fürt csatlakoztatása a DC/OS-fürtben

Az oktatóanyagban ismertetett lépések végrehajtásához szüksége lesz egy ACS DC/OS-fürtre. Amennyiben szükséges, [ezzel a mintaszkripttel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) létrehozhat egyet.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne, tekintse meg [az Azure CLI telepítését ismertető]( /cli/azure/install-azure-cli) szakaszt. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Fájlmegosztás létrehozása a Microsoft Azure-ban

Mielőtt Azure-fájlmegosztást használna egy ACS DC/OS-fürtben, létre kell hoznia a tárfiókot és a fájlmegosztást. A tárat és a fájlmegosztást a következő szkripttel hozhatja létre. A paramétereket cserélje le a saját környezetéből származókkal.

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

## <a name="mount-the-share-in-your-cluster"></a>A megosztás csatlakoztatása a fürtben

Ezután a fájlmegosztást csatlakoztatnia kell a fürtön belül minden virtuális gépen. Ezt a feladatot a cifs eszközzel/protokollal hajthatja végre. A csatlakoztatási művelet elvégezhető manuálisan a fürt egyes csomópontjain vagy szkript futtatásával a fürt csomópontjain.

Ebben a példában két szkriptet futtatunk, egyet az Azure-fájlmegosztás csatlakoztatásához, a másikat pedig ennek a szkriptnek a DC/OS-fürt egyes csomópontjain való futtatásához.

Először az Azure Storage-fiók nevére és hozzáférési kulcsára lesz szükség. Ezeknek az adatoknak a beszerzéséhez futtassa a következő parancsokat. Jegyezze fel ezek mindegyikét, egy későbbi lépésben szükség lesz rájuk.

Tárfiók neve:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Tárfiók hozzáférési kulcsa:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Ez után kérje le a DC/OS-főkiszolgáló teljes tartománynevét, és tárolja egy változóban.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Másolja a titkos kulcsot a főcsomópontba. Erre a kulcsra szükség lesz a fürt összes csomópontjával létrehozott ssh-kapcsolat kialakításához. Amennyiben a fürt eredeti létrehozása alkalmával nem alapértelmezett értéket használt, frissítse a felhasználónevet. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Hozzon létre SSH-kapcsolatot a DC/OS-alapú fürt főkiszolgálójával (vagy az első főkiszolgálójával). Amennyiben a fürt eredeti létrehozása alkalmával nem alapértelmezett értéket használt, frissítse a felhasználónevet.

```azurecli-interactive
ssh azureuser@$FQDN
```

Hozzon létre egy fájlt **cifsMount.sh** néven, és másolja bele a következő tartalmat. 

Ez a szkript az Azure-fájlmegosztás csatlakoztatása szolgál. Frissítse a `STORAGE_ACCT_NAME` és az `ACCESS_KEY` változókat a korábban beszerzett adatokkal.

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
Hozzon létre egy második fájlt **getNodesRunScript.sh** névvel, és másolja az alábbi tartalmat a fájlba. 

Ez a szkript felderíti az összes fürtcsomópontot, és ezután futtatja a **cifsMount.sh** szkriptet a fájlmegosztás csomópontokban történő csatlakoztatásához.

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

Futtassa a szkriptet az Azure-fájlmegosztásnak a fürt összes csomópontján való csatlakoztatásához.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

A fájlmegosztás mostantól elérhető az `/mnt/share/dcosshare` útvonalon a fürt mindegyik csomópontján.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban elérhetővé tettünk egy Azure-fájlmegosztás a DC/OS-fürtben a következő lépések követésével:

> [!div class="checklist"]
> * Azure-tárfiók létrehozása
> * Fájlmegosztás létrehozása
> * A fürt csatlakoztatása a DC/OS-fürtben

A következő oktatóanyagban megtudhatja, hogyan integrálhatja az Azure Container Registryt a DC/OS szolgáltatással az Azure-ban.  

> [!div class="nextstepaction"]
> [Terheléselosztási alkalmazások](container-service-dcos-acr.md)
