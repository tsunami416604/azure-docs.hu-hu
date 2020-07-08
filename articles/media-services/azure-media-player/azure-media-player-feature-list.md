---
title: Azure Media Player szolgáltatások listája
description: A Azure Media Player szolgáltatásra mutató hivatkozás.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727228"
---
# <a name="feature-list"></a>Funkciólista #
Itt láthatók a tesztelt funkciók és a nem támogatott funkciók listája:

|                                         | TESZTELT | RÉSZLEGESEN TESZTELT | KIPRÓBÁLATLAN | TÁMOGATOTT | MEGJEGYZÉSEK                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Lejátszás                                |        |                  |          |             |                                                                                                                      |
| Alapszintű igény szerinti lejátszás                | X      |                  |          |             | Csak Azure Media Services streamek támogatása                                                                      |
| Alapszintű élő lejátszás                     | X      |                  |          |             | Csak Azure Media Services streamek támogatása                                                                      |
| AES                                     | X      |                  |          |             | Támogatja Azure Media Services Key Delivery Service-t                                                                   |
| Több DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Támogatja Azure Media Services Key Delivery Service-t                                                                   |
| Widevine                                |        | X                |          |             | A jegyzékfájlban körvonalazott Widevine PSSH-mezők támogatása                                                                    |
| FairPlay                                |        | X                |          |             | Támogatja Azure Media Services Key Delivery Service-t                                                                   |
| Techs                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash-tartalék (vaku)                | X      |                  |          |             | Nem minden szolgáltatás érhető el ebben a technológiában.                                                                         |
| Silverlight tartalék Silverlight      | X      |                  |          |             | Nem minden szolgáltatás érhető el ebben a technológiában.                                                                         |
| Natív HLS átmenő (Html5)         |        | X                |          |             | A platform korlátozásai miatt nem minden szolgáltatás érhető el ebben a technológiában.                                            |
| Szolgáltatások                                |        |                  |          |             |                                                                                                                      |
| API-támogatás                             | X      |                  |          |             | Lásd: ismert problémák listája                                                                                                |
| Alapszintű felhasználói felület                                | X      |                  |          |                                                                                                                                    |
| Inicializálás JavaScript használatával       | X      |                  |          |             |                                                                                                                      |
| Inicializálás a videó címkén keresztül        |        | X                |          |             |                                                                                                                      |
| Szegmens címzése – időbeli alapú         | X      |                  |          |             |                                                                                                                      |
| Szegmens címzése – index-alapú        |        |                  |          | X           |                                                                                                                      |
| Szegmens címzés – bájt alapú         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services URL-újraíró       |        | X                |          |             |                                                                                                                      |
| Kisegítő lehetőségek – feliratok és feliratok  |        | X                |          |             |  Igény szerint támogatott WebVTT, élő CEA 708 részben tesztelt                                                       |
| Kisegítő lehetőségek – gyorsbillentyűk                 | X      |                  |          |             |                                                                                                                      |
| Kisegítő lehetőségek – kontrasztos megjelenítés           |        | X                |          |             |                                                                                                                      |
| Kisegítő lehetőségek – Tab fókusz               |        | X                |          |             |                                                                                                                      |
| Üzenetküldési hiba                         |        | X                |          |             | A hibák inkonzisztensek az egyes technológiákban                                                                         |
| Esemény-eseményindító                        | X      |                  |          |             |                                                                                                                      |
| Diagnosztika                             |        | X                |          |             | A diagnosztikai adatok csak a AzureHtml5JS műszaki és részben elérhetők a Silverlight-technológiában. |
| Testreszabható technikai sorrend                 |        | X                |          |             |                                                                                                                      |
| Heurisztikus – alapszintű                      | X      |                  |          |             |                                                                                                                      |
| Heurisztikus – Testreszabás              |        |                  | X        |             | A testreszabás csak a AzureHtml5JS Tech esetében érhető el.                                                          |
| Folytonossági                         | X      |                  |          |             |                                                                                                                      |
| Bitráta kiválasztása                          | X      |                  |          |             | Ez az API csak a AzureHtml5JS és a villanások technológiájában érhető el.                                                    |
| Több hangos Stream                      |        | X                |          |             | A programozott hangkapcsolót a AzureHtml5JS és a Flashs technológiája támogatja, és a AzureHtml5JS, a Flashs és a natív Html5 (a Safariban) felhasználói felületének kiválasztásán keresztül érhető el.  A legtöbb platformon ugyanazt a kodeket kell magánjellegű adatokra váltani a hangstreamek váltásához (ugyanaz a kodek, csatorna, mintavételi sebesség stb.). |
| Felhasználói felület honosítása                         |        | X                |          |             |                                                                                                                      |
| Több példányos lejátszás                 |        |                  |          | X           | Ez a forgatókönyv bizonyos technológiák esetében működhet, de jelenleg nem támogatott és nem tesztelt. Azt is megteheti, hogy az IFRAMES használatával működik. |
| Hirdetések támogatása                             |        | x                |          |             | Az AMP támogatja a nagy teljesítményű Active Directory-kiszolgálókat a AzureHtml5JS-technikai platformon a VOD-hoz kapcsolódó előre közép-és utólagos lineáris hirdetéseket. |
| Elemzés                               |        | X                |          |             | Az AMP lehetővé teszi az elemzési és diagnosztikai események figyelését, hogy az Ön által választott elemzési háttérbe küldjék.  Az események és tulajdonságok a platform korlátai miatt nem érhetők el a technológiák között.                                                                            |
| Egyéni bőrök                            |        |                  | X        |             | Állítsa be a vezérlőket hamis értékre a saját HTML és CSS használatával.           |
| Sáv tisztításának megkeresése                      |        |                  |          | X           |                                                                                                                      |
| Trükk – lejátszás                              |        |                  |          | X           |                                                                                                                      |
| Csak hang                              |        |                  |          | X           | Az adaptív adatfolyam-továbbításhoz bizonyos technológiákban működhet, de jelenleg nem támogatott, és nem működik a AzureHtml5JS-ben. A progresszív MP3-lejátszás képes együttműködni a HTML5-technológiával, ha a platform támogatja azt.                                                                                                        |
| Csak videó                              |        |                  |          | X           | Az adaptív adatfolyam-továbbításhoz bizonyos technológiákban működhet, de jelenleg nem támogatott, és nem működik a AzureHtml5JS-ben.      |
| Több időszakos bemutató               |        |                  |          | X                                                                                                                                  |
| Több kamera látószöge                  |        |                  |          | X           |                                                                                                                      |
| Lejátszás sebessége                          |        | X                |          |             | A legtöbb forgatókönyv esetén a lejátszási sebességet a Chrome részleges hibája miatt a legtöbb esetben támogatja a rendszer.                 |

## <a name="next-steps"></a>További lépések ##
- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)