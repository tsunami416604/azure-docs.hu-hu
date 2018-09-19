---
title: A Terraform háttérmoduljaként Azure Storage használata
description: Bevezetés az Azure Storage-ban a Terraform állapot tárolásához.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: article
ms.date: 09/13/2018
ms.author: nepeters
ms.openlocfilehash: 48351dc0351b7a717a610ac9552e11362707e150
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128016"
---
# <a name="store-terraform-state-in-azure-storage"></a>A Terraform állapot Store az Azure Storage-ban

A Terraform állapot egyeztethetők össze, Terraform konfigurációjú üzembe helyezett erőforrásokról történik. Állapot használata esetén a Terraform tudja, milyen Azure-erőforrások hozzáadása, frissítése vagy törlése. Alapértelmezés szerint a Terraform-állapot tárolása helyben, ha a futó *Terraform a alkalmazni*. Ez a konfiguráció nem ideális megoldást néhány okok miatt:

- Helyi állapota nem működik jól, a csapat vagy együttműködési környezet
- A Terraform állapot tartalmazhatnak bizalmas adatokat
- Állapot helyi tárolására megnő az esélye, véletlen törléstől

A Terraform beletartoznak a egy állam háttér, azaz a Terraform állapothoz távoli tároló. Egy állapot-háttérrendszer használatával, az állapot fájl tárolja a például az Azure Storage tárolóban. Ez a dokumentum részletesen bemutatja, konfigurálhatja és használhatja az Azure Storage a Terraform állapot háttérmoduljaként.

## <a name="configure-storage-account"></a>Storage-fiók konfigurálása

Az Azure Storage háttérmoduljaként, mielőtt egy storage-fiókot kell létrehoznia. A storage-fiókot az Azure portal, PowerShell, az Azure CLI-vel vagy maga a Terraform hozhatja létre. A következő minta használatával konfigurálja a storage-fiókot az Azure CLI használatával.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Jegyezze fel a tárfiók neve, a tároló neve és a tárfiók hozzáférési kulcsát. Ezekre az értékekre van szükség, a távoli állapot konfigurálásakor.

## <a name="configure-state-backend"></a>Konfigurálja a háttérrendszer állapota

A Terraform állapot háttérrendszer van konfigurálva, futtatásakor *Terraform init*. Az állapot háttérrendszer konfigurálásához a következő adatok megadása kötelező.

- tárfiók_neve – az Azure Storage-fiók nevére.
- container_name – a blobtároló nevét.
- kulcs – az állapot nevét tárolja a fájl jön létre.
- access_key – a tárelérési kulcs.

Ezeket az értékeket minden egyes adható meg a Terraform konfigurációs fájlban vagy a parancssorban, de javasoljuk, hogy a környezeti változóval a `access_key`. Környezeti változóval megakadályozza, hogy a kulcs ír lemezre.

Hozzon létre egy környezeti változó nevű `ARM_ACCESS_KEY` az Azure Storage elérési kulcs értékét.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Fokozott védelme érdekében az Azure-Tárfiók hozzáférési kulcsát, az Azure Key Vaultban tárolja. A környezeti változó majd lze nastavit pomocí vlastností a következőhöz hasonló parancsot. Az Azure Key Vault további információkért lásd: a [Azure Key Vault-dokumentáció][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

A háttéralkalmazás használatára a Terraform konfigurálása, adjon meg egy *háttérrendszer* típusú konfigurációs *azurerm* belül a Terraform konfigurálása. Adja hozzá a *tárfiók_neve*, *container_name*, és *kulcs* a konfigurációs blokk értékeket.

Az alábbi példa a háttérrendszernek a Terraform konfigurálása és hoz létre, és Azure-erőforráscsoportot. Az értékeket cserélje le a környezetből értékeket.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Most, inicializálja a konfigurációját a *Terraform init* , majd futtassa a konfiguráció *Terraform a alkalmazni*. Befejezése után az állapot fájl található az Azure Storage-Blobba.

## <a name="state-locking"></a>Állapot zárolása

Állapot tárolásához az Azure Storage Blob használja, ha a blob automatikus zárolása előtt írja az állapota. Ez a beállítás megakadályozza, hogy több egyidejű állapottal kapcsolatos műveletek, amely sérülést okozhat. További információkért lásd: [zárolási állapot] [ terraform-state-lock] a Terraform dokumentáció tartalmaz.

A zárolás vizsgálatakor a blobot, ha az Azure portal vagy más Azure eszközök láthatók.

![Az Azure blob-zárolással](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Alapértelmezés szerint egy Azure-blobban tárolt adatok titkosítása előtt, a tároló-infrastruktúra tároló. A Terraform állapotban van szüksége, ha a háttérrendszer lekért és a fejlesztői rendszeren a memóriában tárolt. Ebben a konfigurációban állapot védett Azure Storage-ban, és nem a helyi lemezre írt.

Azure Storage-titkosítással kapcsolatos további információkért lásd: [Azure Storage Service Encryption az inaktív adatok][azure-storage-encryption].

## <a name="next-steps"></a>További lépések

További információ a biztonsági Terraform-konfigurációjában az [Terraform háttérrendszer dokumentáció][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
