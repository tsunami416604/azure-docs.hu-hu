---
title: Az Azure Media Player szolgáltatáslistája
description: Az Azure Media Player szolgáltatáshivatkozása.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727228"
---
# <a name="feature-list"></a>Szolgáltatáslista #
Itt van a lista a tesztelt funkciók és nem támogatott funkciók:

|                                         | Tesztelt | RÉSZBEN TESZTELT | Kipróbálatlan | Támogatott | MEGJEGYZÉSEK                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Lejátszás                                |        |                  |          |             |                                                                                                                      |
| Egyszerű igény szerinti lejátszás                | X      |                  |          |             | Csak az Azure Media Services-ből származó adatfolyamokat támogatja                                                                      |
| Alapvető élő lejátszás                     | X      |                  |          |             | Csak az Azure Media Services-ből származó adatfolyamokat támogatja                                                                      |
| AES                                     | X      |                  |          |             | Támogatja az Azure Media Services kulcskézbesítési szolgáltatását                                                                   |
| Több DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Támogatja az Azure Media Services kulcskézbesítési szolgáltatását                                                                   |
| Widevine                                |        | X                |          |             | Támogatja a manifesztben vázolt Widevine PSSH dobozokat                                                                    |
| FairPlay                                |        | X                |          |             | Támogatja az Azure Media Services kulcskézbesítési szolgáltatását                                                                   |
| Techs                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash tartalék (FlashSS)                | X      |                  |          |             | Nem minden funkció érhető el ezen a technológián.                                                                         |
| Silverlight tartalék SilverlightSS      | X      |                  |          |             | Nem minden funkció érhető el ezen a technológián.                                                                         |
| Natív HLS-áthaladás (Html5)         |        | X                |          |             | A platformkorlátozások miatt nem minden funkció érhető el ezen a technológián.                                            |
| Szolgáltatások                                |        |                  |          |             |                                                                                                                      |
| API-támogatás                             | X      |                  |          |             | Az ismert problémák listájának megtekintése                                                                                                |
| Egyszerű felhasználói felület                                | X      |                  |          |                                                                                                                                    |
| Inicializálás JavaScript-en keresztül       | X      |                  |          |             |                                                                                                                      |
| Inicializálás videocímkével        |        | X                |          |             |                                                                                                                      |
| Szegmens címzése - időalapú         | X      |                  |          |             |                                                                                                                      |
| Szegmens címzése - index alapú        |        |                  |          | X           |                                                                                                                      |
| Szegmenscímzés - bájt alapú         |        |                  |          | X           |                                                                                                                      |
| Az Azure Media Services URL-újraírója       |        | X                |          |             |                                                                                                                      |
| Kisegítő lehetőségek - Feliratok és feliratok  |        | X                |          |             |  WebVTT támogatott on demand, élő CEA 708 részben tesztelt                                                       |
| Kisegítő lehetőségek – Gyorsbillentyűk                 | X      |                  |          |             |                                                                                                                      |
| Kisegítő lehetőségek - Kontrasztos           |        | X                |          |             |                                                                                                                      |
| Kisegítő lehetőségek – Lap fókusza               |        | X                |          |             |                                                                                                                      |
| Hiba üzenetküldés                         |        | X                |          |             | A hibaüzenetek inkonzisztensek a tech-ek között                                                                         |
| Esemény aktiválása                        | X      |                  |          |             |                                                                                                                      |
| Diagnosztika                             |        | X                |          |             | A diagnosztikai információk csak az AzureHtml5JS-technológián érhetők el, és részben a SilverlightSS-technológián. |
| Testreszabható műszaki rendelés                 |        | X                |          |             |                                                                                                                      |
| Heurisztika - Alap                      | X      |                  |          |             |                                                                                                                      |
| Heurisztika - Testreszabás              |        |                  | X        |             | A testreszabás csak az AzureHtml5JS technológiával érhető el.                                                          |
| Megszakítások                         | X      |                  |          |             |                                                                                                                      |
| Bitráta kiválasztása                          | X      |                  |          |             | Ez az API csak az AzureHtml5JS és a FlashSS-techs érhetők el.                                                    |
| Többhangú adatfolyam                      |        | X                |          |             | Az automatikus hangkapcsoló az AzureHtml5JS és a FlashSS-techek számára támogatott, és az AzureHtml5JS, a FlashSS és a natív Html5 (Safari) felhasználói felületi kiválasztása révén érhető el.  A legtöbb platform ugyanazt a kodek személyes adatokat igényel az audiostreamek (azonos kodek, csatorna, mintavételi arány stb.) közötti váltáshoz. |
| Felhasználói felület honosítása                         |        | X                |          |             |                                                                                                                      |
| Többpéldányos lejátszás                 |        |                  |          | X           | Ez a forgatókönyv egyes technikusok esetében működik, de jelenleg nem támogatott és nem tesztelt. Ön is kap ez a munka segítségével iframe |
| Hirdetések támogatása                             |        | x                |          |             | Az AMP támogatja a vast-kompatibilis VOD-kompatibilis hirdetéskiszolgálókon megjelenő, közép- és utógöstés utáni lineáris hirdetések beillesztését az AzureHtml5JS technológiában |
| Elemzés                               |        | X                |          |             | Az AMP lehetővé teszi az elemzési és diagnosztikai események meghallgatását, hogy az Ön által választott Analytics-háttérrendszernek elküldhesse azokat.  A platform korlátai miatt nem érhetők el minden esemény és tulajdonság a tech-ek között.                                                                            |
| Egyéni skinek                            |        |                  | X        |             | Kapcsolja be a beállításvezérlőket hamisra az AMP-ben, és használja a saját HTML-jét és CSS-ét.           |
| Keresek Bar Súrolás                      |        |                  |          | X           |                                                                                                                      |
| Trick-játék                              |        |                  |          | X           |                                                                                                                      |
| Csak hang                              |        |                  |          | X           | Működhet egyes adaptív streamelési technikai technikai, de jelenleg nem támogatott, és nem működik az AzureHtml5JS. A progresszív MP3 lejátszás akkor működik a HTML5 technológiával, ha a platform támogatja azt.                                                                                                        |
| Csak videó                              |        |                  |          | X           | Működhet egyes adaptív streamelési technikai technikai, de jelenleg nem támogatott, és nem működik az AzureHtml5JS.      |
| Többidőszakos bemutató               |        |                  |          | X                                                                                                                                  |
| Több kameraállás                  |        |                  |          | X           |                                                                                                                      |
| Lejátszási sebesség                          |        | X                |          |             | A lejátszási sebesség a legtöbb esetben támogatott, kivéve a mobil esetet a Chrome részleges hibája miatt                 |

## <a name="next-steps"></a>További lépések ##
- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)