---
title: Sávszélességgel kapcsolatos javaslatok távoli munkamenetekhez – Azure
description: A távoli munkamenetekhez elérhető sávszélességgel kapcsolatos javaslatok.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695316"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>A távoli munkamenetek sávszélességgel kapcsolatos javaslatai

Távoli Windows-munkamenet használata esetén a hálózat rendelkezésre álló sávszélessége jelentősen befolyásolja a felhasználói élmény minőségét. A különböző alkalmazások és megjelenítési felbontások eltérő hálózati konfigurációt igényelnek, ezért fontos, hogy a hálózat az igényeinek megfelelő módon legyen konfigurálva. Ez a cikk az egyes számítási feladatok ajánlott sávszélességét ismerteti.

>[!NOTE]
>Az alábbi javaslatok a 0,1%-nál kevesebb veszteséggel rendelkező hálózatokra vonatkoznak.

## <a name="applications"></a>Alkalmazások

A következő táblázat a zökkenőmentes felhasználói élmény minimális követelményeit sorolja fel. 

|Számítási feladat        |Mintaalkalmazások                                                                                           |Ajánlott sávszélesség|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Adott feladattal foglalkozó dolgozó     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1.5 @ no__t – 0Mbps             |
|Office Worker   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer                                        |3 @ no__t – 0Mbps               |
|Knowledge Worker|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer, Java                                  |5 @ no__t – 0Mbps               |
|Munkavégző    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer, Java, CAD/CAM, illusztráció/közzététel|15 @ no__t – 0Mbps              |

Ne feledje, hogy a hálózatra helyezett terhelés az alkalmazás számítási feladatának és a megjelenítési felbontásának megfelelően függ. Ha a frame rate vagy a Display felbontása növekszik, a sávszélességre vonatkozó követelmény is emelkedni fog. Ha például egy nagy felbontású kijelzővel rendelkező kis méretű számítási feladat több elérhető sávszélességet igényel, mint a normál vagy alacsony felbontású, könnyű számítási feladatok.

Más forgatókönyvek esetén a sávszélességre vonatkozó követelmények változhatnak attól függően, hogy miként használják őket, például:

- Hang-vagy videokonferencia
- Valós idejű kommunikáció
- Streaming 4K-videó

Győződjön meg arról, hogy ezeket a forgatókönyveket teszteli a telepítésben a szimulációs eszközök, például a login VSI használatával. Megváltozhat a terhelési méret, a stressz-tesztek futtatása és az általános felhasználói forgatókönyvek tesztelése a távoli munkamenetekben, hogy jobban megértse a hálózat követelményeit. 

## <a name="display-resolutions"></a>Képernyőfelbontások megjelenítése

A különböző megjelenítési felbontások különböző elérhető sávszélességet igényelnek. A következő táblázat azokat a sávszélességeket sorolja fel, amelyeket a szokásos képernyőfelbontások esetében ajánlott zökkenőmentes felhasználói élményre kiterjedően, 30 képkocka/másodperc (FPS) képkocka-sebességgel. Ne feledje, hogy a 30 fps alatti képkockákat érintő forgatókönyvek, például a statikus szöveg olvasása kevesebb elérhető sávszélességet igényelnek. 

|Átlagos képernyőfelbontás 30 fps-nál    |Ajánlott sávszélesség|
|-----------------------------------------|---------------------|
|Körülbelül 1024 × 768 px                      |1,5 Mbps             |
|Körülbelül 1280 × 720 px                      |3 Mbps               |
|Körülbelül 1920 × 1080 px                     |5 Mbps               |
|Körülbelül 3840 × 2160 px (4K)                |15 Mbps              |
