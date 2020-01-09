---
title: Azure CLI-parancsok futtatása Azure AD-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez
titleSuffix: Azure Storage
description: Az Azure CLI támogatja az Azure AD-beli hitelesítő adatokkal való bejelentkezést, hogy parancsokat futtasson az Azure Storage blob és a Queues adatain. A munkamenethez hozzáférési token van megadva, és a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági tag által hozzárendelt RBAC-szerepkörtől függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553461"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure CLI-parancsok futtatása Azure AD-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez

Az Azure Storage olyan bővítményeket biztosít az Azure CLI-hez, amelyek lehetővé teszik a Azure Active Directory (Azure AD) hitelesítő adatokkal való bejelentkezést és parancsfájlok futtatását. Ha Azure AD-beli hitelesítő adatokkal jelentkezik be az Azure CLI-be, az OAuth 2,0 hozzáférési tokent ad vissza. Ezt a tokent a rendszer automatikusan használja az Azure CLI-vel, hogy engedélyezze a további adatműveleteket a blob vagy a várólista-tárolón. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Az Azure AD rendszerbiztonsági tag számára a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet a blob-és üzenetsor-szolgáltatásokhoz. Az Azure Storage RBAC szerepköreivel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz a RBAC használatával](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Támogatott műveletek

Az Azure Storage-bővítmények támogatottak a blob-és üzenetsor-adatokon végzett műveletekhez. A hívható műveletek attól függnek, hogy az Azure AD rendszerbiztonsági tag milyen engedélyekkel rendelkezik, amelyekkel bejelentkezhet az Azure CLI-be. Az Azure Storage-tárolók vagy-várólisták engedélyei a RBAC-on keresztül vannak hozzárendelve. Ha például a **blob-Adatolvasó** szerepkört rendeli hozzá, akkor futtathat parancsfájl-parancsokat, amelyek egy tárolóból vagy várólistából olvasnak be egy adatforrást. Ha a **blob adatközreműködői** szerepkört rendelte hozzá, akkor futtathat olyan parancsfájl-parancsokat, amelyek egy tárolót vagy várólistát, illetve a bennük található adat olvasását, írását vagy törlését írják le.

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a tárolón vagy a várólistán: [tárolási műveletek hívása OAuth-jogkivonatokkal](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Azure CLI-parancsok meghívása az Azure AD hitelesítő adataival

Az Azure CLI támogatja a blob-és üzenetsor-műveletek `--auth-mode` paraméterét:

- Állítsa be a `--auth-mode` paramétert úgy, hogy `login` az Azure AD rendszerbiztonsági tag használatával történő bejelentkezéshez.
- A `--auth-mode` paramétert állítsa a régi `key` értékre, hogy a rendszer megkísérelje a fiók kulcsának lekérdezését, ha nincs megadva hitelesítési paraméter a fiókhoz.

Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót egy új Storage-fiókban az Azure CLI-vel az Azure AD-beli hitelesítő adataival. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

1. Győződjön meg arról, hogy telepítette az Azure CLI 2.0.46 vagy újabb verzióját. `az --version` futtatásával keresse meg a telepített verziót.

1. `az login` futtatása és hitelesítés a böngészőablakban:

    ```azurecli
    az login
    ```

1. Adja meg a kívánt előfizetést. Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. Hozzon létre egy Storage-fiókot az adott erőforráscsoporthoz az [az Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)paranccsal:

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. A tároló létrehozása előtt rendelje hozzá a [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magának. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. A RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés biztosítása az Azure blobhoz és üzenetsor-adatokhoz a Azure Portal RBAC](storage-auth-aad-rbac.md)használatával.

    > [!IMPORTANT]
    > A RBAC szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Az Azure AD-beli hitelesítő adataival hozzon létre egy tárolót az az [Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) paranccsal a `--auth-mode` paraméterrel, amely `login`re van állítva:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

A `--auth-mode` paraméterhez társított környezeti változó `AZURE_STORAGE_AUTH_MODE`. Megadhatja a megfelelő értéket a környezeti változóban, így elkerülhető, hogy az egy Azure Storage-adatműveletre irányuló összes híváson keresztül történjen.

## <a name="next-steps"></a>Következő lépések

- [Az Azure CLI használata RBAC-szerepkör hozzárendeléséhez a blob-és üzenetsor-adathoz való hozzáféréshez](storage-auth-aad-rbac-cli.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](storage-auth-aad-msi.md)
