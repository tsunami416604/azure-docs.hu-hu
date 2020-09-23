---
title: A Azure Security Center díjszabása
description: Azure Security Center két üzemmódban érhető el, az Azure Defender és anélkül.
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
ms.openlocfilehash: 6d2127af2c2c9e04141551dae72f0177f495b165
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904784"
---
# <a name="pricing-of-azure-security-center"></a>A Azure Security Center díjszabása
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít az Azure-ban, a helyszínen és egyéb felhőszolgáltatásokban futó számítási feladatokhoz. Lehetővé teszi a hibrid Felhőbeli számítási feladatok, az aktív védelem és a fenyegetések kialakulásának csökkentését, valamint az intelligens észlelést, amely megkönnyíti a gyors fejlődést okozó cyber-támadásokkal szembeni lépést.


## <a name="free-option-vs-azure-defender-enabled"></a>Ingyenes lehetőség és Azure Defender engedélyezve

Security Center két üzemmódban érhető el:

- **Azure DEFENDER off** (ingyenes) – az Azure defender nélküli Security Center ingyenes az összes Azure-előfizetésben, amikor első alkalommal látogatják meg az Azure Security Center irányítópultot a Azure Portal, vagy ha az API-n keresztül engedélyezte a programozott módon. Az ingyenes mód használatával biztonsági házirendet, folyamatos biztonsági értékelést és gyakorlati biztonsági ajánlásokat biztosít az Azure-erőforrások védelméhez.

- Az **Azure Defender** engedélyezése az Azure Defender számára lehetővé teszi az ingyenes mód képességeit a magán-és más nyilvános felhőkben futó számítási feladatokra, így egységes biztonsági felügyeletet és veszélyforrások elleni védelmet biztosít a hibrid Felhőbeli számítási feladatokban. Az Azure Defender főbb funkciói:

    - **Hibrid biztonság** – az összes helyszíni és Felhőbeli számítási feladatra kiterjedő egységes biztonsági nézet. Alkalmazza a biztonsági házirendeket, és folyamatosan mérje fel a hibrid Felhőbeli számítási feladatok biztonságát a biztonsági normáknak való megfelelés biztosítása érdekében. Több forrásból származó biztonsági adatok gyűjtése, keresése és elemzése, beleértve a tűzfalakat és más partneri megoldásokat.
    - **Veszélyforrások elleni védelem – riasztások** – a fejlett viselkedési elemzések és a Microsoft intelligens biztonsági gráf a fejlődő Cyber-támadásokat biztosítanak. A támadásokat és a napi használatot lehetővé téve a beépített viselkedés-elemzés és a gépi tanulás kihasználása. Figyelheti a hálózatokat, a gépeket és a Felhőbeli szolgáltatásokat a beérkező támadásokhoz és a szabálysértés utáni tevékenységekhez. Egyszerűsítse a nyomozást az interaktív eszközökkel és a kontextusbeli fenyegetések intelligenciával.
    - **Biztonsági rések keresése a virtuális gépek és a tárolók** beállításjegyzékében – egyszerűen üzembe helyezhet egy képolvasót az összes olyan virtuális gépen, amely az iparág legfejlettebb megoldást nyújt a biztonsági rések kezelésére. Megtekintheti, megvizsgálhatja és elháríthatja az eredményeket közvetlenül a Security Centeron belül. 
    - **Hozzáférés-és alkalmazás-vezérlőelemek** – a kártevők és más nemkívánatos alkalmazások letiltása a gépi tanulásra vonatkozó, a konkrét munkaterhelésekhez igazított, engedélyezési és letiltási listát létrehozó javaslatok alkalmazásával. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépeken található felügyeleti portokra vonatkozó, igény szerinti, vezérelt hozzáféréssel. Ez drasztikusan csökkenti a találgatásos és más hálózati támadásoknak való kitettséget.
    - **Tároló-biztonsági funkciók** – a biztonsági rések kezelése és a valós idejű veszélyforrások elleni védelem kihasználása a tároló környezetekben. Ha engedélyezi az **Azure Defender számára a tároló**-beállításjegyzékek használatát, akkor akár 12hrs is eltarthat, amíg az összes funkció be nem fejeződik. A díjak a csatlakoztatott beállításjegyzékbe leküldett egyedi tároló-lemezképek számától függenek. Ha egyszer már megvizsgálta a képet, a rendszer nem számítja fel újra, hacsak nem módosítják, és még egyszer leküldik. 

## <a name="try-azure-defender-free-for-30-days"></a>Próbálja ki az Azure Defendert ingyen 30 napig

Az Azure Defender az első 30 napon belül díjmentes. 30 nap elteltével a szolgáltatás használatának folytatásához automatikusan elindul a használati díj.

## <a name="enable-azure-defender"></a>Az Azure Defender engedélyezése

Az Azure Defender teljes Azure-előfizetését védelemmel láthatja el, és az előfizetésben lévő összes erőforrás örökli a védelmet.

Az Azure Defender engedélyezése:

1. A Security Center főmenüjében válassza a **díjszabás & beállítások**lehetőséget.
1. Válassza ki a frissíteni kívánt előfizetést.
1. A frissítéshez válassza az **Azure Defender** lehetőséget.
1. Kattintson a **Mentés** gombra.

Az alábbi példa egy előfizetésre vonatkozó díjszabási oldalt mutat be. Megfigyelheti, hogy az Azure Defender minden csomagja külön díjszabású, és egyénileg be-és kikapcsolható.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="A Security Center díjszabási oldala a portálon":::

> [!NOTE]
> Az összes Security Center funkció engedélyezéséhez, beleértve a veszélyforrások elleni védelmi képességeket is, engedélyeznie kell az Azure Defender szolgáltatást a megfelelő munkaterheléseket tartalmazó előfizetésben. A munkaterület szintjének engedélyezése nem teszi lehetővé az Azure-erőforrások igény szerinti elérését, az adaptív alkalmazások vezérlését és a hálózati észleléseket. 
>
> Az **Azure Defendert** engedélyezheti a Storage-fiókokhoz az előfizetés szintjén vagy az erőforrás szintjén.
> Az **Azure Defender for SQL** engedélyezhető az előfizetés szintjén vagy az erőforrás szintjén is.
> Az **Azure Database for MariaDB/MySQL/PostgreSQL** veszélyforrások elleni védelmét csak az erőforrás szintjén engedélyezheti.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben a Security Center díjszabását vezették be. Kapcsolódó anyagok esetében lásd:

- [Az Azure számítási feladatok költségeinek optimalizálása](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [A választott pénznem díjszabása és a régiójuk szerint](https://azure.microsoft.com/pricing/details/security-center/)
- Érdemes lehet felügyelni a költségeket, és korlátozni a megoldáshoz összegyűjtött adatok mennyiségét, ha korlátozza azt egy adott ügynökre. A [megoldás célcsoportja](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi, hogy hatókört alkalmazzon a megoldásra, és a munkaterületen lévő számítógépek egy részhalmazát célozza meg. Ha megoldás-célcsoportot használ, Security Center listázza a munkaterületet úgy, hogy ne legyen megoldás.