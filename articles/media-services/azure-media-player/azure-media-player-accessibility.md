---
title: Az Azure Media Player kisegítő lehetőségei
description: További információ az Azure Media Player kisegítő lehetőségeiről.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726556"
---
# <a name="accessibility"></a>Kisegítő lehetőségek #

Az Azure Media Player olyan képernyőolvasó-képességekkel működik együtt, mint a Windows Narrátor és az Apple OSX/iOS VoiceOver. A felhasználói felület gombjaihoz alternatív címkék állnak rendelkezésre, és a képernyőolvasó képes olvasni ezeket az alternatív címkéket, amikor a felhasználó rájuk navigál. További konfigurációk állíthatók be az operációs rendszer szintjén.

## <a name="captions-and-subtitles"></a>Feliratok és feliratok ##

Jelenleg az Azure Media Player támogatja a webvtt formátumú és a CEA 708-at használó élő események feliratozását. A TTML formátum jelenleg nem támogatott. A feliratok és opcionális feliratok segítségével a lejátszó szélesebb közönség igényeit tudja kiszolgálni, például hallássérültekét vagy olyan személyekét, akik más nyelven szeretnék a feliratokat olvasni. A feliratok segítségével maga a videó is jobban követhető, segítik a megértést, valamint hanghatásokra érzékeny környezetben (például munkahelyen) is lehetővé teszik a videó megtekintését.  

## <a name="high-contrast-mode"></a>Kontrasztos mód ##

Az Azure Media Player alapértelmezett felhasználói felülete megfelel a legtöbb, nagy kontrasztú böngészős nézetmódnak. A konfigurációk az operációs rendszer szintjén állíthatók be.

## <a name="mobility-options"></a>Mobilitási lehetőségek ##

### <a name="tabbing-focus"></a>A fókusz be- és kibőbei ###

Az általános HTML-szabványok által biztosított tabbing fókusz az Azure Media Player ben érhető el. A lapélesékbeállítás engedélyezéséhez `tabindex=0` hozzá kell adnia (vagy egy másik értéket, `<video>` ha érti, hogy a HTML-ben milyen hatással van a lapsorrend) a HTML-hez: `<video ... tabindex=0>...</video>`. Egyes platformokon a vezérlők fókusza csak akkor lehet jelen, ha a vezérlők láthatók, és ha a platform támogatja ezeket a képességeket.

Ha a tabbing fókusz engedélyezve van, a végfelhasználó hatékonyan navigálhat és vezérelheti a videolejátszót az egértől függően. Minden helyi menü vagy vezérelhető elem lehet navigálni, hogy nyomja meg a fül gombot, és a kiválasztott enter vagy szóköz. A helyi menü beírása vagy szóközbillentyűbe való beírása kibontja azt, így a végfelhasználó folytathatja a tabbing gombot, hogy kiválasszon egy menüpontot. Ha elérte a kívánt elemet, az Enter vagy a Szóköz billentyűvel választhatja ki.

### <a name="hotkeys"></a>Gyorsbillentyűk ###

Az Azure Media Player támogatja a billentyűzet gyorsbillentyűjének vezérlését. Egy webböngészőben az alapul szolgáló videoelem vezérlésének egyetlen módja az, ha a lejátszóra összpontosít. Ha már a lejátszóra koncentrál, a gyorsbillentyű vezérelheti a lejátszó működését.  Az alábbi táblázat a különböző billentyűparancsokat és a hozzájuk rendelt műveleteket tartalmazza:

| Billentyűparancs              | Viselkedés                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Megnyitja vagy bezárja a lejátszó teljes képernyős nézetét.                                  |
| M/m                  | Elnémítja a hangerőt, vagy feloldja a némítást.                                          |
| Felfelé és lefelé nyíl    | Növeli és csökkenti a lejátszó hangerejét.                                    |
| Balra és jobbra nyíl | Vissza és előre ugrik a videóban.                                  |
| 0,1,2,3,4,5,6,7,8,9  | A videó előrehaladása a\- lenyomott billentyűtől függően 0% 90%-ra változik |
| Kattintson a Művelet gombra         | Lejátssza vagy szünetelteti a videót.                                                   |

## <a name="next-steps"></a>További lépések

<!---Some context for the following links goes here--->
- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)