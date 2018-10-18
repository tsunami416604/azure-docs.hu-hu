---
title: Átalakítások és feladatok az Azure Media Services |} A Microsoft Docs
description: Media Services használata közben szeretne létrehozni egy-egy átalakítási szabályok vagy specifikációk a videók feldolgozásához leírása. Ez a cikk áttekintést nyújt az átalakítás van, és hogyan használható a.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 214d4d3d11255e417f3df1e5f6e648b2a30225ea
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377308"
---
# <a name="transforms-and-jobs"></a>Átalakítások és feladatok

## <a name="overview"></a>Áttekintés 

Az Azure Media Services REST API (v3) legújabb verzióját vezet be az új sablonalapú munkafolyamat erőforrás kódolás és/vagy videók, nevű elemzése egy **átalakítása**. **Átalakítások** kódolás vagy analying videók kapcsolódó gyakori feladatok konfigurálásához használható. Minden egyes **átalakítása** a video- és audiotartalmak fájlok feldolgozási feladatainak egy egyszerű munkafolyamatot ismerteti. 

A **átalakítása** objektum a recept és a egy **feladat** van a alkalmazni, amely az Azure Media Services a tényleges kérelem **átalakítása** egy adott a bemeneti videó vagy hang tartalomhoz. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét. Megadhatja a helyét, a videó használatával: http (s) URL-címek, SAS URL-címek vagy helyileg vagy az Azure Blob storage-ban található fájl elérési útját. Az Azure Media Services-fiókban legfeljebb 100 átalakítások rendelkezik, és ezek az átalakítások alatt feladatok elküldéséhez. Ezután előfizethet eseményekre feladat állapotváltozásokat, például értesítésekkel, amely közvetlenül az Azure Event Grid értesítési rendszerrel integrálhatók. 

Mivel az API-t az Azure Resource Manager által alkalmazások, használhatja a Resource Manager-sablonok létrehozása és üzembe helyezése átalakítások a Media Services-fiók. Szerepköralapú hozzáférés-vezérlés is megadható ezzel az API-erőforrás szinten lehetővé teszi a hozzáférés bizonyos erőforrásokhoz, mint átalakítások zárolását.

## <a name="transform-definition"></a>Definíció átalakítása

Az alábbi táblázat az átalakítási tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|Azonosító|sztring|Az erőforrás teljes erőforrás-azonosítója.|
|név|sztring|Az erőforrás neve.|
|Properties.created |sztring|Az UTC szerinti dátuma és az átalakítási létrehozásának időpontja, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|properties.description |sztring|Egy részletes leírást az átalakítás.|
|properties.lastModified |sztring|Az UTC szerinti dátuma és ideje, ha az átalakítás legutóbbi, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|Properties.outputs |TransformOutput]|Egy vagy több, az átalakítás kell generáló TransformOutputs tömbje.|
|type|sztring|Az erőforrás típusát.|

A teljes meghatározása: [alakítja át az](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Feladat definíciója

Az alábbi táblázat bemutatja a feladat tulajdonságainak, és lehetővé teszi a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|Azonosító|sztring|Az erőforrás teljes erőforrás-azonosítója.|
|név|sztring|Az erőforrás neve.|
|Properties.created |sztring|Az UTC szerinti dátuma és az átalakítási létrehozásának időpontja, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|properties.description |sztring|Egy részletes leírást a feladat.|
|properties.lastModified |sztring|Az UTC szerinti dátuma és ideje, ha az átalakítás legutóbbi, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|Properties.outputs |JobOutput []: JobOutputAsset] |A kimenet a feladathoz.|
|Properties.Priority |Prioritás |Amellyel fel kell dolgozni a feladat prioritása. Magasabb prioritású feladatok az alacsonyabb prioritású feladatok előtt kerülnek feldolgozásra. Ha nincs beállítva, az alapértelmezett érték a normál.
|Properties.state |JobState |A feladat jelenlegi állapota.
|type|sztring|Az erőforrás típusát.|

A teljes meghatározása: [feladatok](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Példa és általános munkafolyamat

1. Hozzon létre egy-egy átalakítási 
2. Adott átalakítási alatt-feladatok elküldése 
3. Lista átalakítások 
4. Egy-egy átalakítási törlése, ha nem tervezi a jövőben ezt "recept" használandó. 

Tegyük fel, hogy szeretne kinyerni a miniatűr képként – az összes videó első keret a lépéseket, akkor a következők: 

1. Adja meg például a feldolgozás – vonatkozó szabály, a videó első keret használja, mint a miniatűr 
2. Ezt követően meg az egyes videók bemenetként a szolgáltatás rendelkezik, akkor mondja el a szolgáltatás: 
    1. Hol található a video- és 
    2. Kiírhatja a kimenetet – például az asztalnak a miniatűrjére helyét 

## <a name="next-steps"></a>További lépések

[Stream-videó fájlok](stream-files-dotnet-quickstart.md)
