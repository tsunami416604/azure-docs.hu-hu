---
title: Klipek létrehozása az Azure Media Clipper szolgáltatással | Microsoft Docs
description: Az Azure Media Clipper áttekintése, amely az eszközökről származó médiaklipek létrehozására szolgáló eszköz
services: media-services
keywords: klip; alklip; kódolás; média
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685019"
---
# <a name="create-clips-with-azure-media-clipper"></a>Klipek létrehozása az Azure Media Clipper-vel 

Az Azure Media Clipper egy ingyenes JavaScript-kódtár, amely lehetővé teszi, hogy a webfejlesztők a médiaklipek létrehozására szolgáló felületet biztosítanak a felhasználóknak. Ez az eszköz integrálható bármely weboldalba, és API-kat biztosít az eszközök betöltéséhez és a kivágási feladatok elküldéséhez.

Az Azure Media Clipper a következőket teszi lehetővé:
- A pre-Slate és a Slate közzététele az élő archívumokból 
- Videók kiemelése AMS élő eseményekről, élő archívumokból vagy fMP4 VOD-fájlokból 
- Videók összefűzése több forrásból 
- Összefoglaló klipek készítése az AMS Media-eszközökről 
- Videók a keret pontosságával 
- Dinamikus jegyzékfájl-szűrők előállítása meglévő élő és VOD-eszközökön a csoport – kép (GOP) pontossággal 
- Kódolási feladatok létrehozása a Media Services-fiók eszközein

Új funkciók igényléséhez adjon meg ötleteket vagy visszajelzést, és küldje el a [Azure Media Services UserVoice](https://aka.ms/amsvoice/). Ha rendelkezik és adott problémákkal, kérdésekkel vagy hibákkal kapcsolatos hibákat talál, dobja el a Media Services csapatának egy sorát amcinfo@microsoft.com.

Az alábbi ábra a Clippers felületet szemlélteti: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Kibocsátási megjegyzések
Tekintse meg a következő listát a Clipper-blogbejegyzések, különböző ismert problémák és a Clipper legújabb kiadásának changelog:
- [Blogbejegyzés](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Ismert problémák listája](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Módosítási napló](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Böngésző-támogatás
Az Azure Media Clipper a modern HTML5-technológiák használatával készült, és a következő böngészőket támogatja:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Jelenleg csak a Azure Media Services streamek HTML5-lejátszása támogatott.

## <a name="language-support"></a>Nyelvi támogatás
A Clipper widget a következő 18 nyelven érhető el:
- Kínai (egyszerűsített)
- Kínai (hagyományos)
- cseh
- Holland, flamand
- Angol
- francia
- német
- magyar
- olasz
- japán
- koreai
- lengyel
- Portugál (brazíliai)
- Portugál (portugáliai)
- orosz
- spanyol
- svéd
- török

## <a name="next-steps"></a>További lépések
Az Azure Media Clipper használatának megkezdéséhez olvassa el az [első lépések](media-services-azure-media-clipper-getting-started.md) című cikket, amely részletesen ismerteti a widget üzembe helyezésének lépéseit.
