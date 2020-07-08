---
title: Azure Security Center szintek díjszabása
description: A Azure Security Center két szinten érhető el – ingyenes és standard. Ez az oldal bemutatja, hogyan frissíthet az Ingyenesről a standard szintre.
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
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 4487d1452611b269eec756cdbc76e8e55c466cd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801138"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Frissítés a standard szintre a fokozott biztonság érdekében

Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Lehetővé teszi a hibrid Felhőbeli számítási feladatok, az aktív védelem és a fenyegetések kialakulásának csökkentését, valamint az intelligens észlelést, amely megkönnyíti a gyors fejlődést okozó cyber-támadásokkal szembeni lépést.

## <a name="pricing-tiers"></a>Árképzési szintek
A Security Center szolgáltatást két szinten biztosítjuk:

- Az **ingyenes** csomag az összes Azure-előfizetéshez engedélyezve van, amikor első alkalommal látogatja meg a Azure Portal Azure Security Center irányítópultot, vagy ha az API használatával programozott módon engedélyezte. Az ingyenes szinten biztonsági szabályzatot, folyamatos biztonsági értékelést és gyakorlati biztonsági ajánlásokat biztosít az Azure-erőforrások védelméhez.

- A **standard** szint kiterjeszti az ingyenes szint képességeit a magán-és más nyilvános felhőkben futó számítási feladatokra, így egységes biztonsági felügyeletet és veszélyforrások elleni védelmet biztosít a hibrid felhőalapú számítási feladatokhoz képest. A standard csomag emellett veszélyforrások elleni védelmi képességeket is biztosít, amelyek beépített viselkedési elemzéseket és gépi tanulást alkalmaznak a támadások és a nulla napi biztonsági rések, hozzáférés-és alkalmazás-vezérlőelemek azonosítására a hálózati támadásokkal és kártevő szoftverekkel szembeni kitettség csökkentése érdekében. Emellett a standard szint a biztonsági rések vizsgálatát is lehetővé teszi a virtuális gépeken. A standard szintű csomag ingyenesen kipróbálható. A Security Center Standard támogatja az Azure-erőforrásokat, beleértve a virtuális gépeket, a virtuálisgép-méretezési csoportokat, a App Service, az SQL-kiszolgálókat és a Storage Ha Azure Security Center standard szintű, az erőforrástípus alapján is letilthatja a támogatást. 

A virtuális gépek ingyenes szintű biztonsági értékelésének nagy része, valamint a standard szintű biztonsági riasztások többsége megköveteli a Log Analytics ügynök telepítését. Az automatikus kiépítés engedélyezése Security Center az ügynök automatikus üzembe helyezése az Azure-beli virtuális gépeken.

## <a name="try-standard-tier-free-for-30-days"></a>A standard szintű csomag 30 napig ingyenesen kipróbálható
A standard szint az első 30 napra ingyenes. 30 nap elteltével a szolgáltatás használatának folytatását követően automatikusan megkezdjük a használati díjat.

A teljes Azure-előfizetést a standard szintre frissítheti, amelyet az előfizetésben lévő összes erőforrás örököl.

A standard szint beszerzése:

1. Válassza a **Security Center** főmenü **díjszabás & beállítások** elemét.
2. Válassza ki azt az előfizetést, amelyet a standard verzióra szeretne frissíteni.
3. Válassza a **Tarifacsomag** lehetőséget,
4. A frissítéshez válassza a **standard** lehetőséget.
5. Kattintson a **Save** (Mentés) gombra.

