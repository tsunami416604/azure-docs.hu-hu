---
title: Kvóták és korlátozások a Azure Media Services v3-ban | Microsoft Docs
description: Ez a témakör a Microsoft Azure Media Services v3 kvótáit és korlátozásait ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888427"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóták és korlátozások a Azure Media Services v3-ban

Ez a cikk a Azure Media Services v3 kvótáit és korlátozásait ismerteti.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egy Azure Media Services-fiókra jutó adategység | 1,000,000|
| Dinamikusjegyzék-szűrők|100|
| JobInputs | 50 (rögzített)|
| JobOutputs | 20 (rögzített) |
| TransformOutputs egy átalakításban | 20 (rögzített) |
| Fájlok száma JobInput|10 (rögzített)|
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásakor támogatott maximális fájlméret megengedett. <sup>1</sup> |
| Feladatok/Media Services fiók | 500 000 <sup>(2)</sup> (rögzített)|
| Élő események Media Services-fiókonként |5|
| Egyetlen előfizetésben lévő fiókok Media Services | 25 (rögzített) |
| Élő kimenet/élő esemény |3 <sup>(3)</sup> |
| Élő kimenet maximális időtartama | 25 óra |
| Tárfiókok | 100<sup>(4)</sup> (rögzített) |
| Adatfolyam-végpontok (leállított vagy futó) Media Services fiókkal|2 (rögzített)|
| Streamelési szabályok | 100 <sup>(5)</sup> |
| Átalakítások száma Media Services fiókkal | 100 (rögzített)|
| Egy adott eszközhöz társított egyedi streaming-lokátorok | 100<sup>(6)</sup> (rögzített) |
| A tartalmi kulcsokra vonatkozó házirend beállításai |30 | 
| Licencek havonta az egyes DRM-típusok esetében Media Services Key Delivery Service-fiókban|1,000,000|

<sup>1</sup> az egyes Blobok maximális mérete jelenleg legfeljebb 5 TB lehet az Azure Blob Storageban. A további korlátozások a szolgáltatás által használt virtuálisgép-méretek alapján Media Services vonatkoznak. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozás (kódolás vagy elemzés) eredményeképpen generált fájlokra is vonatkozik. Ha a forrásfájl mérete meghaladja a 260 GB-ot, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 Media szolgáltatás számára fenntartott egységek korlátozásait mutatja be. Ha a forrásfájl nagyobb a táblázatban megadott korlátoknál, a kódolási feladata meghiúsul. Ha hosszú ideig kódolja a 4K-feloldási forrásokat, a szükséges teljesítmény eléréséhez S3 Media szolgáltatás számára fenntartott egységeket kell használnia. Ha olyan 4K-tartalommal rendelkezik, amely nagyobb, mint az S3 Media szolgáltatás számára fenntartott egységek 260 GB-os korlátja, nyisson meg egy támogatási jegyet.

|Media szolgáltatás számára fenntartott egység típusa   |Maximális bemeneti méret (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> ez a szám üzenetsor-, befejezett, aktív és megszakított feladatokat tartalmaz. Nem tartalmazza a törölt feladatokat. 

A fiók 90 napnál régebbi feladatait automatikusan törli a rendszer, még akkor is, ha a rekordok teljes száma nem éri el a maximális kvótát. 

<sup>3</sup> az élő kimenetek a létrehozás után kezdődnek, és a törlés után leállnak.

<sup>4</sup> a Storage-fiókoknak ugyanahhoz az Azure-előfizetéshez kell tartoznia.

<sup>5</sup> egyéni [adatfolyam-házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies)használata esetén a szabályzatok korlátozott készletét kell megterveznie a Media Service-fiókjához, és újra fel kell használni azokat a StreamingLocators, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. Ne hozzon létre új folyamatos átviteli szabályzatot minden egyes adatfolyam-keresőhöz.

<sup>6</sup> a streaming-lokátorok nem a felhasználónkénti hozzáférés-vezérlés kezelésére szolgálnak. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="support-ticket"></a>Támogatási jegy

A nem rögzített erőforrások esetében a [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kérheti a kvóták kiemelését. A kérelemben szereplő részletes információkat a kívánt kvóta változásaira, a használati esetekre és a szükséges régiókra vonatkozóan adja meg. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>Következő lépések

[Áttekintés](media-services-overview.md)
