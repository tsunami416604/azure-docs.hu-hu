---
title: A felhőbevezetési keretrendszer migrálási célzónára vonatkozó tervének mintája – Áttekintés
description: Az Azure Migrate-hez készült felhőbevezetési keretrendszer migrálási célzónára vonatkozó tervmintájának áttekintése és architektúrája.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 6deb671070a742448560309b1b1a3572f5aa082d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852310"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Az Azure-beli migráláshoz készült Microsoft Cloud bevezetési keretrendszer migrálási célzónára vonatkozó tervmintájának áttekintése

Az Azure-hoz készült Microsoft Cloud bevezetési keretrendszer (CAF) migrálási célzónára vonatkozó terve olyan infrastruktúrákból áll, amelyek segítenek az első számítási feladat migrálásának beállításában és a felhőtulajdon a felhőbevezetési keretrendszerrel összhangban történő kezelésében.

A [felhőbevezetési keretrendszer alaprendszerének](../caf-foundation/index.md) tervmintája e minta kiegészítéseként szolgál.

## <a name="architecture"></a>Architektúra

A felhőbevezetési keretrendszer migrálási célzónára vonatkozó tervmintája alapszintű infrastruktúra-erőforrásokat helyez üzembe az Azure-ban, amelyek segítségével a szervezetek felkészíthetik az előfizetésüket a virtuális gépek migrálására. A tervminta emellett a felhőtulajdon kezeléséhez szükséges szabályozási vezérlők bevezetésében is segít. Ez a minta olyan erőforrásokat, szabályzatokat és sablonokat helyez üzembe és kényszerít ki, amelyek lehetővé teszik a szervezetek számára, hogy magabiztosan vegyék használatba az Azure-t.

:::image type="content" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="A felhőbevezetési keretrendszer migrálási célzónájának ábrája bemutatja, hogy a rendszer miket telepít a felhőbevezetési keretrendszer a kezdeti célzónával kapcsolatos útmutatása részeként" border="false":::

Ez a környezet több Azure-szolgáltatásból épül fel, és biztonságos, teljes körűen monitorozott, vállalati használatra kész szabályozást biztosít. A környezet összetevői:

- Az [Azure Key Vault](../../../../key-vault/general/overview.md) egy példánya, amely a megosztott szolgáltatások környezetében üzembe helyezett tanúsítványok, kulcsok és titkos kulcsok titkos kulcsainak tárolására szolgál
- A [Log Analytics](../../../../azure-monitor/overview.md) üzembe helyezése biztosítja, hogy a migrálás megkezdésétől fogva az összes művelet és szolgáltatás naplózása egyetlen központi helyen történjen
- Az [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) üzembe helyezése izolált hálózatot és alhálózatokat biztosít a virtuális gép számára.
- Az [Azure Migrate-projekt](../../../../migrate/migrate-services-overview.md) üzembe helyezésével felderítést és értékelést végezhet. Folyamatosan biztosítunk eszközöket a kiszolgálóértékelési és -migrálási, valamint az adatbázis-értékelési és -migrálási folyamatokhoz.  


A fentiek mindegyike teljesíti az [Azure Architecture Center referenciaarchitektúrákkal foglalkozó részében](/azure/architecture/reference-architectures/) közzétett, bevált módszerek követelményeit.

> [!NOTE]
> A felhőbevezetési keretrendszer migrálási terve meghatározza a számítási feladatok célzónáját. Ezen alapvető architektúrán felül továbbra is végre kell hajtania a virtuális gépek vagy adatbázisok értékelését és migrálását.

További információ: [Az Azure-hoz készült Microsoft Cloud bevezetési keretrendszere – Migrálás](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>További lépések

Ezzel megismerte a felhőbevezetési keretrendszer migrálási célzónára vonatkozó tervmintájának áttekintését és architektúráját.

> [!div class="nextstepaction"]
> [A felhőbevezetési keretrendszer migrálási célzónára vonatkozó terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