[![Díjszabás Security Center](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Az összes Security Center funkció engedélyezéséhez, beleértve a veszélyforrások elleni védelmi képességeket is, a standard díjszabási szintet a megfelelő munkaterheléseket tartalmazó előfizetésre kell alkalmaznia. A munkaterületek díjszabásának konfigurálása nem teszi lehetővé az igény szerinti virtuálisgép-hozzáférést, az adaptív alkalmazások vezérlését és a hálózati észleléseket az Azure-erőforrásokhoz. 
>
> Az **Azure Storage-fiókok** veszélyforrások elleni védelmét az előfizetés szintjén vagy az erőforrás szintjén engedélyezheti.
> Engedélyezheti a veszélyforrások elleni védelmet **Azure SQL Database SQL-kiszolgálókon** az előfizetés szintjén vagy az erőforrás szintjén.
> Az **Azure Database for MariaDB/MySQL/PostgreSQL** veszélyforrások elleni védelmét csak az erőforrás szintjén engedélyezheti.


## <a name="why-upgrade-to-standard"></a>Miért érdemes a standard verzióra váltani?
A Security Center a hibrid Felhőbeli számítási feladatokhoz nyújt fokozott biztonságot és veszélyforrások elleni védelmet, beleértve a következőket:

- **Hibrid biztonság** – az összes helyszíni és Felhőbeli számítási feladatra kiterjedő egységes biztonsági nézet. Alkalmazza a biztonsági házirendeket, és folyamatosan mérje fel a hibrid Felhőbeli számítási feladatok biztonságát a biztonsági normáknak való megfelelés biztosítása érdekében. Több forrásból származó biztonsági adatok gyűjtése, keresése és elemzése, beleértve a tűzfalakat és más partneri megoldásokat.
- **Biztonsági riasztások** – speciális elemzések és a Microsoft intelligens biztonsági gráf használata a fejlődő cyber-támadásokkal szemben. A támadásokat és a napi használatot lehetővé téve a beépített viselkedés-elemzés és a gépi tanulás kihasználása. Figyelheti a hálózatokat, a gépeket és a Felhőbeli szolgáltatásokat a beérkező támadásokhoz és a szabálysértés utáni tevékenységekhez. Egyszerűsítse a nyomozást az interaktív eszközökkel és a kontextusbeli fenyegetések intelligenciával.
- A **virtuális gépek sebezhetőségének vizsgálata** – egyszerűen üzembe helyezhet egy szkennert az összes virtuális gépen, amely az iparág legfejlettebb megoldást nyújt a biztonsági rések kezelésére. Megtekintheti, megvizsgálhatja és elháríthatja az eredményeket közvetlenül a Security Centeron belül. 
- **Hozzáférés és alkalmazás-vezérlőelemek** – a kártevők és más nemkívánatos alkalmazások letiltása a gépi tanuláshoz kapcsolódó, az adott számítási feladatokhoz igazított engedélyezési Javaslatok alkalmazásával. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépeken található felügyeleti portokra vonatkozó, igény szerinti, vezérelt hozzáféréssel. Ez drasztikusan csökkenti a találgatásos és más hálózati támadásoknak való kitettséget.
- **Tároló-biztonsági funkciók** – a biztonsági rések kezelése és a valós idejű veszélyforrások elleni védelem kihasználása a tároló környezetekben. A tároló-beállításjegyzékek erőforrásának engedélyezésekor akár 12hrs is eltarthat, amíg az összes funkció engedélyezve nem lesz. A díjak a csatlakoztatott beállításjegyzékbe leküldett egyedi tároló-lemezképek számától függenek. Ha egyszer már megvizsgálta a képet, a rendszer nem számítja fel újra, hacsak nem módosítják, és még egyszer leküldik. 




## <a name="next-steps"></a>További lépések
Ebben a cikkben a Security Center díjszabását vezették be. Ha többet szeretne megtudni a standard szintű biztonsági és komplex veszélyforrások elleni védelemről, tekintse meg a következőt:

- [Fenyegetésvédelem az Azure Security Centerben](threat-protection.md)
- [Igény szerinti virtuális gépek hozzáférés-vezérlése](security-center-just-in-time.md)
- [Tárolóbiztonság áttekintése](container-security.md)
- [A választott pénznem díjszabása és a régiójuk szerint](https://azure.microsoft.com/pricing/details/security-center/)