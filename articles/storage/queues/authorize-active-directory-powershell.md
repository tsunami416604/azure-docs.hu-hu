---
title: PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a várólista adatainak eléréséhez
titleSuffix: Azure Storage
description: A PowerShell támogatja az Azure AD-beli hitelesítő adatokkal való bejelentkezést, hogy parancsokat futtasson az Azure Storage-üzenetsor adatain. A munkamenethez hozzáférési token van megadva, és a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági tag számára hozzárendelt Azure-szerepkörtől függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: queues
ms.openlocfilehash: 1dc8009792163730602827a995c4b6900a0ef08d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108582"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a várólista adatainak eléréséhez

Az Azure Storage olyan bővítményeket biztosít a PowerShellhez, amelyek lehetővé teszik a Azure Active Directory-(Azure AD-) hitelesítő adatokkal való bejelentkezést és parancsfájlok futtatását. Ha Azure AD-beli hitelesítő adatokkal jelentkezik be a PowerShellbe, egy OAuth 2,0 hozzáférési tokent ad vissza. A PowerShell automatikusan használja ezt a tokent, hogy engedélyezze a további adatműveleteket a várólista-tárolón. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Az Azure AD rendszerbiztonsági tag számára a szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelhet engedélyeket az üzenetsor-adathoz. Az Azure Storage-beli Azure-szerepkörökkel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz a RBAC használatával](../common/storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Támogatott műveletek

Az Azure Storage-bővítmények az üzenetsor-adatokon végrehajtott műveletek esetében támogatottak. A felhívható műveletek az Azure AD rendszerbiztonsági tag által a PowerShellbe való bejelentkezéshez megadott engedélyektől függenek. Az Azure Storage-várólisták engedélyei a RBAC-on keresztül vannak hozzárendelve. Ha például hozzá lett rendelve a **várólista-Adatolvasó** szerepkörhöz, futtathat parancsfájl-parancsokat, amelyek egy várólistáról olvasnak be egy adatforrást. Ha hozzá lett rendelve a **várólista-adatközreműködői** szerepkörhöz, futtathat parancsfájl-parancsokat, amelyek egy várólistát vagy az általa tárolt adat olvasását, írását vagy törlését írják le, írhatják vagy törölhetik.

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a várólistán: [tárolási műveletek hívása OAuth-jogkivonatokkal](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-parancsok meghívása az Azure AD hitelesítő adataival

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a Azure PowerShell használatával szeretne bejelentkezni, és az Azure Storage-ban az Azure Storage-ban további műveleteket hajt végre, hozzon létre egy tárolási környezetet a Storage-fiókra való hivatkozáshoz, és adja meg a `-UseConnectedAccount` paramétert.

Az alábbi példa azt szemlélteti, hogyan hozható létre üzenetsor egy új Storage-fiókban Azure PowerShell Azure AD-beli hitelesítő adataival. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

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

1. A várólista létrehozása előtt rendelje hozzá a [tárolási üzenetsor adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) szerepkörét. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés biztosítása az Azure blobhoz és a üzenetsor-adatokhoz a Azure Portal RBAC](../common/storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Hozzon létre egy várólistát a [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue)hívásával. Mivel ez a hívás az előző lépésekben létrehozott környezetet használja, a várólista az Azure AD-beli hitelesítő adataival jön létre.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Következő lépések

- [A PowerShell használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adateléréshez](../common/storage-auth-aad-rbac-powershell.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](../common/storage-auth-aad-msi.md)
