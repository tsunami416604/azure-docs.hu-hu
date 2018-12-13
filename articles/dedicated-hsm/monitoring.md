---
title: Megfigyelési lehetőségek – Azure dedikált HSM-be |} A Microsoft Docs
description: Azure dedikált HSM megfigyelési lehetőségek és figyelési feladatok áttekintése
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: b577c2c68512c6e7e36f80b75245d9db4f839d9e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085689"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Az Azure monitoring dedikált HSM

Az Azure dedikált HSM Service egy fizikai eszköz az egyetlen ügyfél használata révén a teljes körű rendszergazdai vezérlés és felügyeleti felelősséget. Az eszköz elérhetővé tett egy [Gemalto SafeNet Luna 7 HSM modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  A Microsoft nem jutnak felügyeleti figyelési szerepkörként fizikai soros port melléklet túl az ügyfél egyszer kiépítve. Ennek eredményeképpen ügyfelek szokásos üzemeltetési tevékenységeket, beleértve a átfogó figyelési felelőssége, és elemzési jelentkezzen.
Ügyfelek teljes körűen felelős az alkalmazásokat, amelyek használni a HSM-EK és a támogatási vagy tanácsadási segítséget Gemalto együttműködve. Ügyfél tulajdonosi működési higiéniai mértékben, mert nincs lehetőség a Microsoft a szolgáltatás magas rendelkezésre állási garancia bármilyen ajánlat. Fontos, hogy az ügyfél felelőssége annak biztosítása érdekében az alkalmazások helyesen van konfigurálva a magas rendelkezésre állás. A Microsoft ellenőrzi és eszközök állapotának és a hálózati kapcsolat fenntartása érdekében.

## <a name="microsoft-monitoring"></a>A Microsoft monitoring

Alapértelmezés szerint az SNMP és a soros port van az Gemalto SafeNet eszközön, használja az eszköz figyelésének közül. A Microsoft a soros port kapcsolat használt Eszközállapot az alapvető telemetriai beolvasni az eszköz csatlakozni egy fizikai eszközként. Ez magában foglalja az elemek, például a hőmérsékletet és az összetevő állapotát – például a tápegységek és a követőknek.
Ennek érdekében a Microsoft nem rendszergazda jogosultságú "figyelés" szerepkör állítsa be az Gemalto eszközön használja. Ez a szerepkör lehetővé teszi a telemetriai adatok lekéréséhez, de nem ad hozzáférést az eszköz felügyeleti feladat tekintetében, vagy bármilyen módon titkosítási információk megtekintése. Ügyfeleink eszköz valóban kezelése, felügyelete és használata saját bizalmas titkosítási kulcs tárolása is biztosítani. Amennyiben bármely ügyfél nem ezt a minimális hozzáférést alapvető állapotfigyelését elégedett, a figyelési fiók letiltását rendelkeznek. Ez a nyilvánvaló következménye, hogy a Microsoft információ nem fog rendelkezni, és ezért nem képes bármilyen proaktív értesítést küldenek az Eszközállapot problémák. Ebben a helyzetben az ügyfél felelős az eszköz állapotát.
A figyelő függvény magára az egészségügyi adatok a 10 percenként lekérdezi az eszköz van beállítva. A hiba magában rejtő jellege miatt soros kommunikációs csak egy egy órás időszakban több negatív állapotjelző után szeretne riasztást generál. Ez a riasztás végső soron a proaktív ügyfél-kommunikáció értesítése a probléma vezetne.
A probléma jellegétől függően a megfelelő lépések gyakorolt hatást, és győződjön meg, hogy alacsony kockázat szervizelési szeretne tenni. Például egy áramellátása meghibásodott nincs eredő ritkáról gyakori elérésű felcserélés eljárás meghamisítása esemény, így kis hatású és minimális kockázati művelet is elvégezhető. Egyéb eljárások szükség lehet egy eszköz zeroized és – figyelmeztetés megszüntetésről minimalizálása érdekében az ügyfél biztonsági kockázatot jelent. Ebben a helyzetben ügyfél lenne egy másik eszköz kiépítése, csatlakoztassa újra a magas rendelkezésre állású párosítás így elindítása az eszköz szinkronizálása. Normál működés folytatódhat minimális időben, a minimális és a legalacsonyabb biztonsági kockázatot jelent.  

## <a name="customer-monitoring"></a>Ügyfelek figyelése

A dedikált HSM szolgáltatást egy értékajánlat a vezérlő, az ügyfél lekéri az eszköz különösen figyelembe véve, felhő-i eszköz. Ez a vezérlő következménye feladata figyelését és kezelését, az eszköz állapotát. A Gemalto SafeNet eszköz SNMP és Syslog megvalósítási útmutatást tartalmaz. A dedikált HSM szolgáltatást használó ügyfeleink számára ajánlott, hogy ezzel, akkor is, ha a Microsoft figyelő fiók aktív marad, és gondolja át, kötelező Ha, tiltsa le a Microsoft monitor-fiókhoz.
Mindkét módszer érhető el szeretné engedélyezi egy ügyfélnek, azonosíthatja a problémákat, és a megfelelő korrekciós munkahelyi kezdeményezni a Microsoft ügyfélszolgálatával.

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatás, például a magas rendelkezésre állással és biztonsággal kapcsolatos összes főbb fogalmakat például megértsük bármely eszközök üzembe helyezését és alkalmazás-tervezés vagy üzembe helyezés előtt. További fogalom szintű témakörök:

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
