---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334858"
---
>[!NOTE]
>A nem rögzített erőforrások esetében nyisson meg egy támogatási jegyet a kvóták növelésének kéréséhez. Ne hozzon létre további Azure Media Services-fiókokat, hogy magasabb korlátokat szerezzen.

| Erőforrás | Korlát | 
| --- | --- | 
| Azure Media Services-fiókok egyetlen előfizetésben | 25 (rögzített) |
| Media-szolgáltatások kalkulátái számára fenntartott egységek Media Services-fiókonként |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Feladatok Media Services-fiókonként | 50,000<sup>2</sup> |
| Kapcsolt műveletek feladatonként | 30 (rögzített) |
| Eszközök Media Services-fiókonként | 1,000,000|
| Objektumok műveletenként | 50 |
| Objektumok feladatonként | 100 |
| Egy objektumhoz egyszerre társított egyedi keresők | 5<sup>4</sup> |
| Élő csatornák Media Services-fiókonként |5|
| Leállított állapotú programok csatornánként |50|
| Futó állapotú programok csatornánként |3|
| A Media Services-fiókonként leállított vagy futó végpontok streamelése|2|
| Streamelési egységek streamvégpontonként |10 |
| Tárfiókok | 1000<sup>5</sup> (rögzített) |
| Házirendek | 1,000,000<sup>6</sup> |
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásához támogatott maximális fájlméret korlátozva van. <sup>7.</sup> |

<sup>1 1</sup> Ha például a típust S2-ről S1-re módosítja, a maximális fenntartott egységkorlátok alaphelyzetbe állnak.

<sup>2.</sup> Ez a szám magában foglalja a várólistán lévő, befejezett, aktív és megszakított feladatokat. Nem tartalmazza a törölt feladatokat. A régi feladatokat törölheti az **IJob.Delete** vagy a **DELETE** HTTP kérés használatával.

2017. április 1-je után a fiókban lévő minden 90 napnál régebbi feladatrekord automatikusan törlődik a kapcsolódó feladatrekordokkal együtt. Az automatikus törlés akkor is megtörténik, ha a rekordok teljes száma nem éri el a maximális kvótát. A feladat- és feladatadatok archiválásához használja az [Eszközök kezelése a Media Services .NET SDK szolgáltatással](../articles/media-services/previous/media-services-dotnet-manage-entities.md)című könyvében leírt kódot.

<sup>3. 20 0</sup> Amikor feladatentitások listázására irányuló kérelmet nyújt be, kérésenként legfeljebb 1000 feladatot ad vissza. Az összes elküldött feladat nyomon követéséhez használja az [OData rendszer lekérdezési beállításaiban](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))leírt felső vagy kihagyott lekérdezéseket.

<sup>4.</sup> A lokátorok at nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha különböző hozzáférési jogokat szeretne biztosítani az egyes felhasználók számára, használjon digitális jogkezelési (DRM) megoldásokat. További információt a Tartalom védelme az Azure Media Services szolgáltatással című [témakörben talál.](../articles/media-services/previous/media-services-content-protection-overview.md)

<sup>5.</sup> A tárfiókoknak ugyanabból az Azure-előfizetésből kell származniuk.

<sup>6.</sup> A Media Services különböző irányelveileg 1 000 000 szabályzat ot állapítanak meg. Példa erre a Lokátor-házirend vagy a ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Ha mindig ugyanazokat a napokat és hozzáférési engedélyeket használja, használja ugyanazt a házirend-azonosítót. További információt és példát [az Eszközök kezelése a Media Services .NET SDK szolgáltatással című](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)témakörben talál.

<sup>7.</sup> Az egyetlen blob maximális támogatott mérete jelenleg legfeljebb 5 TB az Azure Blob Storage-ban. További korlátozások vonatkoznak a Media Services a szolgáltatás által használt virtuális gép méretealapján. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozása (kódolása vagy elemzése) eredményeként létrehozott fájlokra vonatkozik. Ha a forrásfájl nagyobb, mint 260 GB, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 adathordozó-lefoglalt egységek rekedéseit mutatja be. Ha a forrásfájl nagyobb, mint a táblázatban meghatározott korlátok, a kódolási feladat sikertelen lesz. Ha hosszú időtartamú 4K felbontású forrásokat kódol, a szükséges teljesítmény eléréséhez S3 média számára fenntartott egységeket kell használnia. Ha az S3-ban fenntartott egységekre vonatkozó 260 GB-os korlátnál nagyobb 4K-s tartalommal rendelkezik, nyisson meg egy támogatási jegyet.

|Adathordozóáltal lefoglalt egység típusa    |Maximális bemeneti méret (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
