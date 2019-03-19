---
title: Az Azure Media Clipperrel készíthet |} A Microsoft Docs
description: Az Azure Media Clipperrel, egy eszköz, amellyel a klipek eszközökből áttekintése
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 35f1f359b44af00000ccd9047673b80ca541d376
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998204"
---
# <a name="create-clips-with-azure-media-clipper"></a>Készíthet az Azure Media Clipperrel 

Az Azure Media Clipperrel egy ingyenes JavaScript-függvénytárat, amely lehetővé teszi a webes felületet biztosítson a felhasználók Klipek létrehozása. Az eszköz bármilyen weblapon is integrálható, és API-kat biztosít a eszközök betöltése és klipekkel kapcsolatos feladatok elküldése.

Az Azure Media Clipperrel teszi lehetővé:
- Az üzem előtti befutó Trim és utáni állóképek az élő adásokból 
- AMS élő események, az élő adásokból vagy fMP4 VOD fájlok videó pillanatai Compose 
- Több forrásból származó videók összefűzése 
- Az AMS médiatartalmak származó összegző klipek előállításához. 
- Keret pontosságú klip videók 
- Létrehozhat egy dinamikusjegyzék-szűrők élő meglévő és VOD eszközök csoport képek (Képcsoporttal) pontossága 
- Kódolási feladatot hajt végre szemben az eszközök a media services-fiók létrehozásához

Javasolhat új funkciókat, adja meg ötleteit, vagy visszajelzést, az elküldés elemre kattintva [az Azure Media Services UserVoice](https://aka.ms/amsvoice/). Ha rendelkezik, és konkrét problémák, kérdések vagy hibák, dobja el a Media Services-csapat egy sort a Keresés amcinfo@microsoft.com.

Az alábbi ábrán a Clipperrel felületen: ![Az Azure Media Clipperrel](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Kibocsátási megjegyzések
Tekintse meg az alábbi lista a Clipperrel blogbejegyzést, a különböző ismert problémák és a legújabb kiadása a Clipperrel változásnaplója:
- [Blogbejegyzés](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Ismert problémák listája](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Changelog](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Böngésző támogatása
Az Azure Media Clipperrel HTML5-alapú korszerű technológiák használatával, és az alábbi böngészők támogatja:

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54 +
- Safari 10 +
- A Firefox több mint 50

> [!NOTE]
> Az Azure Media Services adatfolyam csak HTML5 lejátszási jelenleg támogatott.

## <a name="language-support"></a>Nyelvi támogatás
A Clipperrel widget 18 a következő nyelveken érhető el:
- kínai (egyszerűsített)
- kínai (hagyományos)
- cseh
- Holland, Flemish
- Angol
- francia
- német
- magyar
- olasz
- japán
- koreai
- lengyel
- portugál (brazíliai)
- portugál (általános)
- orosz
- spanyol
- svéd
- török

## <a name="next-steps"></a>További lépések
Ismerkedés az Azure Media Clipperrel használatával, olvassa el a [bevezetés](media-services-azure-media-clipper-getting-started.md) részleteivel kapcsolatban, hogyan helyezhet üzembe a widget.
