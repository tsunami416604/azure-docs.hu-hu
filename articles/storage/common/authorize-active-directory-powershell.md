---
title: PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez
titleSuffix: Azure Storage
description: A PowerShell támogatja az Azure AD-beli hitelesítő adatokkal való bejelentkezést, hogy parancsokat futtasson az Azure Storage blob és a Queues adatain. A munkamenethez hozzáférési token van megadva, és a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági tag által hozzárendelt RBAC-szerepkörtől függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 80ca5b63a91da31a5b226a589e15fb202eabd4ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805777"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez

Az Azure Storage olyan bővítményeket biztosít a PowerShellhez, amelyek lehetővé teszik a Azure Active Directory-(Azure AD-) hitelesítő adatokkal való bejelentkezést és parancsfájlok futtatását. Ha Azure AD-beli hitelesítő adatokkal jelentkezik be a PowerShellbe, egy OAuth 2,0 hozzáférési tokent ad vissza. A PowerShell automatikusan használja ezt a tokent, hogy engedélyezze a további adatműveleteket a blob vagy a várólista-tárolón. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Az Azure AD rendszerbiztonsági tag számára a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet a blob-és üzenetsor-szolgáltatásokhoz. Az Azure Storage RBAC szerepköreivel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz a RBAC használatával](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Támogatott műveletek

Az Azure Storage-bővítmények támogatottak a blob-és üzenetsor-adatokon végzett műveletekhez. A felhívható műveletek az Azure AD rendszerbiztonsági tag által a PowerShellbe való bejelentkezéshez megadott engedélyektől függenek. Az Azure Storage-tárolók vagy-várólisták engedélyei a RBAC-on keresztül vannak hozzárendelve. Ha például hozzárendelte a **blob-Adatolvasó** szerepkört, futtathat parancsfájl-parancsokat, amelyek egy tárolóból vagy várólistából olvasnak be egy adatforrást. Ha hozzá lett rendelve a **blob-adatközreműködői** szerepkörhöz, futtathat olyan parancsfájl-parancsokat, amelyek egy tárolót vagy várólistát, illetve a bennük található adatkészleteket olvassák, írhatják vagy törölhetik.

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a tárolón vagy a várólistán: [tárolási műveletek hívása OAuth-jogkivonatokkal](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-parancsok meghívása az Azure AD hitelesítő adataival

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a Azure PowerShell használatával szeretne bejelentkezni, és az Azure Storage-ban az Azure Storage-ban további műveleteket hajt végre, hozzon létre egy tárolási környezetet a Storage-fiókra való hivatkozáshoz, és adja meg a `-UseConnectedAccount` paramétert.

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

1. Szerezze be a Storage-fiók környezetét, amely az új Storage-fiókot adja meg a [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)hívásával. Storage-fiók esetén a hitelesítő adatok ismételt átadása helyett hivatkozhat a környezetre. Az `-UseConnectedAccount` Azure ad-beli hitelesítő adataival a következő adatműveletek meghívásához használja a paramétert:

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

- [A PowerShell használata RBAC-szerepkör hozzárendeléséhez a blob-és üzenetsor-adathoz való hozzáféréshez](storage-auth-aad-rbac-powershell.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](storage-auth-aad-msi.md)
