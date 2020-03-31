---
title: Adatok küldése a hot, cold, archív blob szintre az Azure Data Box/Azure Data Box Heavy használatával
description: A cikk azt ismerteti, hogy az Azure Data Box vagy az Azure Data Box Heavy használatával hogyan küldhet adatokat egy megfelelő blokkblob-tárolási szintre, például meleg, hideg vagy archív szintre.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 31178284313ad7dafb094b109a75d4550cabd39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560371"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Az Azure Data Box vagy az Azure Data Box Heavy használatával adatokat küldhet a megfelelő Azure Storage blobrétegnek

Az Azure Data Box nagy mennyiségű adatot szállít az Azure-ba egy saját tárolóeszköz szállításával. Töltse fel az eszközt adatokkal, és küldje vissza. A Data Box-ból származó adatok feltöltésre kerül a tárfiókhoz társított alapértelmezett szintre. Ezután áthelyezheti az adatokat egy másik tárolási szintre.

Ez a cikk azt ismerteti, hogy a Data Box által feltöltött adatok hogyan helyezhetők át egy gyakori, hideg vagy archív blobszintre. Ez a cikk az Azure Data Box ra és az Azure Data Box Heavy-re egyaránt vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Válassza ki az adatoknak megfelelő tárolási szintet

Az Azure storage lehetővé teszi, hogy három különböző réteg tárolja az adatokat a leginkább költséghatékony módon - Meleg, Hideg vagy archiválás. A gyakori elérésű tárolási szint a gyakran elérhető adatok tárolására van optimalizálva. A gyors tárolás tárolási költségek kelnek, mint a Cool és archive tárolás, de a legalacsonyabb hozzáférési költségek.

A ritka tárhelyszint a ritkán használt adatokhoz használható, amelyeket legalább 30 napig kell tárolni. A tárolási költség a hűtőszint alacsonyabb, mint a gyakori elérésű tárolási szint, de az adatelérési díjak magasak, mint a gyakori elérésű szint.

Az Azure Archive szint offline állapotban van, és a legalacsonyabb tárolási költségeket kínálja, de a legmagasabb hozzáférési költségeket is. Ez a szint olyan adatokhoz érhető el, amelyek legalább 180 napig archiválási tárolóban maradnak. Az egyes szintek és a díjszabási modell részleteiért látogasson el a tárolási szintek összehasonlítása című [részébe.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

A Data Box vagy a Data Box Heavy adatait feltölti egy tárolási réteg, amely a tárfiókhoz van társítva. Amikor létrehoz egy tárfiókot, megadhatja a hozzáférési szint a gyakori vagy ritka elérésű. A számítási feladatok és a költségek hozzáférési mintájától függően áthelyezheti ezeket az adatokat az alapértelmezett rétegből egy másik tárolási szintre.

Az objektumtárolási adatokat csak a Blob storage vagy az Általános célú v2 (GPv2) fiókokban rétegezheti. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. Az adatok megfelelő tárolási szintjének kiválasztásához tekintse át az [Azure Blob storage: Prémium, Gyakori, Ritka és Ritka elérésű és archív tárolási szintek részletes szempontjait.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

## <a name="set-a-default-blob-tier"></a>Alapértelmezett blobszint beállítása

Az alapértelmezett blobszint akkor van megadva, amikor a tárfiók az Azure Portalon jön létre. Ha egy tárolási típus t a GPv2 vagy a Blob storage, akkor az Access tier attribútum megadható. Alapértelmezés szerint a Gyakori elérésű szint van kiválasztva.

A rétegek nem adhatók meg, ha új fiókot próbál létrehozni a Data Box vagy a Data Box Heavy rendelésekor. A fiók létrehozása után módosíthatja a fiókot a portálon az alapértelmezett hozzáférési szint beállításához.

Másik lehetőségként először hozzon létre egy tárfiókot a megadott hozzáférési szint attribútummal. A Data Box vagy a Data Box Heavy rendelés létrehozásakor válassza ki a meglévő tárfiókot. Az alapértelmezett blobszint beállításáról a tárfiók létrehozása során című lapban olvashat bővebben: [Tárfiók létrehozása az Azure Portalon.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal)

## <a name="move-data-to-a-non-default-tier"></a>Adatok áthelyezése nem alapértelmezett szintre

Miután a Data Box-eszközről származó adatok feltöltése az alapértelmezett szintre, érdemes lehet áthelyezni az adatokat egy nem alapértelmezett szintre. Ezeket az adatokat kétféleképpen helyezheti át egy nem alapértelmezett szintre.

- **Azure Blob-tárolási életciklus-kezelés** – Szabályzatalapú megközelítést használhat az adatok automatikus rétegezéséhez vagy az életciklusa végén lejáró lejáratához. További információért keresse [fel az Azure Blob-tárolási életciklus kezelése című területet.](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)
- **Parancsfájlok használata** – az Azure PowerShell használatával engedélyezheti a blobszintű rétegezést. Meghívhatja `SetBlobTier` a műveletet a blob szint beállításához.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>A blobszint beállítása az Azure PowerShell használatával

Az alábbi lépések ismertetik, hogyan állíthatja be a blob réteg archiválásegy Azure PowerShell-parancsfájl használatával.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet. Győződjön meg arról, hogy a futó PowerShell 5.0 vagy újabb. Típus:

   `$PSVersionTable.PSVersion`     

2. Jelentkezzen be az Azure PowerShellbe. 

   `Login-AzAccount`  

3. Adja meg a tárolófiók, a hozzáférési kulcs, a tároló és a tárolási környezet változóit.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Az összes blobok a tárolóba.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Állítsa be a réteg az összes blobok a tárolóban archiválása.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    A mintakimenet az alábbiakban látható:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Ha azt szeretné, hogy az adatok archiválása a betöltéskor, állítsa be az alapértelmezett fiókréteget a Gyakori elérésű értékre. Ha az alapértelmezett szint ritka, akkor 30 napos korai törlési büntetés van, ha az adatok azonnal az archiválásra kerülnek.

## <a name="next-steps"></a>További lépések

-  Ismerje meg, hogyan kezelhetők a [gyakori adatrétegezési forgatókönyvek életciklus-szabályzati szabályokkal](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

