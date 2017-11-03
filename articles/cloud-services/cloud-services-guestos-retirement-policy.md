---
title: "Támogatási lehetőségek és a használatból való kivonást házirend útmutató az Azure vendég operációs rendszer |} Microsoft Docs"
description: "Mi a Microsoft támogatást tekintetében az Azure vendég operációs rendszeren felhőalapú szolgáltatás által használt információkat tartalmaz."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: 4bc2d57cf4c7d6e0981aa1a5c7c989860600b897
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Az Azure vendég operációs rendszer támogatásának és a használatból való kivonást házirend
Ezen a lapon található információkat az Azure vendég operációs rendszerre vonatkozik ([vendég operációs rendszer](cloud-services-guestos-update-matrix.md)) Felhőszolgáltatások munkavégző és a webes szerepkörök (PaaS). Virtuális gépek (IaaS) nem vonatkozik.

A Microsoftnál egy közzétett [házirend támogatja a vendég operációs rendszer](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). A lap azért olvassák most ismerteti, hogyan van megvalósítva a házirendet.

A házirend

1. A Microsoft támogatást **legalább a vendég operációs rendszer legújabb két családok**. Család kivonják, az ügyfelek 12 hónapon hivatalos használatból való kivonást egy újabb támogatott vendég operációsrendszer-család frissíteni kell.
2. A Microsoft támogatást **legalább két legújabb verziójáról a támogatott vendég operációsrendszer-családok**.
3. A Microsoft támogatást **legalább az Azure SDK legújabb két verziója**. Az SDK verziójának kivonták a rendszerből, ha az ügyfelek a hivatalos kivezetési dátum újabb verzióra való frissítése a 12 hónapon keresztül fog rendelkezni.

Esetenként, több mint két családok vagy kiadásokban lehet, hogy támogatja. Hivatalos vendég operációs rendszer támogatási információk jelennek meg a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-family-or-version-is-retired"></a>Ha a Vendég operációsrendszer-család vagy a verziójával kivonták a rendszerből
Egy új vendég operációs rendszer **termékcsalád** megjelent a Windows Server operációs rendszer hivatalos új verzióinak megjelenése után egy kis ideig. Megjelent egy új Vendég operációsrendszer-család, amikor a Microsoft fogja vonni a legrégebbi Vendég operációsrendszer-család.

Új vendég operációs rendszer **verziók** kapcsolatos átfogó MSRC legújabb frissítéseit minden hónapban bevezetett. A rendszeres havi frissítéseket, mert a vendég operációs rendszer verziója általában le van tiltva a kiadása után körülbelül 60 nap. Ez a tevékenység tartja az egyes használható legalább két Vendég operációsrendszer-verziók.

### <a name="process-during-a-guest-os-family-retirement"></a>A folyamat során a vendég operációs rendszer termékcsalád kivonása
A használatból való kivonást elavulásának bejelentéséig, az ügyfelek után 12 hónap "átmenet" időszak előtt a régebbi termékcsalád hivatalosan eltávolítja a szolgáltatásból. A váltás ideje Microsoft belátása szerint is kiterjeszthető. A frissítések lesznek közzétéve a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).

A fokozatos használatból való kivonást folyamat megkezdődik az átállási időszak hónap elteltével hat (6). Ebben az időszakban:

1. A Microsoft ügyfelei a használatból való kivonást értesítést küld.
2. Az Azure SDK újabb verziója nem támogatja a kivont Vendég operációsrendszer-család.
3. Új központi telepítéséhez és a Felhőszolgáltatások redeployments nem engedélyezett a kivont termékcsalád

A Microsoft továbbra is bevezeti, az átállási időszak, a "lejárati dátum" néven ismert utolsó napjáig MSRC frissítéseket tartalmazó új vendég operációs rendszer verziója. A lejárati dátum még mindig fut, a Cloud Services lesz támogatott az Azure garantált szolgáltatási szintje alatt. A Microsoftnál frissítés kényszerítése, törölje vagy szolgáltatások leállítását követően tetszése szerint.

