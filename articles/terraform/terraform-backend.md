---
title: Oktatóanyag – Terraform állapot ának tárolása az Azure Storage-ban
description: Bevezetés a Terraform állapot azure Storage-ban való tárolásához.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75708424"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Oktatóanyag: Terraform állapot ának tárolása az Azure Storage-ban

A Terraform állapot az üzembe helyezett erőforrások és a Terraform-konfigurációk egyeztetésére szolgál. Az állapot lehetővé teszi a Terraform számára, hogy megtudja, milyen Azure-erőforrásokat adjon hozzá, frissítsen vagy töröljön. Alapértelmezés szerint a Terraform állapot helyileg tárolódik a `terraform apply` parancs futtatásakor. Ez a konfiguráció nem ideális a következő okok miatt:

- A helyi állam nem működik jól csapat- vagy együttműködési környezetben.
- A terraform állapot bizalmas információkat tartalmazhat.
- Az állapot helyi tárolása növeli a véletlen törlés esélyét.

A Terraform támogatja az állapot megőrzését a távtárolóban. Az egyik ilyen támogatott háttérrendszer az Azure Storage. Ez a dokumentum bemutatja, hogyan konfigurálhatja és használhatja az Azure Storage-ot erre a célra.

## <a name="configure-storage-account"></a>Tárfiók konfigurálása

Mielőtt háttérrendszerként használná az Azure Storage-t, létre kell hoznia egy tárfiókot. A tárfiók az Azure Portalon, a PowerShell, az Azure CLI vagy a Terraform önmagával hozható létre. A következő minta segítségével konfigurálja a tárfiókot az Azure CLI.Use the following sample to configure the storage account with the Azure CLI.

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

Vegye figyelembe a tárfiók nevét, a tároló nevét és a tárolási hozzáférési kulcsot. Ezekre az értékekre a távoli állapot konfigurálásakor van szükség.

## <a name="configure-state-back-end"></a>Állapot háttérkiszolgálókonfigurálása

A Terraform állapot háttérvége a `terraform init` parancs futtatásakor van konfigurálva. Az állapot háttérrendszerének konfigurálásához a következő adatok szükségesek:

- **storage_account_name**: Az Azure Storage-fiók neve.
- **container_name**: A blob tároló neve.
- **kulcs**: A létrehozandó állapottároló fájl neve.
- **access_key**: A tároló-hozzáférési kulcs.

Ezen értékek mindegyike megadható a Terraform konfigurációs fájlban vagy a parancssorban. Azt javasoljuk, hogy az `access_key` értékhez használjon környezeti változót. Egy környezeti változó használata megakadályozza a kulcs lemezre írását.

Hozzon létre `ARM_ACCESS_KEY` egy környezeti változó nevű az Azure Storage hozzáférési kulcs értékével.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Az Azure Storage-fiók hozzáférési kulcsának további védelme érdekében tárolja azt az Azure Key Vaultban. A környezeti változó ezután a következőhöz hasonló paranccsal állítható be. Az Azure Key Vaultról az [Azure Key Vault dokumentációjában](../key-vault/quick-create-cli.md)olvashat bővebben.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

A Terraform háttérkiszolgáló használatára történő beállításához a következő lépéseket kell elvégezni:
- A `backend` típushoz kapcsolódó konfigurációs `azurerm`blokk felvétele.
- Adjon `storage_account_name` hozzá egy értéket a konfigurációs blokkhoz.
- Adjon `container_name` hozzá egy értéket a konfigurációs blokkhoz.
- Adjon `key` hozzá egy értéket a konfigurációs blokkhoz.

A következő példa konfigurálja a Terraform háttérkiszolgálót, és létrehoz egy Azure-erőforráscsoportot.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
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

Inicializálja a konfigurációt a következő lépésekkel:

1. Futtassa a következő parancsot: `terraform init`.
1. Futtassa a következő parancsot: `terraform apply`.

Most már megtalálhatja az állapotfájlt az Azure Storage blobban.

## <a name="state-locking"></a>Állapotzárolás

Az Azure Storage-blobok automatikusan zárolva vannak, mielőtt bármilyen művelet, amely írási állapotot. Ez a minta megakadályozza az egyidejű állapotműveleteket, amelyek sérüléshez vezethetnek. 

További információt a Terraform dokumentációjában található [Állapotzárolás](https://www.terraform.io/docs/state/locking.html) című témakörben talál.

Láthatja a zárolást, amikor megvizsgálja a blobot az Azure Portalon vagy más Azure-felügyeleti eszközökön keresztül.

![Azure blob zárolással](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az Azure blobban tárolt adatok titkosítva vannak, mielőtt megmaradnának. Szükség esetén a Terraform lekéri az állapotot a háttérből, és a helyi memóriában tárolja. Ezzel a mintával az állapot soha nem kerül a helyi lemezre.

Az Azure Storage titkosításáról az [Azure Storage szolgáltatás titkosítása az inaktív adatokról](../storage/common/storage-service-encryption.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform azure-beli használatáról](/azure/terraform)
