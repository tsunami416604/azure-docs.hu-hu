---
title: Figyelési lehetőségek - Azure dedikált HSM | Microsoft dokumentumok
description: Az Azure dedikált HSM-figyelési lehetőségeinek áttekintése és a felügyeleti feladatok
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881425"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Az Azure dedikált HSM-figyelése

Az Azure dedikált HSM-szolgáltatás egy fizikai eszköz egyetlen ügyfél általi használatra, teljes körű felügyeleti felügyelet és felügyeleti felelősség. A rendelkezésre bocsátott eszköz egy [Gemalto SafeNet Luna 7 HSM modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  A Microsoft nem rendelkezik rendszergazdai hozzáféréssel, ha egy ügyfél kiépíti, a fizikai soros portmellékleten kívül, mint figyelési szerepkört. Ennek eredményeképpen az ügyfelek felelősek a tipikus működési tevékenységekért, beleértve az átfogó figyelést és a naplóelemzést.
Az ügyfelek teljes mértékben felelősek a HSM-eket használó alkalmazásokért, és a Gemalto-val együttműködve támogatást vagy tanácsadást nyújtanak. Az üzemeltetési higiénia identikai ügyféláltali tulajdonának mértéke miatt a Microsoft nem tud semmilyen magas rendelkezésre állási garanciát nyújtani erre a szolgáltatásra. Az ügyfél felelőssége annak biztosítása, hogy alkalmazásaik megfelelően legyenek konfigurálva a magas rendelkezésre állás elérése érdekében. A Microsoft figyeli és fenntartja az eszköz állapotát és a hálózati kapcsolatot.

## <a name="microsoft-monitoring"></a>A Microsoft figyelése

A használatban lévő Gemalto SafeNet eszköz alapértelmezés szerint SNMP és soros porttal rendelkezik az eszköz figyelésének lehetőségeként. A Microsoft a soros portkapcsolatot fizikai eszközként használta az eszközhöz való csatlakozáshoz az eszköz állapotának alapvető telemetriája lekéréséhez. Ez magában foglalja az olyan elemeket, mint a hőmérséklet és az alkatrész állapota, mint például a tápegységek és a ventilátorok.
Ennek elérése érdekében a Microsoft a Gemalto eszközön beállított nem felügyeleti "figyelő" szerepkört használja. Ez a szerepkör lehetővé teszi a telemetriai adatok lekérését, de nem biztosít hozzáférést az eszközhöz a felügyeleti feladat vagy bármilyen módon a kriptográfiai adatok megtekintése szempontjából. Ügyfeleink biztosak lehetnek abban, hogy az eszközük valóban a sajátjuk a bizalmas titkosítási kulcstárolás kezelésére, felügyeletére és használatára. Abban az esetben, ha bármely ügyfél nem elégedett ezzel a minimális hozzáféréssel az alapvető állapotfigyeléshez, lehetősége van letiltani a figyelési fiókot. Ennek nyilvánvaló következménye, hogy a Microsoft nak nem lesz információja, és így nem lesz képes proaktív értesítést adni az eszköz állapotáról. Ebben a helyzetben az ügyfél felelős az eszköz állapotáért.
Maga a figyelőfunkció úgy van beállítva, hogy 10 percenként leállítsa az eszközt az állapotadatok leéséhez. A soros kommunikáció hibaveszélyes jellege miatt csak egy órán keresztül több negatív állapotjelző tanod riasztást. Ez a riasztás végső soron egy proaktív ügyfél-kommunikációhoz vezetne, amely értesíti a problémát.
A probléma jellegétől függően megfelelő lépéseket tennének a hatás csökkentése és az alacsony kockázatú felszámolás biztosítása érdekében. Például az áramellátás meghibásodása egy üzem közbeni csereügylet, amelynek nincs eredő szabotázseseménye, így alacsony ütéssel és minimális működési kockázattal hajtható végre. Más eljárások szükségessé tehetik az eszköz nullázását és megszüntetését, hogy minimálisra csökkentsék az ügyfélre jelentett biztonsági kockázatot. Ebben az esetben az ügyfél egy másik eszközt létesítene, újra csatlakozik egy magas rendelkezésre állású párosításhoz, így elindítja az eszköz szinkronizálását. A normál működés minimális idő alatt folytatódna, minimális megszakítással és a legkisebb biztonsági kockázattal.  

## <a name="customer-monitoring"></a>Ügyfélfigyelés

A dedikált HSM szolgáltatás értékajánlata az a vezérlő, amelyet az ügyfél kap az eszközről, különösen, ha figyelembe vesszük, hogy felhőalapú eszközről van szó. Ennek a vezérlőnek a következménye az eszköz állapotának figyelése és kezelése. A Gemalto SafeNet eszköz útmutatást nyújt az SNMP és a Syslog implementációhoz. A dedikált HSM-szolgáltatás ügyfeleinek akkor is ajánlott ezt használni, ha a Microsoft-figyelőfiók aktív marad, és kötelezőnek kell tekinteniük, ha letiltják a Microsoft figyelőfiókját.
Mindkét rendelkezésre álló technika lehetővé tenné az ügyfél számára, hogy azonosítsa a problémákat, és hívja a Microsoft ügyfélszolgálatát a megfelelő javítási munka kezdeményezéséhez.

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatás minden kulcsfontosságú fogalma, például a magas rendelkezésre állás és a biztonság, például, jól érthető, mielőtt bármilyen eszköz kiépítése és az alkalmazás tervezése vagy üzembe helyezése. További koncepciószintű témák:

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
