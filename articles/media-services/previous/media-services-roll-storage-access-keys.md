---
title: Media Services frissítése a működés közbeni tárolási hozzáférési kulcsok után | Microsoft dokumentumok
description: Ez a cikk útmutatást nyújt a Media Services frissítéséhez a működés közbeni tárolási hozzáférési kulcsok után.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 2a0d1c5af572c88dc11bed950b46706f0a2f081f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981957"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>A Media Services frissítése tárelérési kulcsok váltása után 

Amikor új Azure Media Services (AMS) fiókot hoz létre, a rendszer arra is kéri, hogy válasszon ki egy Azure Storage-fiókot, amely a médiatartalom tárolására szolgál. A Media Services-fiókhoz több tárfiókot is hozzáadhat. Ez a cikk bemutatja, hogyan kell elforgatni a tárolókulcsokat. Azt is bemutatja, hogyan adhat hozzá tárfiókokat egy médiafiókhoz. 

A cikkben leírt műveletek végrehajtásához az [Azure Resource Manager API-kat](/rest/api/media/operations/azure-media-services-rest-api-reference) és a Powershellt kell [használnia.](https://docs.microsoft.com/powershell/module/az.media)  További információ: [Az Azure-erőforrások kezelése a PowerShell és az Erőforrás-kezelő segítségével](../../azure-resource-manager/management/manage-resource-groups-powershell.md)című témakörben talál.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Új tárfiók létrehozásakor az Azure két 512 bites tároló-hozzáférési kulcsot hoz létre, amelyek a tárfiókhoz való hozzáférés hitelesítésére szolgálnak. A tárolókapcsolatok biztonságosabbá tétele érdekében ajánlott rendszeresidőközönként újragenerálni és forgatni a tárolóelérési kulcsot. Két hozzáférési kulcs (elsődleges és másodlagos) áll rendelkezésre annak érdekében, hogy a tárfiókhoz való kapcsolatok karbantartása az egyik hozzáférési kulcs használatával, míg a másik hozzáférési kulcs újragenerálása. Ezt az eljárást "gördülő hozzáférési kulcsoknak" is nevezik.

A Media Services a hozzá biztosított tárolókulcstól függ. Pontosabban, a lokátorok, amelyek az eszközök streamelésére vagy letöltésére szolgálnak, a megadott tárolási hozzáférési kulcstól függenek. A MS-fiók létrehozásakor alapértelmezés szerint az elsődleges tárolóelérési kulcstól függ, de felhasználóként frissítheti az AMS tárolókulcsát. A jelen cikkben ismertetett lépések ben mindenképpen tudatnia kell a Media Services szolgáltatással, hogy melyik kulcsot kell használnia.  

>[!NOTE]
> Ha több tárfiókkal rendelkezik, ezt az eljárást minden tárfiókkal hajtsa végre. A tárolóbillentyűk elforgatásának sorrendje nem rögzített. Először elforgathatja a másodlagos kulcsot, majd az elsődleges kulcsot, vagy fordítva.
>
> Mielőtt végrehajtana a cikkben leírt lépéseket egy éles fiókban, győződjön meg arról, hogy tesztelje őket egy üzem előtti fiókban.
>

## <a name="steps-to-rotate-storage-keys"></a>A tárolóbillentyűk elforgatásának lépései 
 
 1. Módosítsa a tárfiók elsődleges kulcsát a powershell-parancsmagon vagy az [Azure Portalon](https://portal.azure.com/) keresztül.
 2. A Sync-AzMediaServiceStorageKeys parancsmag hívása megfelelő paramokkal, hogy kényszerítse a médiafiókot a tárfiók kulcsainak felvételéhez
 
    A következő példa bemutatja, hogyan szinkronizálhatja a kulcsokat a tárfiókok.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Várj egy órát. Ellenőrizze, hogy a streamelési forgatókönyvek működnek-e.
 4. A tárfiók másodlagos kulcsának módosítása a powershell-parancsmagon vagy az Azure Portalon keresztül.
 5. Hívja meg a Sync-AzMediaServiceStorageKeys powershellt a megfelelő params használatával, hogy kényszerítse a médiafiókot az új tárfiók kulcsok felvételéhez. 
 6. Várj egy órát. Ellenőrizze, hogy a streamelési forgatókönyvek működnek-e.
 
### <a name="a-powershell-cmdlet-example"></a>Példa powershell-parancsmagra 

A következő példa bemutatja, hogyan lehet leszerezni a tárfiókot, és szinkronizálni az AMS-fiókkal.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>A tárfiókok AMS-fiókhoz való hozzáadásának lépései

A következő cikk bemutatja, hogyan adhat hozzá tárfiókokat az AMS-fiókhoz: [Több tárfiók csatolása egy Media Services-fiókhoz.](meda-services-managing-multiple-storage-accounts.md)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Köszönetnyilvánítás
Szeretnénk elismerni a következő embereket, akik hozzájárultak a dokumentum létrehozásához: Cenk Dingiloglu, Milan Gada, Seva Titov.
