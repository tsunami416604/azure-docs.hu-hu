---
title: Gyors javítási szervizelés az Advisor ajánlásaihoz
description: Csoportos szervizelés végrehajtása a gyors javítással az Advisorban
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79502489"
---
# <a name="quick-fix-remediation-for-advisor"></a>Gyors javítási szervizelés az Advisorhoz
A **gyors javítás** a több erőforrásra vonatkozó javaslat gyorsabb és egyszerűbb megoldását teszi lehetővé. Lehetővé teszi az erőforrások tömeges szervizelését, és lehetővé teszi az előfizetések gyorsabb optimalizálását az erőforrások nagy mértékű szervizelésével.
A szolgáltatás csak bizonyos ajánlásokhoz érhető el, Azure Portalon keresztül.


## <a name="steps-to-use-quick-fix"></a>A gyors javítás használatának lépései

1. A **gyors javítás** címkével rendelkező javaslatok listájában kattintson a javaslatra.

   ![Advisor – gyors javítás](./media/quick-fix-1.png)
   
   *A rendszerkép árai csak példaként szolgálnak.*

2. A javaslat részletei lapon megtekintheti azokat az erőforrásokat, amelyekhez ezt a javaslatot elvégezte. Válassza ki az összes olyan erőforrást, amelyet a javaslathoz szeretne szervizelni.

   ![Advisor – gyors javítás](./media/quick-fix-2.png)
   
   *A rendszerkép árai csak példaként szolgálnak.*

3. Miután kiválasztotta az erőforrásokat, kattintson a **gyors javítás** gombra a tömeges szervizeléshez.

   > [!NOTE]
   > Előfordulhat, hogy a felsorolt erőforrások némelyike le van tiltva, mert nem rendelkezik a megfelelő engedélyekkel a módosításhoz.
   
   > [!NOTE]
   > Ha más következményekkel is jár, az Advisorban említett előnyök mellett a tapasztalatok alapján tájékoztatni fogja Önt a hasznos szervizelési döntések meghozataláról.
   
4. Értesítést fog kapni a szervizelés befejezéséről. A rendszer hibaüzenetet jelenít meg, ha vannak olyan erőforrások, amelyek nem szervizeltek, és az erőforrások a kijelölt módban vannak az erőforrás-lista nézetben.  


## <a name="next-steps"></a>További lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Bevezetés a Azure Advisorba](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
