---
title: Tanácsadói adatok az Azure Resource Graph ban
description: Az Azure Resource Graph-ban az Advisor-adatok lekérdezéseinek lekérdezi
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502450"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Az Erőforrás-gráf kezelőjében (Azure Resource Graph) lévő tanácsadói adatok lekérdezése

Az Advisor-erőforrások most antól az [Azure Resource Graph szolgáltatásba](https://azure.microsoft.com/features/resource-graph/)vannak betáblázva. Ez lefekteti az alapot számos, az Advisor-ajánlások hoz a konkénti ügyfélforgatókönyvek. Kevés olyan forgatókönyv, amelyet korábban nem lehetett nagy méretekben megtenni, és most már elérhető az Erőforrás-grafikon használatával:
* Lehetővé teszi összetett lekérdezések elvégzését az Azure Portalösszes előfizetéséhez
* Ajánlások kategóriatípusok (például magas rendelkezésre állás, teljesítmény) és hatástípusok (magas, közepes, alacsony) szerint összesítve
* Egy adott ajánlástípusra vonatkozó összes ajánlás
* Érintett erőforrások száma ajánlási kategória szerint

![Tanácsadó az Azure erőforrás-gráftalkapcsolat-kezelőben](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tanácsadóerőforrás-típusok az Azure Graphban

Elérhető Tanácsadó erőforrástípusok az [Erőforrás-gráfban:](https://docs.microsoft.com/azure/governance/resource-graph/)Az Advisor-erőforrások alatt 3 erőforrástípus érhető el lekérdezéshez. Itt található azoknak az erőforrásoknak a listája, amelyek már elérhetők az Erőforrás-diagram lekérdezésére.
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/elnyomás

Ezek az erőforrástípusok egy AdvisorResources nevű új tábla alatt vannak felsorolva, amelyet az Azure Portal Erőforrásgráf-kezelőben is lekérdezhet.


## <a name="next-steps"></a>További lépések

További információ az Advisor-ajánlásokról:
* [Bevezetés az Azure Advisorba](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Tanácsadói költségajánlások](advisor-cost-recommendations.md)
* [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
* [Az Advisor Operational Excellence ajánlásai](advisor-operational-excellence-recommendations.md)
* [Tanácsadó PIHENÉS API](https://docs.microsoft.com/rest/api/advisor/)
