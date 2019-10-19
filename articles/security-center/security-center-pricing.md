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
ms.date: 03/17/2019
ms.author: memildin
ms.openlocfilehash: c5e457a6478fce070070ba7d0d610cbfe4f55024
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554676"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>A fokozott biztonság érdekében frissítsen Security Center Standard csomagra
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Lehetővé teszi a hibrid Felhőbeli számítási feladatok, az aktív védelem és a fenyegetések kialakulásának csökkentését, valamint az intelligens észlelést, amely megkönnyíti a gyors fejlődést okozó cyber-támadásokkal szembeni lépést.

## <a name="pricing-tiers"></a>Árképzési szintek
A Security Center szolgáltatást két szinten biztosítjuk:

- Az **ingyenes** csomag az összes Azure-előfizetéshez engedélyezve van, ha első alkalommal látogatja meg az Azure Security Center irányítópultot az Azure Portalon, vagy ha az API-n keresztül engedélyezte a progremmaticaly. Ha más előfizetéseket is aftewards, az új előfizetéshez alapértelmezés szerint nincs Security Center engedélyezve, de csak be kell indítania a Security Center portált, és az új előfizetés regisztrálása és ingyenes szintje lesz alkalmazva. Az ingyenes szinten biztonsági szabályzatot, folyamatos biztonsági értékelést és gyakorlati biztonsági ajánlásokat biztosít az Azure-erőforrások védelméhez.
- A **standard** szint kiterjeszti az ingyenes szint képességeit a magán-és más nyilvános felhőkben futó számítási feladatokra, így egységes biztonsági felügyeletet és veszélyforrások elleni védelmet biztosít a hibrid felhőalapú számítási feladatokhoz képest. A standard csomag emellett komplex veszélyforrás-észlelési képességeket is biztosít, amelyek beépített viselkedési elemzéseket és gépi tanulást alkalmaznak a támadások és a nulla napi biztonsági rések, hozzáférés-vezérlés és alkalmazás-vezérlőelemek azonosítására a hálózati támadásokkal és kártevő szoftverekkel szembeni kitettség csökkentése érdekében, valamint További. A standard szintű csomag ingyenesen kipróbálható. A Security Center Standard támogatja az Azure-erőforrásokat, beleértve a virtuális gépeket, a virtuálisgép-méretezési csoportokat, a App Service, az SQL-kiszolgálókat és a Storage Ha Azure Security Center standard szintű, az erőforrástípus alapján is letilthatja a támogatást. 

A virtuális gépek ingyenes szintű biztonsági vizsgálatainak többsége, valamint a standard szintű biztonsági riasztások nagy része megköveteli a Microsoft monitoring Agent (MMA) funkció telepítését. A Security Center automatikus kiépítését engedélyezheti az Azure-beli virtuális gépek ügynökének automatikus telepítéséhez.

További információkért tekintse meg a Security Center [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-30-days"></a>30 napig ingyenesen kipróbálható
A standard csomag az első 30 nap során ingyenesen elérhető. 30 nap elteltével a szolgáltatás használatának folytatását követően automatikusan megkezdjük a használati díjat.

A teljes Azure-előfizetést a standard szintre frissítheti, amelyet az előfizetésben lévő összes erőforrás örököl.

A standard szint beszerzése:

1. Válassza a **Security Center** főmenü **díjszabás & beállítások** elemét.
2. Válassza ki azt az előfizetést, amelyet a standard verzióra szeretne frissíteni.
3. Válassza ki az **árképzési szintet**.
4. A frissítéshez válassza a **standard** lehetőséget.
5. Kattintson a **Save** (Mentés) gombra.

(A rendszerkép árai csak példaként szolgálnak.)  ![Security központ díjszabása ](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> A Security Center összes szolgáltatásának engedélyezéséhez a Standard tarifacsomagot kell alkalmaznia a vonatkozó virtuális gépeket tartalmazó előfizetésen. A munkaterületek díjszabásának konfigurálása nem teszi lehetővé az igény szerinti virtuálisgép-hozzáférést, az adaptív alkalmazások vezérlését és a hálózati észleléseket az Azure-erőforrásokhoz.
>
>

## <a name="why-upgrade-to-standard"></a>Miért érdemes a standard verzióra váltani?
A Security Center a hibrid Felhőbeli számítási feladatokhoz nyújt fokozott biztonságot és veszélyforrások elleni védelmet, beleértve a következőket:

- **Hibrid biztonság** – az összes helyszíni és Felhőbeli számítási feladatra kiterjedő egységes biztonsági nézet. Alkalmazza a biztonsági házirendeket, és folyamatosan mérje fel a hibrid Felhőbeli számítási feladatok biztonságát a biztonsági normáknak való megfelelés biztosítása érdekében. Többféle forrásból, például tűzfalakból és más partneri megoldásból származó biztonsági adatokat gyűjthet, kereshet és elemezhet.
- Komplex **veszélyforrások észlelése** – a speciális elemzések és a Microsoft intelligens biztonsági gráf segítségével megkezdheti a fejlődő cyber-támadások előnyeit.  A beépített viselkedéselemzés és a gépi tanulás használatával azonosíthatja a támadásokat és a biztonsági rések nulladik napi kihasználását. Figyelheti a bejövő támadásokat és az incidens utáni tevékenységeket a hálózatokon, a gépeken és a felhőszolgáltatásokon. Interaktív eszközökkel és fenyegetésekkel kapcsolatos környezetalapú tudásbázissal egyszerűsítheti a vizsgálatot.
- **Hozzáférés és alkalmazás-vezérlőelemek** – a kártevők és más nemkívánatos alkalmazások letiltása az adott számítási feladatokhoz igazított engedélyezési Javaslatok alkalmazásával és a gépi tanulással. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépeken futó felügyeleti portok igény szerinti, felügyelt hozzáférésével, drasztikusan csökkentve a találgatásos és más hálózati támadásokkal szembeni kitettséget.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben a Security Center díjszabását vezették be. Ha többet szeretne megtudni a standard szintű biztonsági és komplex veszélyforrások elleni védelemről, tekintse meg a következőt:

- [Fejlett fenyegetésészlelés](security-center-threat-report.md)
- [Igény szerinti virtuális gépek hozzáférés-vezérlése](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
