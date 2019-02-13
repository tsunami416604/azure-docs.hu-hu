---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 9cd5a63b5b1d7000605f90752be755356d40bb4a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213069"
---
>[!NOTE]
>A nem rögzített erőforrások esetében kérvényezheti a kvóták emelését egy támogatási jegy megnyitásával. **Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egy előfizetéshez tartozó Azure Media Services- (AMS-) fiókok | 25 (rögzített) |
| Media szolgáltatás számára fenntartott egységek AMS-fiókonként |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Feladatok AMS-fiókonként | 50,000<sup>(2)</sup> |
| Kapcsolt műveletek feladatonként | 30 (rögzített) |
| Objektumok AMS-fiókonként | 1,000,000|
| Objektumok műveletenként | 50 |
| Objektumok feladatonként | 100 |
| Egy objektumhoz egyszerre társított egyedi keresők | 5<sup>(4)</sup> |
| Élő csatornák AMS-fiókonként |5|
| Leállított állapotú programok csatornánként |50|
| Futó állapotú programok csatornánként |3|
| Streamvégpontok (Leállítva vagy fut) a Media Services-fiókonként|2|
| Streamelési egységek streamvégpontonként |10 |
| Tárfiókok | 1000<sup>5</sup> (rögzített) |
| Házirendek | 1,000,000<sup>(6)</sup> |
| Fájlméret| Bizonyos esetekben nincs korlátozva a Media Services támogatja a maximális fájlméretet. <sup>7</sup> |
  
<sup>1</sup> módosítja a típusa (például S2-ről S1-re), ha az RU korlátaival állnak vissza.

<sup>2</sup> Ez a szám a várólistás, a befejeződött, az aktív, illetve a megszakított feladatokat is magában foglalja. A törölt feladatokat nem tartalmazza. A régi feladatokat az **IJob.Delete** vagy a **DELETE** HTTP-kérés segítségével lehet törölni.

2017. április 1. a fiókban 90 napnál régebbi feladat rekordot automatikusan törölve lesznek, a társított feladat bejegyzéseket, valamint akkor is, ha a rekordok száma nem éri a maximális kvótát. A feladatok/műveletek adatainak archiválásához használhatja az [itt](../articles/media-services/previous/media-services-dotnet-manage-entities.md) ismertetett programkódot.

<sup>3</sup> kérés a feladat entitások, legfeljebb 1000 feladatok kérelmenként adja vissza. Ha az összes elküldött feladatot nyomon szeretné követni, ezt a top/skip (felső korlát/kihagyás) beállítás segítségével teheti meg az [OData rendszerlekérdezési beállításait](https://msdn.microsoft.com/library/gg309461.aspx) ismertető cikkben leírtak szerint.

<sup>4</sup> A keresőket nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat. További információ [itt](../articles/media-services/previous/media-services-content-protection-overview.md) érhető el.

<sup>5</sup> A tárfiókoknak ugyanahhoz az Azure-előfizetéshez kell tartozniuk.

<sup>6</sup> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. 

>[!NOTE]
> Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket stb. használja, a szabályzatazonosítónak is ugyanannak kell lennie. További információkért és példákért lásd [ezt](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies) a szakaszt.

<sup>7</sup>Ha tartalmat tölt fel egy objektumba történik az Azure Media Services feldolgozni azt a szolgáltatás a médiaelemzés médiafeldolgozói egyikével (vagyis a Media Encoder Standard és a Media Encoder Premium munkafolyamat vagy programmaggal Face Detectorhoz hasonló elemző), Ezután a korlátozások a támogatott maximális fájlméretek tisztában kell lennie. 

Egy blob esetében támogatott maximális mérete jelenleg legfeljebb 5 TB-os Azure Blob Storage-ban. Azonban további korlátozások érvényesek az Azure Media Services a szolgáltatás által használt Virtuálisgép-méretek alapján. Az alábbi táblázatban láthatók a korlátok az egyes, a Media szolgáltatás számára fenntartott egységeket (S1, S2, S3.) Ha a forrásfájl mérete nagyobb, mint a tábla meghatározott keretek, a kódolási feladat sikertelen lesz. Ha a 4K felbontása forrásai hosszú időtartam kódolja, S3 szintű Media szolgáltatás számára fenntartott egységek használata szükséges a teljesítmény elérése érdekében szükségesek. Ha 4K tartalom, amely nagyobb, mint az S3 szintű Media szolgáltatás számára fenntartott egységek a 260 GB-os korlátot, írjon nekünk az amshelp@microsoft.com a támogatásához a lehetséges kezelésükre.

| Media szolgáltatás számára fenntartott egység típusa | Maximális bemeneti mérete (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
