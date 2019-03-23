---
title: Az Azure vendég operációs rendszer útmutató támogatási és kivezetési szabályzata |} A Microsoft Docs
description: Mi a Microsoft támogatást az alábbiak tekintetében az Azure vendég operációs rendszerre a Cloud Services által használt információkat biztosít.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: ce66d44c0ddb84ed8c2908d02b8062195d6b461d
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351014"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Az Azure vendég operációs rendszer támogatási és kivezetési szabályzat
Az Azure vendég operációs rendszer ezen a lapon található információkat konfigurációelemmel kapcsolatos ([vendég operációs rendszer](cloud-services-guestos-update-matrix.md)) Cloud Services feldolgozói és a webes szerepkör (PaaS). Nem alkalmazható a virtuális gépeken (IaaS).

A Microsoft rendelkezik egy közzétett [a vendég operációs rendszer vonatkozó támogatási szabályzatban](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Általában azért olvassák el a lapot most azt ismerteti, hogyan van megvalósítva a házirendet.

A házirend

1. A Microsoft támogatást **legalább a vendég operációs rendszer legújabb két családot**. Család kivonják a forgalomból, amikor a felhasználóknak kell frissíteni egy újabb támogatott vendég operációs rendszerek a hivatalos kivezetési dátuma 12 hónapig.
2. A Microsoft támogatást **legalább a támogatott vendég operációsrendszer-családok két legújabb verziója**.
3. A Microsoft támogatást **legalább az Azure SDK legújabb két verziója**. Amikor az SDK-verziója elavult, ügyfelek frissítése újabb verzióra a hivatalos kivezetési dátuma 12 hónapig fog rendelkezni.

Időnként több mint két családok vagy kiadásokban támogathatók. Hivatalos vendég operációs rendszer támogatási információk jelennek meg a [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Ha a vendég operációs rendszer verziója elavult
Új vendég operációs rendszer **verziók** kapcsolatos a legújabb frissítéseket MSRC havonta jelennek meg. A rendszeres havi frissítések miatt a vendég operációs rendszer verziója általában le van tiltva a kiadása után körülbelül 60 napig. Ez a tevékenység biztosítja, hogy legalább két Vendég operációsrendszer-verziók egyes családokon használható.

### <a name="process-during-a-guest-os-family-retirement"></a>A folyamat során a vendég operációs rendszer család használatból való kivonást egyaránt
A kivezetési bejelentéséig, ügyfelek után 12 hónapos "átmeneti" időszak előtt a régebbi termékcsalád hivatalosan eltávolítja a szolgáltatásból. A váltás ideje, a Microsoft saját belátása is kiterjeszthető. Frissítések közzéteszi a rendszer a a [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).

Fokozatos használatból való kivonást egyaránt folyamat hat (6) hónapos való átállási időszak lejárta megkezdődik. Ebben az időszakban:

1. A Microsoft értesíti ügyfeleit a használatból való kivonást egyaránt.
2. Az Azure SDK újabb verziója nem támogatja a kivont Vendég operációsrendszer-család.
3. Új telepítések és felhőalapú szolgáltatások redeployments nem engedélyezett a kivont termékcsalád

A Microsoft továbbra is új Vendég operációsrendszer-verzió a legújabb MSRC-frissítések utolsó napjáig az átállási időszak, más néven "lejárati dátum" beépítése vezetnek be. A lejárati dátum továbbra is fut a Cloud Services lesz támogatott alatt az Azure SLA. A Microsoft rendelkezik a saját belátása szerint értékeli frissítés kényszerítéséhez, törölje vagy állítsa le ezeket a szolgáltatásokat ezt követően.

### <a name="process-during-a-guest-os-version-retirement"></a>Folyamat során a vendég operációs rendszer verziója kivonása
Ügyfelek automatikus frissítése a vendég operációs rendszer beállítása, ha soha nem rendelkeznek aggódnia a Vendég operációsrendszer-verziók többé vesződnie a sérült. Majd mindig a legújabb vendég operációs rendszer használ.

Vendég operációsrendszer-verziók havonta jelennek meg. Rendszeres kiadások arányát, mert minden verzió rendelkezik egy rögzített időtartama van.

60 napon belül be a gyűjteményszintű, egy verzió: "*le van tiltva*". "Letiltva" azt jelenti, hogy a verzióra a portálról törlődik. A verzió már nem állítható be a CSCFG-konfigurációs fájlt. Meglévő üzemelő példányok futó van hátra. Azonban nem engedélyezett új üzembe helyezésekhez és telepítéseit kód és a konfiguráció frissítéseit.

Némi várakozás után váljon "Letiltva", "lejár" a vendég operációs rendszer verziója, és az összes telepítés továbbra is fut, hogy a lejárt verziót biztonsági és biztonsági problémák vannak kitéve. Általában a lejárati történik, és kötegekben, így a megfelelő időszak-lejárati eltérőek lehetnek.

Ügyfelek, akik a vendég operációs rendszer frissítése manuálisan, a szolgáltatások konfigurálásához biztosítania kell, hogy azok a szolgáltatások futnak-e a támogatott vendég operációs rendszeren. Ha a szolgáltatás automatikusan frissíti a vendég operációs rendszer van konfigurálva, az alapul szolgáló platform lesz a megfelelőség biztosítása, és frissíti a a legújabb vendég operációs rendszer.

Ezeket az időszakokat ügyfél átmenetek megkönnyítése érdekében a Microsoft saját belátása szerint értékeli hosszabb lehet elvégezni. Módosítások kommunikálja a a [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Kivonás során értesítések
* **Család használatból való kivonást egyaránt** <br>A Microsoft olvashat a blogbejegyzésekben és a portál értesítései használja. Ügyfelek, akik továbbra is használja a kivont Vendég operációsrendszer-család keresztül közvetlen kommunikáció (e-mailt, portál üzeneteket, telefonhívás) a hozzárendelt szolgáltatás-rendszergazdák értesítést kap. Minden módosítás lesz közzétéve, az [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).
* **Verzió kivonása** <br>Összes módosítás és a dátumok bekövetkezésük lesz közzétéve, az [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md), beleértve a kiadási le van tiltva és lejárati. Rendszergazdái fog kapni e-mailek, ha egy letiltott Vendég operációsrendszer-verzió vagy termékcsalád üzemelő példányok rendelkeznek. E-mailek időzítése eltérőek lehetnek. Ezek általában legalább előtt megfelelő, havonta bár ez időzítési nem hivatalos szolgáltatásszint-szerződést.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Hogyan csökkentheti a migrálás következményeinek?**

Azt javasoljuk, hogy a legújabb Vendég operációsrendszer-család a Cloud Services szolgáltatások tervezéséhez használja.

1. A migrálás korai egy újabb családba megtervezésére.
2. Állítsa be ideiglenes tesztkörnyezetek tesztelése a Cloud Services, az új család futtat.
3. A Vendég operációsrendszer-verzió beállítása **automatikus** (osVersion = * a a [.cscfg](cloud-services-model-and-package.md#cscfg) fájl), az áttelepítést az új Vendég operációsrendszer-verziók automatikusan történik.

**Mi történik, ha a webalkalmazásom az operációs rendszer mélyebb integrációjuk szükséges?**

Ha a webalkalmazás-architektúra attól függ, hogy az operációs rendszer alapjául szolgáló funkcióját, használjon támogatott platform képességei például [indítási feladatok](cloud-services-startup-tasks.md) vagy más bővítési mechanizmust. Másik lehetőségként használhatja [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktúra-szolgáltatás), hol az alapul szolgáló operációs rendszer karbantartásáért felelős.

## <a name="next-steps"></a>További lépések
Tekintse át a legújabb [vendég operációs rendszer kiadással](cloud-services-guestos-update-matrix.md).
