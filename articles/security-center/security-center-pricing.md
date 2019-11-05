---
title: A fokozott biztonság érdekében frissítsen Security Center Standard szintjére | Microsoft Docs
description: Ez a cikk a Azure Security Center díjszabásával kapcsolatos információkat tartalmaz.
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 43ae34b946fada4d27c6843dbb8c3f9511aefd81
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520777"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>A fokozott biztonság érdekében frissítsen Security Center Standard csomagra
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Lehetővé teszi a hibrid Felhőbeli számítási feladatok, az aktív védelem és a fenyegetések kialakulásának csökkentését, valamint az intelligens észlelést, amely megkönnyíti a gyors fejlődést okozó cyber-támadásokkal szembeni lépést.

## <a name="pricing-tiers"></a>Árképzési szintek
A Security Center szolgáltatást két szinten biztosítjuk:

- Az **ingyenes** csomag az összes Azure-előfizetéshez engedélyezve van, ha a Azure Security Center-irányítópultot első alkalommal látogatja meg a Azure Portal, vagy ha az API használatával programozott módon engedélyezte. Az ingyenes szinten biztonsági szabályzatot, folyamatos biztonsági értékelést és gyakorlati biztonsági ajánlásokat biztosít az Azure-erőforrások védelméhez.
- A **standard** szint kiterjeszti az ingyenes szint képességeit a magán-és más nyilvános felhőkben futó számítási feladatokra, így egységes biztonsági felügyeletet és veszélyforrások elleni védelmet biztosít a hibrid felhőalapú számítási feladatokhoz képest. A standard csomag emellett komplex veszélyforrás-észlelési képességeket is biztosít, amelyek beépített viselkedési elemzéseket és gépi tanulást alkalmaznak a támadások és a nulla napi biztonsági rések, hozzáférés-vezérlés és alkalmazás-vezérlőelemek azonosítására a hálózati támadásokkal és kártevő szoftverekkel szembeni kitettség csökkentése érdekében. További. A standard szintű csomag ingyenesen kipróbálható. A Security Center Standard támogatja az Azure-erőforrásokat, beleértve a virtuális gépeket, a virtuálisgép-méretezési csoportokat, a App Service, az SQL-kiszolgálókat és a Storage Ha Azure Security Center standard szintű, az erőforrástípus alapján is letilthatja a támogatást. 

A virtuális gépek ingyenes szintű biztonsági vizsgálatainak többsége, valamint a standard szintű biztonsági riasztások nagy része megköveteli a Microsoft monitoring Agent (MMA) funkció telepítését. A Security Center automatikus kiépítését engedélyezheti az Azure-beli virtuális gépek ügynökének automatikus telepítéséhez.

## <a name="try-standard-free-for-30-days"></a>30 napig ingyenesen kipróbálható
A standard szint 30 napig ingyenesen használható. 30 nap elteltével a szolgáltatás használatának folytatását követően automatikusan megkezdjük a használati díjat.

A teljes Azure-előfizetést a standard szintre frissítheti, amelyet az előfizetésben lévő összes erőforrás örököl.

A standard szint beszerzése:

1. Válassza a **Security Center** főmenü **díjszabás & beállítások** elemét.
2. Válassza ki azt az előfizetést, amelyet a standard verzióra szeretne frissíteni.
3. Válassza a **Tarifacsomag** lehetőséget,
4. A frissítéshez válassza a **standard** lehetőséget.
5. Kattintson a **Save** (Mentés) gombra.

(A rendszerkép árai csak szemléltető célokra szolgálnak) [![Security Center díjszabása](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> A Security Center összes szolgáltatásának engedélyezéséhez a Standard tarifacsomagot kell alkalmaznia a vonatkozó virtuális gépeket tartalmazó előfizetésen. A munkaterületek díjszabásának konfigurálása nem teszi lehetővé az igény szerinti virtuálisgép-hozzáférést, az adaptív alkalmazások vezérlését és a hálózati észleléseket az Azure-erőforrásokhoz.
>

## <a name="why-upgrade-to-standard"></a>Miért érdemes a standard verzióra váltani?
A Security Center a hibrid Felhőbeli számítási feladatokhoz nyújt fokozott biztonságot és veszélyforrások elleni védelmet, beleértve a következőket:

- **Hibrid biztonság** – az összes helyszíni és Felhőbeli számítási feladatra kiterjedő egységes biztonsági nézet. Alkalmazza a biztonsági házirendeket, és folyamatosan mérje fel a hibrid Felhőbeli számítási feladatok biztonságát a biztonsági normáknak való megfelelés biztosítása érdekében. Több forrásból származó biztonsági adatok gyűjtése, keresése és elemzése, beleértve a tűzfalakat és más partneri megoldásokat.
- Komplex **veszélyforrások észlelése** – a speciális elemzések és a Microsoft intelligens biztonsági gráf segítségével megkezdheti a fejlődő cyber-támadások előnyeit.  A beépített viselkedéselemzés és a gépi tanulás használatával azonosíthatja a támadásokat és a biztonsági rések nulladik napi kihasználását. Figyelheti a bejövő támadásokat és az incidens utáni tevékenységeket a hálózatokon, a gépeken és a felhőszolgáltatásokon. Interaktív eszközökkel és fenyegetésekkel kapcsolatos környezetalapú tudásbázissal egyszerűsítheti a vizsgálatot.
- **Hozzáférés és alkalmazás-vezérlőelemek** – a kártevők és más nemkívánatos alkalmazások letiltása a gépi tanuláshoz kapcsolódó, az adott számítási feladatokhoz igazított engedélyezési Javaslatok alkalmazásával. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépeken található felügyeleti portokra vonatkozó, igény szerinti, vezérelt hozzáféréssel. Ez drasztikusan csökkenti a találgatásos és más hálózati támadásoknak való kitettséget.
- **Tárolók biztonsági funkciói** – a biztonsági rések kezelése és a valós idejű fenyegetések észlelése a tároló környezetekben. A tároló-beállításjegyzékek erőforrásának engedélyezésekor akár 12hrs is eltarthat, amíg az összes funkció engedélyezve nem lesz.


## <a name="next-steps"></a>További lépések
Ebben a cikkben a Security Center díjszabását vezették be. Ha többet szeretne megtudni a standard szintű biztonsági és komplex veszélyforrások elleni védelemről, tekintse meg a következőt:

- [Fejlett fenyegetésészlelés](security-center-threat-report.md)
- [Igény szerinti virtuális gépek hozzáférés-vezérlése](security-center-just-in-time.md)
- [Tárolók biztonsági áttekintése](container-security.md)
- [A választott pénznem díjszabása és a régiójuk szerint](https://azure.microsoft.com/pricing/details/security-center/)