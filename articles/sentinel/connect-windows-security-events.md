---
title: A Windows biztonsági eseményadatainak csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Windows biztonsági eseményadatait az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124962"
---
# <a name="connect-windows-security-events"></a>A Windows biztonsági eseményeinek csatlakoztatása 

A Biztonsági események összekötő lehetővé teszi, hogy a Windows-rendszerek (kiszolgálók és munkaállomások, fizikai és virtuális) összes biztonsági eseményét az Azure Sentinel-munkaterületre továbbítsa. Ez lehetővé teszi a Windows biztonsági eseményeinek megtekintését az irányítópultokon, az egyéni riasztások létrehozásához való használatukat, valamint a vizsgálatok javításához való támaszkodást, így több betekintést nyerhet a szervezet hálózatába, és bővítheti a biztonsági műveleteket. Képességek. A következő készletek közül választhat:<a name="event-sets"></a>

- **Minden esemény** – Minden Windows biztonsági és AppLocker-esemény.
- **Közös** – Az események szabványos készlete naplózási célokra. Ebben a készletben teljes felhasználói naplózási nyomvonal található. Például tartalmazza a felhasználói bejelentkezési és a felhasználói kijelentkezési eseményeket (4624-es és 4634-es eseményazonosítók). Vannak naplózási műveletek is, például a biztonsági csoport módosításai, a kulcstartományvezérlő Kerberos műveletei és más típusú események az elfogadott ajánlott eljárásokkal összhangban.

    A **közös** eseménykészlet tartalmazhat bizonyos típusú események, amelyek nem olyan gyakoriak.  Ennek az az oka, hogy a **közös** készlet fő célja az események mennyiségének kezelhetőbb szintre csökkentése, miközben továbbra is fenntartja a teljes naplózási nyomvonal-képességet.

- **Minimális** – Események kis halmaza, amelyek potenciális fenyegetésekre utalhatnak. Ez a készlet nem tartalmaz teljes naplózási nyomvonalat. Csak azokat az eseményeket tartalmazza, amelyek sikeres szabályszegésre utalhatnak, és más fontos eseményeket, amelyek előfordulási aránya nagyon alacsony. Például sikeres és sikertelen felhasználói bejelentkezéseket tartalmaz (4624-es és 4625-ös eseményazonosítók), de nem tartalmaz kijelentkezési információkat (4634), amelyek a naplózáshoz fontosak, de nem jelentenek értelmet a biztonsági rés észlelése esetén, és viszonylag nagy mennyiséggel rendelkeznek. A készlet adatkötetének nagy része bejelentkezési eseményekből és folyamatlétrehozási eseményekből (4688-as eseményazonosító) áll.

- **Nincs** – Nincs biztonsági vagy AppLocker-esemény. (Ezzel a beállítással letilthatja az összekötőt.)

    Az alábbi lista az egyes sorozatok biztonsági és alkalmazástároló-eseményazonosítóinak teljes bontását tartalmazza:

    | Eseménykészlet | Összegyűjtött eseményazonosítók |
    | --- | --- |
    | **Minimális** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Közös** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> A biztonsági események gyűjteménye egyetlen munkaterület környezetében konfigurálható az Azure Security Center vagy az Azure Sentinel, de nem mindkettő. Ha az Azure Sentinel talóiban olyan munkaterületen dolgozik, amely már fut az Azure Security Center, és biztonsági események gyűjtésére van beállítva, két lehetősége van:
> - Hagyja a biztonsági események gyűjtemény az Azure Security Center, ahogy van. Ezeket az eseményeket lekérdezheti és elemezheti az Azure Sentinelben, valamint az Azure Security Centerben. Azonban nem lesz képes figyelni az összekötő kapcsolati állapotát, vagy módosítani a konfigurációt az Azure Sentinelben. Ha ez fontos az Ön számára, fontolja meg a második lehetőséget.
>
> - [Tiltsa le](../security-center/security-center-enable-data-collection.md) a biztonsági események gyűjteményaz Azure Security Centerben, és csak ezután adja hozzá a biztonsági események összekötőt az Azure Sentinel. Az első lehetőséghez is képes lesz lekérdezni és elemezni az eseményeket az Azure Sentinelben és az Azure Security Centerben, de most már figyelheti az összekötő kapcsolati állapotát, vagy módosíthatja a konfigurációját – és csak is az Azure Sentinelben.

