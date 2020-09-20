---
title: A felhőbevezetési keretrendszer alaprendszerének tervmintája – Áttekintés
description: Az Azure Foundationhöz készült felhőbevezetési keretrendszer tervmintájának áttekintése és architektúrája.
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: cce5ea001b32ae92542f38f8f11427e85075cee9
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531695"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Az Azure-hoz készült Microsoft Cloud bevezetési keretrendszerhez tartozó tervminta áttekintése

Az Azure-hoz készült Microsoft Cloud bevezetési keretrendszer (CAF) alaprendszeréhez tartozó terv üzembe helyezi az első, termelésre kész Azure-alkalmazásához szükséges kulcsfontosságú infrastruktúra-erőforrásokat és szabályzatvezérlőket. Az alaprendszerhez tartozó terv a felhőbevezetési keretrendszerben található ajánlott mintán alapul.

## <a name="architecture"></a>Architektúra

A felhőbevezetési keretrendszer alaprendszeréhez tartozó tervminta olyan ajánlott infrastruktúra-erőforrásokat helyez üzembe az Azure-ban, amelyek segítségével a szervezetek bevezethetik a felhőtulajdon kezeléséhez szükséges alaprendszer-vezérlőket. Ez a minta olyan erőforrásokat, szabályzatokat és sablonokat helyez üzembe és kényszerít ki, amelyek lehetővé teszik a szervezetek számára, hogy magabiztosan vegyék használatba az Azure-t.

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="A felhőbevezetési keretrendszer alaprendszerének ábrája bemutatja, hogy a rendszer miket telepít a felhőbevezetési keretrendszer alaprendszerének létrehozásával kapcsolatos útmutatás részeként az Azure megismerése érdekében." border="false":::
   Bemutatja a felhőbevezetési keretrendszer alaprendszertervének üzembe helyezésével elérhető Azure-architektúrát.  Ez naplók tárolására szolgáló tárfiókokból és a tárfiókban való tároláshoz konfigurált Log Analyticsből álló erőforráscsoportokkal rendelkező előfizetésekre vonatkozik. Az Azure Security Center standard beállításával konfigurált Azure Key Vaultot is bemutatja. Ezek az alapvető infrastruktúrák az Azure Active Directoryval érhetők el, és az Azure Policyvel kényszeríthetők ki.     
:::image-end:::

Ez az implementálás több Azure-szolgáltatást foglal magába a biztonságos, teljes körűen felügyelt, vállalati használatra kész alaprendszer biztosítása érdekében. A környezet összetevői:

- Az [Azure Key Vault](../../../../key-vault/general/overview.md) egy példánya, amely a megosztott szolgáltatások környezetében üzembe helyezett virtuális gépek titkos kulcsainak tárolására szolgál
- A [Log Analytics](../../../../azure-monitor/overview.md) üzembe helyezése biztosítja, hogy az összes művelet és szolgáltatás naplózása egyetlen központi helyen történjen onnantól kezdve, hogy megkezdi a [tárfiókokba](../../../../storage/common/storage-introduction.md) való biztonságos üzembe helyezést diagnosztikai naplózás céljából
- Az [Azure Security Center](../../../../security-center/security-center-intro.md) üzembe helyezése (standard verzió) fenyegetésvédelmet nyújt a migrált számítási feladatok számára
- A terv emellett meghatároz és üzembe helyez [Azure-szabályzatokat](../../../policy/overview.md) a következőkhöz: 
  - Az erőforráscsoportok esetében alkalmazott címkézés (CostCenter)
  - Erőforrások hozzáfűzése a CostCenter címkével rendelkező erőforráscsoporthoz
  - Engedélyezett Azure-régió az erőforrások és az erőforráscsoportok esetében
  - Engedélyezett tárfiók-termékváltozatok (válassza ki az üzembe helyezés során)
  - Azure-beli virtuális gépek engedélyezett termékváltozatai (válassza ki az üzembe helyezés során)
  - A Network Watcher üzembe helyezésének megkövetelése 
  - Az Azure Storage-fiók biztonságos átvitele titkosításának megkövetelése
  - Erőforrástípusok tiltása (válassza ki az üzembe helyezés során)  
- Kezdeményezések
  - Monitorozás engedélyezése az Azure Security Centerben (több mint 100 szabályzatdefiníció)

A fentiek mindegyike teljesíti az [Azure Architecture Center referenciaarchitektúrákkal foglalkozó részében](/azure/architecture/reference-architectures/) közzétett, bevált módszerek követelményeit.

> [!NOTE]
> A felhőbevezetési keretrendszer alaprendszere adja a számítási feladatok alapvető architektúráját.
> Ezen alapvető architektúrán kívül továbbra is üzembe kell helyezni számítási feladatokat.

További információ: [Az Azure-hoz készült Microsoft Cloud bevezetési keretrendszere – Kész](/azure/cloud-adoption-framework/ready/)

## <a name="next-steps"></a>További lépések

Ezzel megismerte a felhőbevezetési keretrendszer alaprendszeréhez tartozó tervminta áttekintését és architektúráját.

> [!div class="nextstepaction"]
> [A felhőbevezetési keretrendszer alaprendszerének terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
