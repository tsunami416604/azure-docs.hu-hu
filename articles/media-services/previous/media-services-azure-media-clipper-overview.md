---
title: Hozzon létre videóklipeket Azure Media Webmontázskészítő |} Microsoft Docs
description: Azure Media Webmontázskészítő, egy eszköz használatával történő megtervezésével media videóklipeket eszközökből áttekintése
services: media-services
keywords: Klip; subclip; kódolás; adathordozó
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f3822386d0d16b1feaf16853424329558a18f910
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788122"
---
# <a name="create-clips-with-azure-media-clipper"></a>Az Azure Media Webmontázskészítő videóklipeket létrehozása
Az Azure Media Webmontázskészítő egy ingyenes JavaScript-függvénytárat, amely lehetővé teszi webes fejlesztők számára a felhasználói felülettel történő media videóklipeket létrehozása. Az eszköz integrálhatók a weblapokat, és API felületeket biztosít az eszközök betöltésekor és Kivágás feladatok elküldésekor.

Az Azure Media Webmontázskészítő teszi lehetővé:
- A előtti lappal Trim utáni slate származó élő és archívumok 
- Videó emeli ki AMS élő eseményeket, élő archívumokat vagy fMP4 VOD-fájlok összeállítása 
- A különböző forrásokból származó ÖSSZEFŰZ videók 
- Az AMS media eszközökből összefoglaló videóklipeket előállításához 
- Keret pontossággal klip videók 
- Dinamikus jegyzék szűrők készítése meglévő élő és VOD eszközök csoport képek (GOP) pontossággal 
- Az eszközök a media services-fiók ellen kódolási feladatok létrehozására

Kérjen új funkciókat, adja meg a ötleteket vagy visszajelzést szeretne küldeni, terjeszt [Azure Media Services UserVoice](http://aka.ms/amsvoice/). Ha rendelkezik, és konkrét problémák, kérdések vagy hibáit, dobja el a Media Services team: egy sor keresés amcinfo@microsoft.com.

Az alábbi ábrán a Webmontázskészítő felület: ![Azure Media Webmontázskészítő](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Kibocsátási megjegyzések
Tekintse meg az alábbi lista a Webmontázskészítő blogbejegyzés, különböző ismert problémák és a legújabb kiadása a Webmontázskészítő a változásnaplója:
- [blogbejegyzés](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Ismert problémák listája](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Változásnaplója](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Webböngésző támogatása
Az Azure Media Webmontázskészítő modern HTML5 technológiákra épül, és támogatja a következő böngészőkben:

- A Microsoft Edge 13 +
- Az Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Azure Media Services-adatfolyamokat csak HTML5 lejátszását jelenleg támogatott.

## <a name="language-support"></a>Nyelvi támogatás
A Webmontázskészítő widget a következő 18 nyelveken érhető el:
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
- portugál (Brazília)
- portugál (Portugália)
- orosz
- spanyol
- svéd
- török

## <a name="next-steps"></a>További lépések
Azure Media Webmontázskészítő használatának megkezdéséhez olvassa el a [bevezetés](media-services-azure-media-clipper-getting-started.md) cikkben talál részletes információt a widget központi telepítéséről.
