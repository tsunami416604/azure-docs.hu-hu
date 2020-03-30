---
title: PowerShell-parancsok futtatása Azure AD-hitelesítő adatokkal a blob- vagy várólista-adatok eléréséhez
titleSuffix: Azure Storage
description: A PowerShell támogatja az Azure AD-hitelesítő adatokkal történő bejelentkezést az Azure Storage blobok és várólisták adatainak futtatásához. A munkamenethez hozzáférési jogkivonat áll rendelkezésre, amely a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági taghoz rendelt RBAC szerepkörtől függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553448"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>PowerShell-parancsok futtatása Azure AD-hitelesítő adatokkal a blob- vagy várólista-adatok eléréséhez

Az Azure Storage olyan powershell-bővítményeket biztosít, amelyek lehetővé teszik a bejelentkezést és a parancsfájl-parancsfájl-parancsok futtatását az Azure Active Directory (Azure AD) hitelesítő adataival. Amikor bejelentkezik a PowerShell-be az Azure AD-hitelesítő adatokkal, egy OAuth 2.0-s hozzáférési jogkivonatot ad vissza. Ezt a jogkivonatot a PowerShell automatikusan használja a Blob vagy a Queue storage további adatműveletek engedélyezésére. A támogatott műveletek, már nem kell átadni egy fiókkulcsot vagy SAS-jogkivonatot a parancsot.

Szerepköralapú hozzáférés-vezérlés (RBAC) keresztül engedélyeket rendelhet a blob- és várólista-adatokhoz egy Azure AD rendszerbiztonsági taghoz. Az RBAC-szerepkörökről az Azure Storage-ban további információt az [Azure Storage-adatokhoz való hozzáférési jogok kezelése az RBAC-mal című](storage-auth-aad-rbac.md)témakörben talál.

## <a name="supported-operations"></a>Támogatott műveletek

Az Azure Storage-bővítmények blob- és várólista-adatokon végzett műveletekhez támogatottak. Mely műveleteket hívhatja meg, az Azure AD rendszerbiztonsági tagnak adott engedélyektől függ, amellyel bejelentkezik a PowerShellbe. Az Azure Storage-tárolókhoz vagy várólistákhoz az RBAC-on keresztül vannak hozzárendelve. Ha például a **Blob Data Reader** szerepkört kapta, futtathat parancsfájl-kezelési parancsokat, amelyek adatokat olvasnak egy tárolóból vagy várólistából. Ha a **Blob Data Contributor** szerepkört kapta, futtathat parancsfájl-kezelési parancsokat, amelyek egy tárolót vagy várólistát vagy a benne lévő adatokat olvasnak, írnak vagy törölnek.

Az egyes Azure Storage-műveletekhez egy tárolón vagy várólistán szükséges engedélyekről az [OAuth-jogkivonatokkal végzett tárolási műveletek hívása](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)című témakörben talál.  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-parancsok hívása az Azure AD-hitelesítő adatokkal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure PowerShell használatával jelentkezzen be, és futtassa a további műveleteket az Azure Storage-on az `-UseConnectedAccount` Azure AD hitelesítő adatok használatával, hozzon létre egy tárolási környezetben hivatkozik a tárfiók, és tartalmazza a paramétert.

A következő példa bemutatja, hogyan hozhat létre egy tárolót egy új tárfiókban az Azure PowerShell az Azure AD hitelesítő adatait. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

1. Jelentkezzen be Azure-fiókjába a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal:

    ```powershell
    Connect-AzAccount
    ```

    Az Azure-ba a PowerShellhasználatával való bejelentkezésről a [Bejelentkezés az Azure PowerShellhasználatával](/powershell/azure/authenticate-azureps)című témakörben talál további információt.

1. Hozzon létre egy [Azure-erőforráscsoportot a New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)hívásával. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Tárfiókot hozhat létre a [New-AzStorageAccount hívásával.](/powershell/module/az.storage/new-azstorageaccount)

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. A Tárfiók környezetének bekésezése, amely a [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)hívásával határozza meg az új tárfiókot. Egy tárfiókon való működés során hivatkozhat a környezetre a hitelesítő adatok ismételt átadása helyett. Adja `-UseConnectedAccount` meg a paramétert az Azure AD-hitelesítő adatok használatával végzett további adatműveletek hívásához:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. A tároló létrehozása előtt rendelje hozzá a [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magához. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekre van szüksége az adatműveletek a tárfiókon való végrehajtásához. Az RBAC-szerepkörök hozzárendeléséről az [Azure blob- és várólista-adatok hoz való hozzáférés megadása az RBAC-mal című témakörben talál további információt az Azure Portalon.](storage-auth-aad-rbac.md)

    > [!IMPORTANT]
    > Az RBAC szerepkör-hozzárendelések propagálása néhány percet is igénybe vehet.

1. Hozzon létre egy tárolót a [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)hívásával. Mivel ez a hívás az előző lépésekben létrehozott környezetet használja, a tároló az Azure AD hitelesítő adataival jön létre.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>További lépések

- [A PowerShell használatával RBAC-szerepkört rendelhet a blob- és várólista-adatokhoz való hozzáféréshez](storage-auth-aad-rbac-powershell.md)
- [Blob- és várólista-adatokhoz való hozzáférés engedélyezése felügyelt identitásokkal az Azure-erőforrásokhoz](storage-auth-aad-msi.md)
