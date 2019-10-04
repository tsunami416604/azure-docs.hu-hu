---
title: Az Azure Storage használata Terraform-háttérként
description: Bevezetés az Azure Storage-beli Terraform-állapotának tárolására.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: e9b447f4f4dc9d0ee090da9729e483cc17ac7c15
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169945"
---
# <a name="store-terraform-state-in-azure-storage"></a>Terraform-állapot tárolása az Azure Storage-ban

A Terraform állapota az üzembe helyezett erőforrások Terraform-konfigurációkkal való összeegyeztetésére szolgál. Az állapot használatával a Terraform tudja, hogy mely Azure-erőforrások hozzáadására, frissítésére vagy törlésére van lehetőség. Alapértelmezés szerint a Terraform állapota helyileg tárolódik a *Terraform*futtatásakor. Ez a konfiguráció néhány okból nem ideális:

- A helyi állapot nem működik megfelelően egy csapatban vagy együttműködési környezetben
- A Terraform-állapot bizalmas adatokat is tartalmazhat
- Az állapot helyi tárolása növeli a véletlen törlés esélyét

A Terraform tartalmazza az állapotjelző háttér fogalmát, amely a Terraform-állapot távoli tárolója. Állapotjelző háttérrendszer használatakor az állapotjelző fájl egy adattárba, például az Azure Storage-ba van tárolva. Ez a dokumentum részletesen ismerteti, hogyan konfigurálhatja és használhatja az Azure Storage-t Terraform állapot-háttérként.

## <a name="configure-storage-account"></a>Storage-fiók konfigurálása

Az Azure Storage háttérként való használata előtt létre kell hoznia egy Storage-fiókot. A Storage-fiók a Azure Portal, a PowerShell, az Azure CLI vagy a Terraform használatával hozható létre. A következő minta használatával konfigurálja a Storage-fiókot az Azure CLI-vel.

```azurecli
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

Jegyezze fel a Storage-fiók nevét, a tároló nevét és a tároló elérési kulcsát. Ezek az értékek a távoli állapot konfigurálásakor szükségesek.

## <a name="configure-state-backend"></a>Állapotjelző háttér konfigurálása

A Terraform állapotának háttere a *Terraform init*futtatásakor van konfigurálva. Az állapot-háttér konfigurálásához a következő adatértékekre van szükség.

- storage_account_name – az Azure Storage-fiók neve.
- container_name – a blob-tároló neve.
- kulcs – a létrehozandó State Store-fájl neve.
- access_key – a tárolási hozzáférési kulcs.

Mindegyik érték megadható a Terraform konfigurációs fájljában vagy a parancssorban, de ajánlott környezeti változót használni a `access_key`alkalmazáshoz. Környezeti változó használata megakadályozza a kulcs lemezre írását.

Hozzon létre egy nevű `ARM_ACCESS_KEY` környezeti változót az Azure Storage hozzáférési kulcsának értékével.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Az Azure Storage-fiók elérési kulcsának további védeleméhez tárolja azt Azure Key Vault. Ezután a környezeti változó a következőhöz hasonló paranccsal állítható be. A Azure Key Vaultről a [Azure Key Vault dokumentációjában][azure-key-vault]talál további információt.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Ha a Terraform-t a háttér használatára szeretné konfigurálni, adjon meg egy *háttér* -konfigurációt egy *azurerm* a Terraform-konfiguráción belül. Adja hozzá a *storage_account_name*, a *container_name*és a *kulcs* értékeit a konfigurációs blokkhoz.

A következő példa egy Terraform-hátteret konfigurál, és létrehoz egy Azure-erőforráscsoportot. Cserélje le az értékeket a környezetében lévő értékekre.

```hcl
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

Ezután inicializálja a konfigurációt a *Terraform init* használatával, majd futtassa a konfigurációt a *Terraform alkalmazással*. Ha elkészült, megkeresheti az állapot fájlt a Azure Storage Blobban.

## <a name="state-locking"></a>Állapot zárolása

Ha Azure Storage Blob használ az állapot-tároláshoz, a rendszer automatikusan zárolja a blobot az állapotot írási művelet előtt. Ez a konfiguráció megakadályozza a több párhuzamos állapotú műveletet, ami sérülést okozhat. További információkért lásd: az [állapot zárolása][terraform-state-lock] a Terraform dokumentációjában.

A zárolás látható a blob Azure Portal vagy más Azure Management-eszközön keresztüli vizsgálatakor.

![Azure-Blob zárolással](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Alapértelmezés szerint az Azure-Blobokban tárolt adattárolók titkosítva maradnak a tárolási infrastruktúra megőrzése előtt. Ha a Terraform igények állapota, a rendszer lekéri a háttérből, és a fejlesztői rendszeren tárolt memóriában tárolja azt. Ebben a konfigurációban az állapot az Azure Storage-ban védett, és nem íródik a helyi lemezre.

Az Azure Storage titkosításával kapcsolatos további információkért lásd: [Azure-Storage Service encryption a REST-adatokhoz][azure-storage-encryption].

## <a name="next-steps"></a>További lépések

További információ a Terraform háttér-konfigurációról a [Terraform háttérbeli dokumentációjában][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
