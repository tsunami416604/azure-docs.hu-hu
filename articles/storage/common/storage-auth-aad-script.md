---
title: Azure CLI-vagy PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a blob vagy a várólista adatainak eléréséhez | Microsoft Docs
description: Az Azure CLI és a PowerShell támogatja az Azure AD-beli hitelesítő adatokkal való bejelentkezést az Azure Storage blob és a Queues adatainak futtatásához. A munkamenethez hozzáférési token van megadva, és a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági tag által hozzárendelt RBAC-szerepkörtől függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e1c7f4531dee8673cc5b6dfe675e4c793144931e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671091"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure CLI-vagy PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez

Az Azure Storage olyan bővítményeket biztosít az Azure CLI és a PowerShell számára, amelyek lehetővé teszik a Azure Active Directory (Azure AD) hitelesítő adatokkal való bejelentkezést és parancsfájlok futtatását. Ha Azure AD-beli hitelesítő adatokkal jelentkezik be az Azure CLI-be vagy a PowerShellbe, egy OAuth 2,0 hozzáférési tokent ad vissza. A CLI vagy a PowerShell automatikusan használja ezt a tokent, hogy engedélyezze a további adatműveleteket a blob vagy a várólista-tárolón. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Az Azure AD rendszerbiztonsági tag számára a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet a blob-és üzenetsor-szolgáltatásokhoz. Az Azure Storage RBAC szerepköreivel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz a RBAC használatával](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Támogatott műveletek

A bővítmények támogatottak a tárolók és a várólisták műveleteihez. A felhívható műveletek attól függnek, hogy az Azure AD rendszerbiztonsági tag milyen engedélyekkel rendelkezik, amelyekkel bejelentkezhet az Azure CLI-be vagy a PowerShellbe. Az Azure Storage-tárolók vagy-várólisták engedélyei szerepköralapú hozzáférés-vezérléssel (RBAC) vannak társítva. Ha például a **blob** -Adatolvasó szerepkört rendeli hozzá, akkor futtathat parancsfájl-parancsokat, amelyek egy tárolóból vagy várólistából olvasnak be egy adatforrást. Ha a **blob adatközreműködői** szerepkört rendelte hozzá, akkor futtathat olyan parancsfájl-parancsokat, amelyek egy tárolót vagy várólistát, illetve a bennük található adat olvasását, írását vagy törlését írják le. 

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a tárolón vagy a várólistán: [tárolási műveletek hívása OAuth](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)-jogkivonatokkal.  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>CLI-parancsok hívása az Azure AD hitelesítő adataival

Az Azure CLI támogatja `--auth-mode` a blob-és üzenetsor-adatok műveletének paraméterét:

- Állítsa be `--auth-mode` a `login` paramétert úgy, hogy bejelentkezzen egy Azure ad rendszerbiztonsági tag használatával.
- Állítsa a `--auth-mode` paramétert az örökölt `key` értékre, hogy a rendszer megkísérelje a fiók kulcsának lekérdezését, ha nincs megadva hitelesítési paraméter a fiókhoz. 

Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót egy új Storage-fiókban az Azure CLI-vel az Azure AD-beli hitelesítő adataival. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben: 

1. Győződjön meg arról, hogy telepítette az Azure CLI 2.0.46 vagy újabb verzióját. Futtassa `az --version` a parancsot a telepített verziójának vizsgálatához.

1. Futtatás `az login` és hitelesítés a böngészőablakban: 

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

1. Az Azure ad-beli hitelesítő adataival `--auth-mode` hozza létre a `login` tárolót az az [Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) paranccsal, a következő paraméterrel:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

`--auth-mode` A`AZURE_STORAGE_AUTH_MODE`paraméterhez társított környezeti változó. Megadhatja a megfelelő értéket a környezeti változóban, így elkerülhető, hogy az egy Azure Storage-adatműveletre irányuló összes híváson keresztül történjen.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-parancsok meghívása az Azure AD hitelesítő adataival

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a Azure PowerShell használatával szeretne bejelentkezni, és az Azure Storage-ban az Azure Storage-ban további műveleteket hajt végre az Azure AD hitelesítő adataival, hozzon létre egy tárolási környezetet a Storage-fiókra való hivatkozáshoz, és adja meg a @no__t-

Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót egy új Storage-fiókban Azure PowerShell az Azure AD-beli hitelesítő adataival. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

1. Jelentkezzen be az Azure-fiókjába a [Kapcsolódás-AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal:

    ```powershell
    Connect-AzAccount
    ```

    További információ az Azure-ba történő bejelentkezésről a PowerShell használatával: [bejelentkezés Azure PowerShellsal](/powershell/azure/authenticate-azureps).

1. Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)hívásával. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Hozzon létre egy Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)hívásával.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Szerezze be a Storage-fiók környezetét, amely az új Storage-fiókot adja meg a [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)hívásával. Storage-fiók esetén a hitelesítő adatok ismételt átadása helyett hivatkozhat a környezetre. Az Azure `-UseConnectedAccount` ad-beli hitelesítő adataival a következő adatműveletek meghívásához használja a paramétert:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. A tároló létrehozása előtt rendelje hozzá a [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magának. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. A RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés biztosítása az Azure blobhoz és üzenetsor-adatokhoz a Azure Portal RBAC](storage-auth-aad-rbac.md)használatával.

    > [!IMPORTANT]
    > A RBAC szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Hozzon létre egy tárolót a [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)hívásával. Mivel ez a hívás az előző lépésekben létrehozott környezetet használja, a tároló az Azure AD-beli hitelesítő adataival jön létre. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Storage RBAC szerepköreiről, tekintse meg a [hozzáférési jogosultságok kezelése a RBAC](storage-auth-aad-rbac.md)szolgáltatással című témakört.
- További információ az Azure-erőforrások felügyelt identitások használatáról az Azure Storage-ban: a [blobok és várólisták hozzáférésének hitelesítése az Azure-erőforrások Azure Active Directory és felügyelt identitásával](storage-auth-aad-msi.md).
- Ha szeretné megtudni, hogyan engedélyezheti a tárolók és a várólisták hozzáférését a Storage-alkalmazásokban, tekintse meg az [Azure ad és a Storage-alkalmazások használatát](storage-auth-aad-app.md)ismertető témakört.
