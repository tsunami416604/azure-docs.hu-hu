---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469495"
---
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Create a storage account

Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Tárfiók hitelesítő adatainak megadása

Az Azure CLI az oktatóanyagban szereplő parancsok legtöbbje esetén elkéri a tárfiók hitelesítő adatait. Ennek végrehajtására több lehetőség is rendelkezésre áll, amelyek közül az egyik legegyszerűbb az `AZURE_STORAGE_ACCOUNT` és az `AZURE_STORAGE_KEY` környezeti változó beállítása.

> [!NOTE]
> Ez a cikk bemutatja, hogyan állíthatja be a környezeti változókat a bash használatával. Más környezetekben szintaktikai módosításokra lehet szükség.

Először jelenítse meg a Storage-fiók kulcsait az az [Storage Account Keys List](/cli/azure/storage/account/keys) parancs használatával. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Ez után adja meg az `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_KEY` környezeti változókat. Ezt a bash rendszerhéjban teheti meg a `export` parancs használatával. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

A fiók-hozzáférési kulcsok Azure Portal használatával történő beolvasásával kapcsolatos további információkért lásd: a [Storage-fiók elérési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md).