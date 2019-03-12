---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e7243c1f0c0bc26b2e77a060b5def6e204835235
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553746"
---
>[!NOTE]
>Nem rögzített erőforrások nyissa meg egy támogatási jegyet, lépjen kapcsolatba a kvóták növelését. Ne hozzon létre további Azure Media Services-fiókokat az, hogy megpróbálja tágítani a magasabb korlátok.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egyetlen előfizetéshez az Azure Media Services-fiókok | 25 (rögzített) |
| Media szolgáltatás számára fenntartott egységek száma a Media Services-fiók |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Feladatok száma a Media Services-fiók | 50,000<sup>2</sup> |
| Kapcsolt műveletek feladatonként | 30 (rögzített) |
| Eszközök a Media Services-fiókonként | 1,000,000|
| Objektumok műveletenként | 50 |
| Objektumok feladatonként | 100 |
| Egy objektumhoz egyszerre társított egyedi keresők | 5<sup>4</sup> |
| Az élő csatornák a Media Services-fiókonként |5|
| Leállított állapotú programok csatornánként |50|
| Futó állapotú programok csatornánként |3|
| Streamvégpontok, leállított vagy a Media Services-fiókonként|2|
| Streamelési egységek streamvégpontonként |10 |
| Tárfiókok | 1000<sup>5</sup> (rögzített) |
| Házirendek | 1,000,000<sup>6</sup> |
| Fájlméret| Bizonyos esetekben nincs korlátozva a Media Services támogatja a maximális fájlméretet. <sup>7</sup> |

<sup>1</sup>Ha módosítja a típusát, például az S1, S2-ről a szolgáltatás számára fenntartott egység maximális korlátok alapértékre állnak vissza.

<sup>2</sup>ezt az értéket tartalmazza a sorba állított, befejezett, aktív és a visszavont feladatokat. A törölt feladatokat nem tartalmazza. Törölheti a régi feladatokat **IJob.Delete** vagy a **törlése** HTTP-kérés.

2017. április 1. a fiókban 90 napnál régebbi feladat rekordot automatikusan törlődik, és a kapcsolódó bejegyzések. Az automatikus törlés akkor fordul elő, akkor is, ha a rekordok száma nem éri a maximális kvótát. A feladatok és tevékenységek információkat archiválni, használja a kódot ismertetett [a Media Services .NET SDK-eszközök kezelése](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>egy kérést lista feladat entitások, legfeljebb 1000 feladatok kérelmenként adja vissza. Az összes elküldött feladatot nyomon, használja a top vagy lekérdezések folytassa leírtak szerint [OData rendszerlekérdezési beállításait](https://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup>lokátorok nem felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Az egyes felhasználóknak különböző hozzáférési jogosultságokat biztosíthat, használja a digitális jogkezelési (DRM) megoldásokat. További információkért lásd: [a tartalmat az Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>a tárfiókok az Azure-előfizetéshez kell lennie.

<sup>6</sup>a korlát 1 000 000 szabályzat különböző Media Services-szabályzatok esetében. Például a Locator vagy a ContentKeyAuthorizationPolicy szól. 

>[!NOTE]
> Ha mindig ugyanazokat a napokat használ, és hozzáférési engedélyek, a szabályzatazonosítónak is ugyanannak használata Információk és a egy példa: [a Media Services .NET SDK-eszközök kezelése](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>Ha tartalmat tölt fel egy objektumba történik, a Media Services feldolgozni azt a szolgáltatás a médiaelemzés médiafeldolgozói egyikével, vegye figyelembe a maximális fájlméretek, amelyek használata támogatott. Eszközök tartalmazzák a Media Encoder Standard programmaggal és a Media Encoder Premium munkafolyamat vagy elemzési motor, például a Face detector használatával.

A maximális méretet, amely egy blob támogatott jelenleg legfeljebb 5 TB-os Azure Blob Storage-ban. További korlátozások érvényesek a Media Services a szolgáltatás által használt Virtuálisgép-méretek alapján. Az alábbi táblázatban látható a korlátok a media szolgáltatás számára fenntartott egységek S1, S2 és S3. Ha a forrásfájl mérete nagyobb, mint a tábla meghatározott keretek, a kódolási feladat sikertelen lesz. Ha hosszú időtartam 4K felbontása forrásai kódol, meg kell S3 szintű media szolgáltatás számára fenntartott egységek használata szükséges a teljesítmény elérése érdekében. Ha 4K tartalom, amely nagyobb, mint az S3 szintű media szolgáltatás számára fenntartott egységek 260 GB-os korlátot, írjon nekünk az amshelp@microsoft.com a támogatásához a lehetséges kezelésükre.

| Media szolgáltatás számára fenntartott egység típusa | Maximális bemeneti mérete (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
