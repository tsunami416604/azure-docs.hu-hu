---
title: Frissítés a Security Center Standard csomagra váltásával fokozott biztonságot |} A Microsoft Docs
description: Ez a cikk az Azure Security Center díjszabási információkat nyújt.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: monhaber
ms.openlocfilehash: c246c6a96f56d36e1d42bb8c44f7c9a06949ecd5
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648951"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>A Security Center Standard csomagra váltásával fokozott biztonságot érhet
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Átláthatóvá és ellenőrizhetővé hibrid felhőbeli számítási feladatokhoz, amely csökkenti a fenyegetéseket, és intelligens észlelés segít lépést tartani, gyorsan fejlődő kibertámadások aktív védelmet kínál.

## <a name="pricing-tiers"></a>Árképzési szintek
A Security Center két szinten érhető el:

- A **ingyenes** réteg minden Azure-előfizetés automatikusan engedélyezve van, és biztosítja a biztonsági házirend, folyamatos biztonsági értékelés és végrehajtható biztonsági ajánlásokat az Azure-erőforrások védelmére.
- A **Standard** szint bővíti ki az ingyenes szinten a magán- és más nyilvános felhőkben futó munkaterhelések, így egyesített biztonságkezelést és fenyegetések elleni védelmet a hibrid felhőalapú számítási feladatokhoz. A Standard is tarifacsomag speciális fenyegetésészlelési képességeket tartalmaz, amely a beépített viselkedéselemzés és a gépi tanulás használatával azonosíthatja a támadásokat és a nulladik napi támadások, a hozzáférés- és alkalmazásvezérlőket használ a hálózati támadások és kártevők kitettség csökkentése érdekében, és További. Próbálja meg a Standard szintű szolgáltatás ingyenes. A Security Center támogatja az Azure erőforrások, többek között a virtuális gép, Virtuálisgép-méretezési csoportok, App Service, az SQL Server-kiszolgálók és a Storage-fiókok. Ha az Azure Security Center Standard választhatják a nem támogatott erőforrástípus alapján. 


További információkért lásd: a Security Center [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-30-days"></a>Próbálja ki 30 napig ingyenesen Standard
A Standard szint az első 30 napban ingyenesen kínáljuk. 30 nap végén kell kíván használni a szolgáltatást, automatikusan megkezdjük számlázni a használatot.

A Standard szintre, amely örökli az előfizetés összes erőforrásának frissítheti teljes Azure-előfizetéssel.

A standard szintű csomag lekéréséhez:

1. Válassza ki **biztonsági házirend** a a **a Security Center** főmenü.
2. A Standard kívánt előfizetés kiválasztásához.
3. Az a **biztonsági házirend** panelen válassza ki **tarifacsomag**.
4. Válassza ki **Standard** frissítése.
5. Kattintson a **Save** (Mentés) gombra.

![A Security Center díjszabása](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Ahhoz, hogy az összes Security Center-szolgáltatásokat, telepítenie kell a Standard díjcsomag az előfizetés vagy a alkalmazni virtual machines gépet tartalmazó erőforráscsoport. Csak a time VM access, az adaptív alkalmazásvezérlők és hálózati észlelések az Azure-erőforrások nem engedélyezi a munkaterülethez tartozó díjszabás konfigurálása.
>
>

## <a name="why-upgrade-to-standard"></a>Miért frissítsen a standard előfizetésre?
A Security Center fejlett biztonsági és a hibrid felhőbeli számítási, beleértve a veszélyforrások elleni védelmet kínálja:

- **A hibrid biztonsági** – biztonsági egységes képet kaphat az összes helyszíni és felhőbeli számítási feladatok. Biztonsági házirendek vonatkoznak, és folyamatosan értékelni a biztonsági szabványoknak való megfelelőség biztosításához a hibrid felhőbeli számítási feladatok biztonságát. Többféle forrásból, például tűzfalakból és más partneri megoldásból származó biztonsági adatokat gyűjthet, kereshet és elemezhet.
- **Speciális fenyegetésészlelés** – fejlett analitika és a Microsoft Intelligent Security Graph segítségével kivédheti kibertámadásokat –.  A beépített viselkedéselemzés és a gépi tanulás használatával azonosíthatja a támadásokat és a biztonsági rések nulladik napi kihasználását. Figyelheti a bejövő támadásokat és az incidens utáni tevékenységeket a hálózatokon, a gépeken és a felhőszolgáltatásokon. Interaktív eszközökkel és fenyegetésekkel kapcsolatos környezetalapú tudásbázissal egyszerűsítheti a vizsgálatot.
- **Hozzáférés- és alkalmazásvezérlőket** -blokkolja a kártevőket és más nemkívánatos alkalmazásokat engedélyezési javaslatok alkalmazásával adott számítási feladatokhoz igazított, és a gépi tanuláson alapuló. A hálózat támadási felület csökkentése – igény szerinti, szabályozott hozzáféréssel rendelkező felügyeleti portjaihoz, az Azure virtuális gépekhez, kevésbé legyen kitéve a találgatásos támadásoknak és más hálózati támadásoknak.


## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center díjszabását. A Standard csomag fokozott biztonságot és a komplex veszélyforrások elleni védelem kapcsolatos további információkért lásd:

- [Fejlett fenyegetésészlelés](security-center-threat-report.md)
- [Csak a virtuális gép hozzáférés-vezérlés idő](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
