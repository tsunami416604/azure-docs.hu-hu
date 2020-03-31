---
title: Az Azure Media Services magas rendelkezésre állású streamelése
description: Megtudhatja, hogyan lehet feladatátvételt egy másodlagos Media Services-fiókba, ha egy regionális adatközpont-kimaradás vagy hiba történik.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899229"
---
# <a name="media-services-high-availability-streaming"></a>Media Services magas rendelkezésre állású streamelés

Az Azure Media Services jelenleg nem biztosít azonnali feladatátvételt a szolgáltatás, ha van egy regionális adatközpont kimaradás vagy az alapul szolgáló összetevő vagy függő szolgáltatások meghibásodása. Ez a cikk útmutatást ad, hogyan hozhat létre video-on-demand régiók közötti streamelés.

## <a name="prerequisites"></a>Előfeltételek

[Áttekintendő, hogyan építhetünk fel régiók közötti kódolási rendszert](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Hogyan készítsünk video-on-demand régiók közötti streaming 

* Az igény szerinti, régiók közötti streamelés magában foglalja [az eszközök,](assets-concept.md) [a tartalomkulcs-házirendek](content-key-policy-concept.md) (ha van használat esetén) megkettőzését , [a streamelési házirendeket](streaming-policy-concept.md)és [a streamelési lokátorokat.](streaming-locators-concept.md) 
* Mindkét régióban létre kell hoznia a házirendeket, és naprakészen kell tartania azokat. 
* A streamelési lokátorok létrehozásakor ugyanazt a lokátor-azonosítóértéket, ContentKey-azonosító-értéket és ContentKey-értéket kell használnia.  
* Ha a tartalmat kódolja, javasoljuk, hogy kódolja a tartalmat az A régióban, és tegye közzé, majd másolja a kódolt tartalmat a B régióba, és tegye közzé ugyanazokat az értékeket használva, mint az A régióban.
* A Traffic Manager az eredeti és a kulcskézbesítési szolgáltatás állomásnevein használható (a Media Services konfigurációjában ez úgy fog kinézni, mint egy egyéni kulcskiszolgáló URL-címe).

## <a name="next-steps"></a>További lépések

nézd meg:

* [Oktatóanyag: Távoli fájl kódolása URL alapján, és streamelése](stream-files-dotnet-quickstart.md)
* [kódminták](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