## <a name="set-up-the-windows-security-events-connector"></a>A Windows biztonsági események összekötőbeállítása

A Windows biztonsági eseményeinek összegyűjtése az Azure Sentinelben:

1. Az Azure Sentinel navigációs menüjében válassza az **Adatösszekötők**lehetőséget. Az összekötők listájában kattintson a **Biztonsági események**elemre, majd az **Összekötő megnyitása lapgombra** a jobb alsó sarokban. Ezután kövesse a képernyőn megjelenő utasításokat az **Utasítások** lap alatt, a szakasz többi részében leírtak szerint.

1. Ellenőrizze, hogy rendelkezik-e az **Előfeltételek**című csoportban leírt megfelelő engedélyekkel.

1. Töltse le és telepítse a [Log Analytics-ügynököt](../azure-monitor/platform/log-analytics-agent.md) (más néven a Microsoft Monitoring Agentet vagy MMA-t) azon gépeken, amelyek biztonsági eseményeit az Azure Sentinelbe szeretné streamelni.

    Azure virtuális gépek esetén:
    
    1. Kattintson az **Ügynök telepítése az Azure Windows virtuális gépen**, majd az alábbi hivatkozásra.
    1. Minden csatlakoztatni kívánt virtuális gépesetében kattintson a nevére a jobb oldalon megjelenő listában, majd kattintson a **Csatlakozás gombra.**

    Nem Azure-os Windows-gépek (fizikai, virtuális on-prem vagy virtuális egy másik felhőben):

    1. Kattintson az **Ügynök telepítése nem Azure Windows Machine rendszeren**, majd az alábbi hivatkozásra.
    1. Kattintson a megfelelő letöltési linkekre, amelyek a jobb oldalon, a Windows számítógépek csoportban jelennek **meg.**
    1. A letöltött végrehajtható fájl használatával telepítse az ügynököt az Ön által választott Windows rendszerekre, és konfigurálja azt a **munkaterület-azonosító és** a fent említett letöltési hivatkozások alatt megjelenő kulcsok használatával.

    > [!NOTE]
    >
    > Annak érdekében, hogy a szükséges internetkapcsolattal nem rendelkező Windows-rendszerek továbbra is továbbíthassák az eseményeket az Azure Sentinelbe, töltse le és telepítse az **OMS-átjárót** egy külön számítógépre a jobb alsó sarokban lévő hivatkozás használatával, hogy proxyként működjön.  Továbbra is telepítenie kell a Log Analytics-ügynököt minden olyan Windows rendszerre, amelynek eseményeit össze szeretné gyűjteni.
    >
    > Ebben a forgatókönyvben további információt a [ **Log Analytics-átjáró** dokumentációjában](../azure-monitor/platform/gateway.md)talál.

    További telepítési lehetőségeket és további részleteket a [ **Log Analytics-ügynök** dokumentációjában](../azure-monitor/platform/agent-windows.md)talál.

1. Válassza ki, hogy melyik eseménykészletet[(Összes, Gyakori vagy Minimális)](#event-sets)szeretné streamelni.

1. Kattintson a **Frissítés** parancsra.

1. Ha a megfelelő sémát szeretné használni a `SecurityEvent` Windows biztonsági eseményekhez szükséges Log Analytics szolgáltatásban, írja be a lekérdezési ablakot.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Körülbelül 20 percet is igénybe vehet, amíg a naplók kezdenek megjelenni a Log Analytics. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan kapcsolhatja össze a Windows biztonsági eseményeit az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- A [beépített](tutorial-detect-threats-built-in.md) vagy [egyéni](tutorial-detect-threats-custom.md) szabályok használatával első lépések észlelheti a fenyegetéseket az Azure Sentinel segítségével.

