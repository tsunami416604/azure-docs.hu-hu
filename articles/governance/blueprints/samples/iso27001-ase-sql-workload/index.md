---
title: Minta – ISO 27001 ASE/SQL-számításifeladat terve – áttekintés
description: Az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintájának áttekintése és architektúrája.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b17b07124cf4491a6c58e9be2b10f958e29217ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783822"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintájának áttekintése

Az ISO 27001 Azure Blueprints App Service Environment/SQL Database-számításifeladat tervmintája kiegészítő infrastruktúrát biztosít az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervmintához.
E terv segítségével az ügyfelek olyan felhőalapú architektúrákat helyezhetnek üzembe, amelyek akkreditációs vagy megfelelőségi követelményekkel rendelkező alkalmazási helyzetekre kínálnak megoldást.

Két ISO 27001 tervminta áll rendelkezésre: ez a minta és az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervminta.

> [!IMPORTANT]
> A jelen minta az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervmintával üzembe helyezett infrastruktúrával van függőségi viszonyban. Először ezt a mintát kell üzembe helyezni.

## <a name="architecture"></a>Architektúra

Az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintája szolgáltatásalapú webes környezetként helyezi üzembe a platformot. Ez a környezet több, ISO 27001 szabványoknak megfelelő webalkalmazás, webes API és SQL Database-példány üzemeltetésére alkalmas. Ez a tervminta az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervmintával van függőségi viszonyban.

![Az ISO 27001 ASE/SQL-számításifeladat tervmintájának kialakítása](../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png)

Ez a környezet több Azure-szolgáltatásból épül fel, és ISO 27001 szabványokon alapuló, biztonságos, teljes körűen monitorozott, vállalati használatra kész számításifeladat-infrastruktúrát biztosít. A környezet összetevői:

- A DevOps nevű [szerepköralapú hozzáférés-vezérlési](../../../../role-based-access-control/overview.md) (RBAC) szerepkör erőforrások üzembe helyezéséhez és kezeléséhez rendelkezik jogosultsággal a tervmintával üzembe helyezett [Azure App Service Environment-környezetekben](../../../../app-service/environment/intro.md)
- [Azure-szabályzatok](../../../policy/overview.md), amelyekkel rögzíthető, hogy mely szolgáltatások helyezhetők üzembe a környezetben, illetve megtagadható a nyilvános IP-cím- (PIP-) erőforrások létrehozása
- Egyetlen alhálózatot tartalmazó virtuális hálózat, amely egy már meglévő [megosztott szolgáltatási](../iso27001-shared/index.md)környezethez van társítva, és arra kényszeríti a teljes adatforgalmat, hogy a [megosztott szolgáltatások](../iso27001-shared/index.md) tűzfalán haladjon át. A virtuális hálózaton a következő erőforrások találhatók:
  - Egy vagy több webalkalmazás, webes API vagy funkció üzemeltetésére használható [Azure App Service Environment-környezet](../../../../app-service/environment/intro.md)
  - Az [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) VNet-szolgáltatásvégpontot használó példánya, amely a számítási feladat környezetében futó alkalmazások titkos kulcsainak tárolására szolgál
  - Az [Azure SQL Database](../../../../sql-database/sql-database-technical-overview.md) VNet-szolgáltatásvégpontot használó kiszolgálópéldánya, amely a számítási feladat környezetében futó alkalmazásokhoz használt adatbázisok üzemeltetésére szolgál

## <a name="next-steps"></a>További lépések

Ezzel megismerte az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintájának áttekintését és architektúráját. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database-számításifeladat terve – Vezérlőelem-leképezés](./control-mapping.md)
> [ISO 27001 App Service Environment/SQL Database-számításifeladat terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.