---
title: "A Media Services frissítése után a működés közbeni tárelérési kulcsok |} Microsoft Docs"
description: "Ez a cikk nyújtanak útmutatást a Media Services frissítése után a működés közbeni tárelérési kulcsok."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: 304e72e0d2d4a7e95df513e6d5481def9eae3f68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>A Media Services frissítése után a működés közbeni tárelérési kulcsok

Amikor létrehoz egy új Azure Media Services (AMS)-fiókot, a rendszer is felkéri válassza ki az Azure Storage-fiók, amely a médiatartalom tárolására szolgál. Egynél több tárfiókokat adhat hozzá a Media Services-fiókhoz. Ez a témakör bemutatja, hogyan tárolási kulcsok megváltoztatása. Azt is bemutatja, hogyan vehet fel a storage-fiókok adathordozó-fiókra. 

A témakörben ismertetett műveletek elvégzéséhez kell használni [ARM API-k](https://docs.microsoft.com/rest/api/media/mediaservice) és [Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  További információkért lásd: [kezelése az Azure-erőforrások az PowerShell és a Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Áttekintés

Új tárfiók létrehozásakor az Azure létrehoz két 512 bites tárelérési kulcsot, amellyel hitelesíti a hozzáférést a tárfiókhoz használt. A biztonságos tárolási kapcsolatai több, javasoljuk, hogy rendszeresen generálja újra és elforgatása a tárelérési kulcsát. Két kulcs (elsődleges és másodlagos) ahhoz, hogy az egyik kulcs, amíg a többi hozzáférési kulcs újragenerálása a tárfiókhoz kapcsolatok megtartani vannak megadva. Ez az eljárás "működés közbeni hívóbetűk" néven is ismert.

A Media Services attól függ, hogy a megadott kulcs. A keresők továbbításához vagy letöltéséhez az eszközök használt pontosabban, a megadott tárelérési kulcs függ. Az AMS-fiók létrehozásakor az felveszi egy függőséget az elsődleges tárelérési kulcs alapértelmezés szerint, de egy olyan felhasználó nevében a kulcsot, amely rendelkezik AMS frissíthető. Győződjön meg arról, hogy tudja, melyik kulcs használatához kövesse a jelen témakörben ismertetett Media Services segítségével.  

>[!NOTE]
> Ha több tárfiókot, akkor ez a művelet minden egyes storage-fiók. A sorrendet, amelyben tárolási kulcsok elforgatása nincs megoldva. A másodlagos kulcs első és az elsődleges kulcs, vagy fordítva fordítva is elforgatása.
>
> Egy éles fiókon ebben a témakörben leírt lépések végrehajtása, előtt ellenőrizze, hogy tesztelését egy üzemi előtti fiók-e.
>

## <a name="steps-to-rotate-storage-keys"></a>Lépéseket tárolási kulcsok megváltoztatása 
 
 1. A tárfiók elsődleges kulcsot a powershell-parancsmaggal módosíthatja vagy [Azure](https://portal.azure.com/) portálon.
 2. Hívja meg Sync-AzureRmMediaServiceStorageKeys parancsmagot a megfelelő paraméterei media-fiók tárfiókkulcsainak átvételéhez kényszerítése
 
    A következő példa bemutatja, hogyan kulccsal storage-fiókok szinkronizálása.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Várjon egy órát. Ellenőrizze, hogy az adatfolyam-továbbítási forgatókönyv dolgozik.
 4. Módosítsa a tárfiók másodlagos kulcsot a powershell-parancsmagot vagy az Azure-portál használatával.
 5. Hívja meg az új tárfiókkulcsok átvételéhez media fiókot megfelelő paraméterei a Sync-AzureRmMediaServiceStorageKeys powershell. 
 6. Várjon egy órát. Ellenőrizze, hogy az adatfolyam-továbbítási forgatókönyv dolgozik.
 
### <a name="a-powershell-cmdlet-example"></a>Egy powershell parancsmag – példa 

A következő példa bemutatja, hogyan a storage-fiók és szinkronizálás a az AMS-fiók.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Lépések végrehajtásával adja hozzá az AMS-fiók storage-fiókok

A következő témakör bemutatja, hogyan storage-fiókok hozzáadása az AMS-fiók: [több tárfiókot csatolása a Media Services-fiók](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Nyugták
Ez a dokumentum kialakításához része volt a következő személyek tudomásul szeretnénk: Cenk Dingiloglu, Milánó Gada Seva Titov.
