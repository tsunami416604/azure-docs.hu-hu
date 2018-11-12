---
title: Az Azure Media Clipperrel készíthet |} A Microsoft Docs
description: Az Azure Media Clipperrel, egy eszköz, amellyel a klipek eszközökből áttekintése
services: media-services
keywords: Klip; részklip; kódolás; adathordozó
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0fc04fb64e6d34c46f20b4ba5ff8a1ffc8da9c31
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237910"
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

Az alábbi ábrán a Clipperrel felület: ![Azure Media Clipperrel](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Kibocsátási megjegyzések
Tekintse meg az alábbi lista a Clipperrel blogbejegyzést, a különböző ismert problémák és a legújabb kiadása a Clipperrel változásnaplója:
- [Blogbejegyzés](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Ismert problémák listája](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Változásnaplójában](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Böngésző támogatása
Az Azure Media Clipperrel HTML5-alapú korszerű technológiák használatával, és az alábbi böngészők támogatja:

- A Microsoft Edge 13 +
- Az Internet Explorer 11 +
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