### <a name="process-during-a-guest-os-version-retirement"></a>A folyamat során a vendég operációs rendszer verziója kivonása
Ha az ügyfelek automatikus frissítése a vendég operációs rendszer, akkor soha nem kell foglalkoznia a Vendég operációsrendszer-verziók foglalkozik. Majd mindig a vendég operációs rendszer legújabb verzióját használ.

Vendég operációsrendszer-verziók havonta kiadott. Rendszeres kiadásokban arányát, mert egyes verzióihoz rögzített élettartamot rendelkezik.

Az élettartamot be 60 nap egy verziója "*le van tiltva*". "Letiltott" azt jelenti, hogy eltávolítja-e a verziót a portálról. A verzió már nem állítható be a szolgáltatáskonfigurációs SÉMA konfigurációs fájlból. Meglévő telepítések meghagyott futtatása. Azonban új központi telepítéséhez és a meglévő telepítések kód és a konfigurációs frissítésekről nem engedélyezett.

Némi várakozás után válik "Letiltva", a vendég operációs rendszer verziója "*lejár*", és minden még fut az adott telepítéshez kényszerített frissíteni, és automatikusan frissíteni a jövőben a vendég operációs rendszer beállítása. Lejárati kötegekben történik, a megfelelő és a lejárati idő időtartama eltérőek lehetnek.

Ezek az időszakok Microsoft megítélése megkönnyítése érdekében ügyfél átmenetek hosszabb tehető. A kommunikálja a módosításokat a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Értesítések során kivonása
* **Családbiztonsági kivonása** <br>Microsoft blogbejegyzések és a portál értesítései fogja használni. Egy kivont Vendég operációsrendszer-család továbbra is használó ügyfelek hozzárendelt szolgáltatás-rendszergazdák közvetlen kommunikációt (e-mail, portál üzenetek, telefonhívás) keresztül értesítést fog kapni. Minden változást lesznek közzétéve a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).
* **Verzió kivonása** <br>Összes módosítás és az előfordulásuk időpontjában naplózhatja őket dátumok lesznek közzétéve a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md), beleértve a kiadásban, le van tiltva és lejárati. Szolgáltatás-rendszergazdák e-maileket kap, ha egy letiltott vendég operációs rendszer verziója vagy a családot futó központi telepítések. Az e-maileket ütemezése változhatnak. Ezek általában legalább előtt megfelelő, egy hónap, ha a időzítési nem hivatalos szolgáltatásiszint-szerződésben garantált.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Hogyan csökkentheti az áttelepítés hatásai**

Azt javasoljuk, hogy használja-e legújabb Vendég operációsrendszer-család a Felhőszolgáltatások tervezéséhez.

1. Indítsa el az áttelepítést egy újabb termékcsalád korai tervezési.
2. A felhőalapú szolgáltatás, az új termékcsalád futó tesztelése beállítása ideiglenes próbatelepítést.
3. A vendég operációs rendszer verzió **automatikus** (osVersion = * a a [.cscfg](cloud-services-model-and-package.md#cscfg) fájl), az áttelepítés új Vendég operációsrendszer-verziók automatikusan megtörténik.

**Mi történik, ha a webalkalmazás számára szükséges az operációs rendszer szorosabb integrációt?**

Ha a webes alkalmazás felépítésére attól függ, hogy az operációs rendszer alapjául szolgáló funkciók, használjon támogatott platform képességei például [indítási feladatok](cloud-services-startup-tasks.md) vagy más bővítési mechanizmusokat. Másik megoldásként használhatja [Azure virtuális gépek](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktúra-szolgáltatás), ha az alapul szolgáló operációs rendszer felelős áll.

## <a name="next-steps"></a>Következő lépések
Tekintse át a legutóbbi [feloldja a vendég operációs rendszer](cloud-services-guestos-update-matrix.md).
