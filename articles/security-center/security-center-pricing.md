---
title: Az Azure Security Center-csomagok díjszabása
description: Az Azure Security Center két rétegben érhető el – ingyenes és normál. Ez az oldal bemutatja, hogyan lehet frissíteni a Szabad standard.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: fd84058c8421d144678c91fac3e5671511d0fd4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435504"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Frissítés standard szintre a fokozott biztonság érdekében
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Láthatóságot és vezérlést biztosít a hibrid felhőbeli számítási feladatok, a fenyegetéseknek való kitettséget csökkentő aktív védelem és az intelligens észlelés felett, amely segít lépést tartani a gyorsan változó kibertámadásokkal.

## <a name="pricing-tiers"></a>Árképzési szintek
A Security Center szolgáltatást két szinten biztosítjuk:

- Az **ingyenes** szint engedélyezve van az összes Azure-előfizetések, ha először látogat az Azure Security Center irányítópultján az Azure Portalon az első alkalommal, vagy ha engedélyezve van programozott API-n keresztül. Az ingyenes szint biztonsági szabályzatot, folyamatos biztonsági értékelést és végrehajtható biztonsági javaslatokat biztosít az Azure-erőforrások védelme érdekében.
- A **Standard** szint kiterjeszti az ingyenes szint képességeit a magán- és más nyilvános felhőkben futó számítási feladatokra, egységes biztonsági felügyeletet és fenyegetésvédelmet biztosítva a hibrid felhőbeli számítási feladatokban. A standard szint fenyegetésvédelmi képességekkel is rendelkezik, amelyek beépített viselkedéselemzéssel és gépi tanulással azonosítják a támadásokat és a nulladik napi biztonsági réseket, a hozzáférést és az alkalmazásvezérlőket a hálózati támadásoknak és rosszindulatú programoknak való kitettség csökkentése érdekében. Emellett a standard szintű biztonsági résa a virtuális gépek biztonsági résének leolvasását is növeli. Kipróbálhatja a standard szint ingyen. A Security Center szabvány támogatja az Azure-erőforrásokat, beleértve a virtuális gépeket, a virtuálisgép-méretezési csoportokat, az App Service-t, az SQL-kiszolgálókat és a Storage-fiókokat. Ha rendelkezik az Azure Security Center szabvány, leiratkozhat a támogatás erőforrás-típus alapján. 

A virtuális gépek ingyenes szintű biztonsági felméréseinek többsége, valamint számos szabványos szintű biztonsági riasztás a Log Analytics-ügynök képesség telepítését igényli. Engedélyezheti, hogy a Security Center automatikus kiépítése automatikusan telepítse az ügynököt az Azure-beli virtuális gépekhez.

## <a name="try-standard-tier-free-for-30-days"></a>Próbálja ki a standard szintű ingyenes 30 napig
A standard szint az első 30 napban ingyenes. 30 nap elteltével, ha úgy dönt, hogy folytatja a szolgáltatás használatát, automatikusan megkezdjük a használat díjának díját.

Frissítheti a teljes Azure-előfizetést a standard szintre, amelyet az előfizetésen belüli összes erőforrás öröklődik.

A standard szint beszerezése:

1. Válassza a **Biztonsági központ** főmenüjének **Árképzési & beállításai t.**
2. Válassza ki a szabványosra frissíteni kívánt előfizetést.
3. Válassza a **Tarifacsomag** lehetőséget,
4. Válassza a frissítéshez **a Szokásos** lehetőséget.
5. Kattintson a **Mentés** gombra.

[![A Biztonsági központ díjszabása](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> A Security Center összes szolgáltatásának engedélyezéséhez a szabványos tarifacsomagot kell alkalmaznia a megfelelő virtuális gépeket tartalmazó előfizetésre. A munkaterület díjszabásának konfigurálása nem teszi lehetővé a virtuális gépek hez való just-in-time hozzáférést, az adaptív alkalmazásvezérlőket és az Azure-erőforrások hálózati észleléseit.
>

## <a name="why-upgrade-to-standard"></a>Miért érdemes alapfelszereltségre frissíteni?
A Security Center fokozott biztonságot és veszélyforrások elleni védelmet nyújt a hibrid felhőbeli számítási feladatokhoz, többek között a következőket:

- **Hibrid biztonság** – Egységes biztonságszemléletet kaphat az összes helyszíni és felhőbeli számítási feladatban. A biztonsági szabályzatokat és a hibrid felhőbeli számítási feladatok biztonságát folyamatosan felmérheti a biztonsági szabványoknak való megfelelés érdekében. Több forrásból , például tűzfalakból és más partnermegoldásokból származó biztonsági adatok gyűjtése, keresése és elemzése.
- Biztonsági riasztások – Fejlett **elemzésekkel** és a Microsoft Intelligent Security Graph segítségével előnyt szerezhet a fejlődő kibertámadásokkal szemben. Használja ki a beépített viselkedéselemzéseket és a gépi tanulást a támadások és a nulladik napi támadások azonosításához. Figyelje a hálózatokat, gépeket és felhőszolgáltatásokat a bejövő támadások és a szabálysértést követő tevékenységek esetén. Egyszerűsítse a nyomozást interaktív eszközökkel és kontextuális fenyegetésekkel kapcsolatos intelligenciával.
- **Virtuális gépek biztonsági résének vizsgálata** – Egyszerűen telepíthet egy képolvasót az összes olyan virtuális gépre, amely az iparág legfejlettebb biztonsági réskezelési megoldását biztosítja. Megtekintheti, kivizsgálhatja és kiigazíthatja az eredményeket közvetlenül a Security Centeren belül. 
- **Hozzáférés és alkalmazásvezérlők** – Blokkolja a rosszindulatú programokat és más nem kívánt alkalmazásokat az adott számítási feladatokhoz igazított gépi tanulás alapú engedélyezési javaslatok alkalmazásával. Csökkentse a hálózati támadási felületet az Azure virtuális gépek felügyeleti portjaihoz való just-in-time, szabályozott hozzáféréssel. Ez drasztikusan csökkenti a nyers erőnek és más hálózati támadásoknak való kitettséget.
- **Tárolóbiztonsági funkciók** – A biztonsági rések kezelése és a valós idejű veszélyforrások elleni védelem kihasználása a tárolóba helyezett környezetekben. A tároló-beállításjegyzék-erőforrás engedélyezésekor akár 12 órás is igénybe vehet, amíg az összes szolgáltatás engedélyezve nem lesz.


## <a name="next-steps"></a>További lépések
Ebben a cikkben a Security Center díjszabása. Ha többet szeretne megtudni a standard szint fokozott biztonságáról és a speciális veszélyforrások elleni védelemről, olvassa el a következő témaköröket:

- [Veszélyforrások elleni védelem az Azure Security Centerben](threat-protection.md)
- [Just-in-time virtuális gép hozzáférés-vezérlés](security-center-just-in-time.md)
- [Tárolóbiztonság áttekintése](container-security.md)
- [Az árak rakoncáta az Ön által választott pénznemben és a régiónak megfelelően](https://azure.microsoft.com/pricing/details/security-center/)