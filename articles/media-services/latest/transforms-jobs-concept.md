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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742779"
---
# <a name="transforms-and-jobs"></a>Átalakítások és feladatok
 
Használat [alakítja át](https://docs.microsoft.com/rest/api/media/transforms) és videók elemzése gyakori feladatok konfigurálása. Minden egyes **átalakítása** egy módszereivel, vagy egy munkafolyamatot a video- és audiotartalmak fájlok feldolgozása kapcsolatos feladatokat ismerteti. 

A [feladat](https://docs.microsoft.com/rest/api/media/jobs) van az Azure Media Services tényleges kérelem a alkalmazni a **átalakítása** egy adott a bemeneti videó vagy hang tartalomhoz. A **feladat** információkat, például a bemeneti videó helyét, és a kimeneti helyét adja meg. Megadhatja a helyét, a bemeneti videó használatával: HTTPS URL-címek, SAS URL-címeit, vagy [Media Services-eszközök](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Jellemző munkafolyamat

1. Hozzon létre egy-egy átalakítási 
2. Adott átalakítási alatt-feladatok elküldése 
3. Lista átalakítások 
4. Egy-egy átalakítási törlése, ha nem tervezi a jövőben használni. 

Tegyük fel, hogy szeretne kinyerni a miniatűr képként – az összes videó első keret a lépéseket, akkor a következők: 

1. Adja meg a recept, vagy a szabály a videók feldolgozásához – "a videó első keret adatokként a miniatűr". 
2. Minden egyes, a szolgáltatás vázolnak fel: 
    1. Hol található a videót  
    2. Hol lehet írni a kimeneti asztalnak a miniatűrjére. 

A **átalakítása** hozhat létre a recept egyszer (1. lépés), és küldje el a feladatokat az adott recept (2. lépés).

## <a name="transforms"></a>Átalakítások

Átalakítások hozhat létre Media Services-fiók a v3 API segítségével közvetlenül, vagy valamelyik közzétett SDK-k használatával. A Media Services v3, így használhatja a Resource Manager-sablonok létrehozása és üzembe helyezése az API által az Azure Resource Manager alkalmazások alakítja át a Media Services-fiók. Szerepköralapú hozzáférés-vezérlés segítségével átalakítások való hozzáférés zárolása.

### <a name="transform-definition"></a>Definíció átalakítása

Az alábbi táblázat az átalakítás tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Leírás|
|---|---|
|Azonosító|Az erőforrás teljes erőforrás-azonosítója.|
|név|Az erőforrás neve.|
|Properties.created |Az UTC szerinti dátuma és az átalakítási létrehozásának időpontja, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|properties.description |Egy részletes leírást az átalakítás.|
|properties.lastModified |Az UTC szerinti dátuma és ideje, ha az átalakítás legutóbbi, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|Properties.outputs |Egy vagy több, az átalakítás kell generáló TransformOutputs tömbje.|
|type|Az erőforrás típusát.|

A teljes meghatározása: [alakítja át az](https://docs.microsoft.com/rest/api/media/transforms).

Amint azt fent kifejtettük, az egy-egy átalakítási segítségével létrehozhatja az egy módszereivel vagy a szabály a videók feldolgozásához. Egyetlen átalakító egynél több szabály is alkalmazhat. Egy-egy átalakítási megadhatja például, hogy minden videó kódolhatók-e a megadott sávszélességű, MP4-fájlokat, és, hogy egy miniatűr képére jöjjön létre a videó első keretből. Az átalakítási szerepeltetni kívánt minden egyes szabály egy TransformOutput bejegyzést jelentene.

> [!NOTE]
> Az átalakítások definíciója egy olyan frissítési művelet támogatja, miközben beavatkozást, hogy az összes folyamatban lévő feladatok befejezését, mielőtt végrehajtja a módosítását. Általában meg szeretne frissíteni egy meglévő átalakító leírásának módosításához, vagy módosítsa az alapul szolgáló TransformOutputs prioritásait. Ha szeretne írja újra a recept, akkor egy új átalakítás hozna létre.

## <a name="jobs"></a>Feladatok

Egy átalakítás létrehozása után küldhet feladatokat a Media Services API-k és az egyéb közzétett SDK-k használatával. A folyamat és a feladatok állapotának figyelésével az Event Grid-események szerezhető meg. További információkért lásd: [EventGrid események figyelése](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Feladat definíciója

Az alábbi táblázat bemutatja a feladatok a tulajdonságok, és lehetővé teszi a definíciójukat.

|Name (Név)|Leírás|
|---|---|
|id|Az erőforrás teljes erőforrás-azonosítója.|
|név   |Az erőforrás neve.|
|properties.correlationData |A megadott ügyfél korrelációs adatokat (nem módosítható), amely a feladatok és JobOutput állapot részeként adja vissza a módosítási értesítésekre. További információkért lásd: [Eseménysémák](media-services-event-schemas.md)<br/><br/>A tulajdonság is használható a több-bérlős alkalmazásokhoz a Media Services. A feladatok a bérlőazonosítók helyezheti. |
|Properties.created |Az UTC szerinti dátuma és a feladat létrehozásának időpontja, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|properties.description |A feladat a megadott választható ügyfél leírása.|
|Properties.input|A feladat bemenetei.|
|properties.lastModified    |Az UTC szerinti dátuma és időpontja, amikor a feladat utolsó módosításának, a "éééé-hh-DDThh:mm:ssZ" formátumban.|
|Properties.outputs|A kimenet a feladathoz.|
|Properties.Priority    |Amellyel fel kell dolgozni a feladat prioritása. Magasabb prioritású feladatok az alacsonyabb prioritású feladatok előtt kerülnek feldolgozásra. Ha nincs beállítva, az alapértelmezett érték a normál.|
|Properties.state   |A feladat jelenlegi állapota.|
|type   |Az erőforrás típusát.|

A teljes meghatározása: [feladatok](https://docs.microsoft.com/rest/api/media/jobs).

> [!NOTE]
> Amíg a feladat definíciója egy olyan frissítési művelet támogatja, módosíthatók, a feladat elküldése után csak tulajdonságok, a leírást és a prioritás. További a prioritás módosítás érvényben, csak akkor, ha a feladat még mindig aszinkron állapotban van. Ha a feladat már megkezdődött a feldolgozás, vagy véget ért, prioritásának módosítása nem befolyásolja.

### <a name="pagination"></a>Tördelés

A Media Services v3 feladatok tördelés használata támogatott.

> [!TIP]
> A gyűjtemény enumerálása, és nem függ egy adott oldal méretét a következő hivatkozás mindig használjon.

A lekérdezési válasz számos elemet tartalmaz, ha a szolgáltatás visszaadja egy "\@odata.nextLink" tulajdonságát a következő lapra az eredmények lekérése. Ez használható a lapozza végig a teljes eredményhalmaz. Az oldal méretét nem lehet konfigurálni. 

Feladatok létrehozása vagy a gyűjtemény átlapozva közben, ha a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részén.) 

A következő C# példa bemutatja a feladatok a fiókban lévő számba.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

További példák: [feladatok – lista](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>További lépések

[Stream-videó fájlok](stream-files-dotnet-quickstart.md)
