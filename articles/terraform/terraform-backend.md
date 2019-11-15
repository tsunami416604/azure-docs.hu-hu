---
title: Oktatóanyag – Terraform-állapot tárolása az Azure Storage-ban
description: Bevezetés az Azure Storage-beli Terraform-állapotának tárolására.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 374936c39221d79d59fc8a54dc2bc4a49800240d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078564"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Oktatóanyag: Terraform-állapot tárolása az Azure Storage-ban

A Terraform állapota az üzembe helyezett erőforrások Terraform-konfigurációkkal való összeegyeztetésére szolgál. Az állapot lehetővé teszi a Terraform számára, hogy megismerjék, milyen Azure-erőforrásokat kell hozzáadni, frissíteni vagy törölni. Alapértelmezés szerint a Terraform állapot a `terraform apply` parancs futtatásakor helyileg tárolódik. Ez a konfiguráció a következő okok miatt nem ideális:

- A helyi állapot nem működik megfelelően egy csapatban vagy együttműködési környezetben.
- A Terraform-állapot bizalmas adatokat is tartalmazhat.
- Az állapot helyi tárolása növeli a véletlen törlés esélyét.

A Terraform támogatja az állapot megőrzését a távoli tárolóban. Az egyik ilyen támogatott háttér az Azure Storage. Ebből a dokumentumból megtudhatja, hogyan konfigurálhatja és használhatja az Azure Storage-t erre a célra.

## <a name="configure-storage-account"></a>Storage-fiók konfigurálása

Az Azure Storage háttérbeli használata előtt létre kell hoznia egy Storage-fiókot. A Storage-fiók a Azure Portal, a PowerShell, az Azure CLI vagy a Terraform használatával hozható létre. A következő minta használatával konfigurálja a Storage-fiókot az Azure CLI-vel.

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

## <a name="configure-state-back-end"></a>Állapot visszaállításának beállítása

A Terraform állapota a `terraform init` parancs futtatásakor van konfigurálva. Az állapot háttérbeli konfigurálásához a következő adatértékekre van szükség:

- **storage_account_name**: az Azure Storage-fiók neve.
- **container_name**: a blob-tároló neve.
- **kulcs**: a létrehozandó állapot-tároló fájl neve.
- **access_key**: a tárolási hozzáférési kulcs.

Ezek az értékek a Terraform konfigurációs fájljában vagy a parancssorban adhatók meg. Javasoljuk, hogy környezeti változót használjon a `access_key` értékhez. Környezeti változó használata megakadályozza a kulcs lemezre írását.

Hozzon létre egy `ARM_ACCESS_KEY` nevű környezeti változót az Azure Storage hozzáférési kulcsának értékével.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Az Azure Storage-fiók elérési kulcsának további védeleméhez tárolja azt Azure Key Vault. A környezeti változó ezután a következőhöz hasonló paranccsal állítható be. A Azure Key Vaultről a [Azure Key Vault dokumentációjában](../key-vault/quick-create-cli.md)talál további információt.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

A Terraform a háttér használatára való konfigurálásához a következő lépéseket kell elvégeznie:
- `backend` konfigurációs blokkot adjon meg `azurerm`típussal.
- Adjon hozzá egy `storage_account_name` értéket a konfigurációs blokkhoz.
- Adjon hozzá egy `container_name` értéket a konfigurációs blokkhoz.
- Adjon hozzá egy `key` értéket a konfigurációs blokkhoz.

Az alábbi példa egy Terraform konfigurál, és létrehoz egy Azure-erőforráscsoportot.

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

A konfiguráció inicializálásához hajtsa végre a következő lépéseket:

1. Futtassa a következő parancsot: `terraform init`.
1. Futtassa a következő parancsot: `terraform apply`.

Most már megtalálhatja az állapotjelző fájlt az Azure Storage-blobban.

## <a name="state-locking"></a>Állapot zárolása

Az Azure Storage-Blobok automatikusan zárolva vannak, mielőtt bármilyen művelet beírja az állapotot. Ez a minta megakadályozza az egyidejű állapotú műveleteket, ami sérülést okozhat. 

További információ: [State locking](https://www.terraform.io/docs/state/locking.html) in the Terraform dokumentáció.

A zárolást akkor láthatja, ha a Azure Portal vagy más Azure Management-eszközön keresztül vizsgálja meg a blobot.

![Azure-Blob zárolással](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure-blobokban tárolt adatforgalom titkosítva marad. Ha szükséges, a Terraform lekérdezi az állapotot a háttérből, és a helyi memóriában tárolja. A minta használatával az állapot soha nem íródik a helyi lemezre.

További információ az Azure Storage-titkosításról: [Azure Storage szolgáltatás titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform Azure-beli használatáról](/azure/terraform)