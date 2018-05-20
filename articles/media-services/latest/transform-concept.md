---
title: Átalakítja az és az Azure Media Services feladatai |} Microsoft Docs
description: A Media Services használata esetén egy átalakítási szabályok vagy a videók feldolgozásához specifikációk leírása létrehozásához szükséges. Ez a cikk áttekintést nyújt az átalakító van, és hogy miképpen lehet vele.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="transforms-and-jobs"></a>Az átalakító és feladatok

## <a name="overview"></a>Áttekintés 

Az Azure Media Services REST API (v3) legújabb verzióját vezet be az új sablonalapú munkafolyamat erőforrás kódolás és/vagy videók, úgynevezett elemzése a **átalakítási**. **Átalakítja az** kódolás vagy analying videók kapcsolódó gyakori feladatok konfigurálásához használható. Minden egyes **átalakítási** egy egyszerű munkafolyamatot a video- vagy -fájlok feldolgozása kapcsolatos feladatokat ismerteti. 

A **átalakítási** célja a módszereivel és egy **feladat** alkalmazni, amely az Azure Media Services a tényleges kérelem **átalakítási** egy adott bemeneti video- vagy tartalomhoz. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét. Megadhatja, hogy a videó használatával helye: http (s) URL-címek, SAS URL-címek vagy helyileg vagy az Azure Blob Storage tárolóban található fájlok elérési útját. Legfeljebb 100 átalakítások rendelkezik az Azure Media Services-fiók, és ezek átalakítások a feladatok elküldéséhez. Majd előfizetni, esemény, például a feladat állapotának megváltozásakor, értesítések, amely integrálható közvetlenül az Azure Event rács értesítési rendszerrel használatával. 

Mivel ez az API által Azure Resource Manager célja, használhatja a Resource Manager-sablonok létrehozása és telepítése átalakítások a Media Services-fiók. Szerepköralapú hozzáférés-vezérlés is is szintjén adhatók meg az erőforrás ezt az API-ban, hogy lehetővé teszi a zárolását, például átalakítások egy adott erőforráshoz való hozzáférés.

## <a name="transform-definition"></a>Átalakítás meghatározása

A következő táblázat átalakítás tulajdonságait jeleníti meg, és lehetőséget ad a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|Azonosító|karakterlánc|Az erőforrás teljes erőforrás-azonosító.|
|név|karakterlánc|Az erőforrás neve.|
|Properties.created |karakterlánc|Az UTC szerinti dátuma és az átalakítási létrehozásának időpontja, a "éééé-hh-ssZ" formátumban.|
|properties.description |karakterlánc|Részletes leírását. az átalakítás.|
|properties.lastModified |karakterlánc|Az UTC-dátum és idő, amikor az átalakítás utolsó frissítés idejét, a "éééé-hh-ssZ" formátumban.|
|Properties.outputs |TransformOutput]|A transzformáció bemeneti eredményező kell egy vagy több TransformOutputs tömbjét.|
|type|karakterlánc|Az erőforrás típusa.|

A teljes meghatározása: [átalakítja](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Feladat definíciója

A következő táblázat feladat tulajdonságait jeleníti meg, és lehetőséget ad a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|Azonosító|karakterlánc|Az erőforrás teljes erőforrás-azonosító.|
|név|karakterlánc|Az erőforrás neve.|
|Properties.created |karakterlánc|Az UTC szerinti dátuma és az átalakítási létrehozásának időpontja, a "éééé-hh-ssZ" formátumban.|
|properties.description |karakterlánc|Részletes leírását. a feladat.|
|properties.lastModified |karakterlánc|Az UTC-dátum és idő, amikor az átalakítás utolsó frissítés idejét, a "éééé-hh-ssZ" formátumban.|
|Properties.outputs |JobOutput []: [JobOutputAsset] |A feladat kimenetének.|
|Properties.Priority |Prioritás |A prioritás, amelyhez a feladat fel kell dolgozni. A magasabb prioritású feladatok dolgozza fel előbb alacsonyabb prioritású feladatokat. Ha nincs beállítva, az alapértelmezett nem jelent hibás működést.
|Properties.state |JobState |A feladat aktuális állapotát.
|type|karakterlánc|Az erőforrás típusa.|

A teljes meghatározása: [feladatok](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Tipikus munkafolyamat és példa

1. Hozzon létre egy átalakítási 
2. Az adott átalakító feladatok elküldéséhez 
3. Lista átalakítások 
4. A "módszereivel" használata a jövőben nem szeretne törölni egy átalakítási. 

Tegyük fel, hogy szeretne kinyerni a miniatűr képére – első keretében a videók volna lépései a következők: 

1. Adja meg a szabály a feldolgozás – például, a videó első keret használják a miniatűr 
2. Ezt követően minden videó bemenetként a szolgáltatás rendelkezik, a szolgáltatás akkor adja: 
    1. Hol található a videoeszközök, és 
    2. A kimenet – például a miniatűr képére helyét 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az adatfolyam videofájlok](stream-files-dotnet-quickstart.md)
