---
title: Gyors javítási szervizelés az Advisor ajánlásaihoz
description: Csoportos szervizelés végrehajtása a gyors javítással az Advisorban
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: ebd993733c1aca9cd4f7d92f0a75e22d68a87ffe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518062"
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


## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Bevezetés a Azure Advisorba](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
