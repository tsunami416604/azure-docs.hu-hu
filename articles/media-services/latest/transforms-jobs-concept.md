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
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: d93e0de48fd10677ad30e002390dc2e8177cf2eb
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408476"
---
# <a name="transforms-and-jobs"></a>Átalakítások és feladatok
 
Használat [alakítja át](https://docs.microsoft.com/rest/api/media/transforms) és videók elemzése gyakori feladatok konfigurálása. Minden egyes **átalakítása** egy módszereivel, vagy egy munkafolyamatot a video- és audiotartalmak fájlok feldolgozása kapcsolatos feladatokat ismerteti. Egyetlen átalakító egynél több szabály is alkalmazhat. Egy-egy átalakítási megadhatja például, hogy minden videó kódolhatók-e a megadott sávszélességű, MP4-fájlokat, és, hogy egy miniatűr képére jöjjön létre a videó első keretből. Az átalakítási szerepeltetni kívánt minden egyes szabály egy TransformOutput bejegyzést jelentene. Átalakítások hozhat létre Media Services-fiók a Media Services v3 API-val, vagy valamelyik közzétett SDK-k használatával. A Media Services v3, így használhatja a Resource Manager-sablonok létrehozása és üzembe helyezése az API által az Azure Resource Manager alkalmazások alakítja át a Media Services-fiók. Szerepköralapú hozzáférés-vezérlés segítségével átalakítások való hozzáférés zárolása.

A frissítési műveletet a [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) entitás szánnak módosítása a leírást, illetve az alapul szolgáló TransformOutputs prioritásait. Javasoljuk, hogy az ilyen frissítések hajtható végre az összes folyamatban lévő feladatok befejezését. Ha azt tervezi, újraírási a recept, hozzon létre egy új átalakítás szeretne.

A [feladat](https://docs.microsoft.com/rest/api/media/jobs) van az Azure Media Services tényleges kérelem a alkalmazni a **átalakítása** egy adott a bemeneti videó vagy hang tartalomhoz. Az átalakítás létrehozása után küldhet feladatokat a Media Services API-k és az egyéb közzétett SDK-k használatával. A **feladat** információkat, például a bemeneti videó helyét, és a kimeneti helyét adja meg. Megadhatja a helyét, a bemeneti videó használatával: HTTPS URL-címek, SAS URL-címeit, vagy [eszközök](https://docs.microsoft.com/rest/api/media/assets). A folyamat és a feladatok állapotának figyelésével az Event Grid-események szerezhető meg. További információkért lásd: [EventGrid események figyelése](job-state-events-cli-how-to.md).

A frissítési műveletet a [feladat](https://docs.microsoft.com/rest/api/media/jobs) entitás módosításához is használható a *leírás*, és a *prioritású* tulajdonságok a feladat elküldése után. Módosítás a *prioritású* a tulajdonság csak akkor, ha a feladat még mindig aszinkron állapotban van. Ha a feladat már megkezdődött a feldolgozás, vagy véget ért, prioritás módosítása nem befolyásolja.

Az alábbi ábrán látható átalakítások és feladatok munkafolyamat.

![Átalakítások](./media/encoding/transforms-jobs.png)

> [!NOTE]
> Tulajdonságainak **átalakítása** és **feladat** a DateTime típusú állandóan UTC formátumban vannak.

## <a name="typical-workflow"></a>Jellemző munkafolyamat

1. Hozzon létre egy-egy átalakítási 
2. Adott átalakítási alatt-feladatok elküldése 
3. Lista átalakítások 
4. Egy-egy átalakítási törlése, ha nem tervezi a jövőben használni. 

### <a name="example"></a>Példa

Tegyük fel, hogy szeretne kinyerni a miniatűr képként – az összes videó első keret a lépéseket, akkor a következők: 

1. Adja meg a recept, vagy a szabály a videók feldolgozásához – "a videó első keret adatokként a miniatűr". 
2. Minden egyes, a szolgáltatás vázolnak fel: 
    1. Hol található a videót  
    2. Hol lehet írni a kimeneti asztalnak a miniatűrjére. 

A **átalakítása** hozhat létre a recept egyszer (1. lépés), és küldje el a feladatokat az adott recept (2. lépés).

## <a name="paging"></a>Lapozás

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

[Feltöltése, kódolása és videofájlok streamelése](stream-files-tutorial-with-api.md)
