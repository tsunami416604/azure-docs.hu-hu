---
title: Tervminták indexe
description: Az Azure Blueprintsszel való üzembe helyezéshez való megfelelőségi és standard környezeti minták indexe
author: DCtheGeek
manager: carmonm
ms.service: blueprints
ms.topic: sample
ms.date: 08/20/2019
ms.author: dacoulte
ms.custom: fasttrack-edit
ms.openlocfilehash: 8a91ae0c576299e139cf036f1f20b90470f51349
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972304"
---
# <a name="azure-blueprints-samples"></a>Azure Blueprints-minták

A következő táblázat az Azure Blueprints szolgáltatáshoz használható mintákra mutató hivatkozásokat tartalmaz. Az összes minta éles alkalmazásnak megfelelő minőségű, és már ma üzembe helyezhetők a különböző megfelelőségi igények kielégítéséhez.

## <a name="standards-based-blueprint-samples"></a>Szabványalapú tervminták

|  |  |
|---------|---------|
| [IRS 1075](./irs-1075/index.md) | Védőkorlátokat biztosít az IRS 1075 szabványnak való megfelelőséghez.|
| [ISO 27001](./iso27001/index.md) | Védőkorlátokat biztosít az ISO 27001 szabványnak való megfelelőséghez. |
| [ISO 27001 – Megosztott szolgáltatások](./iso27001-shared/index.md) | Szabványoknak megfelelő infrastruktúra-minták és szabályzati védőkorlátok együttesét biztosítja, amelyek segítséget nyújtanak az ISO 27001-tanúsítvány megszerzéséhez. |
| [ISO 27001 App Service Environment/SQL Database-számításifeladat](./iso27001-ase-sql-workload/index.md) | Kiegészítő infrastruktúrát biztosít az [ISO 27001 Megosztott szolgáltatások](./iso27001-shared/index.md) tervmintához. |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Védőkorlátokat biztosít az NIST SP 800-53 R4 keretrendszernek való megfelelőséghez. |
| [PCI-DSS 3.2.1](./pci-dss-3.2.1/index.md) | Szabályzatkészletet biztosít PCI-DSS 3.2.1 szabványnak való megfelelőséghez. |
| [UK OFFICIAL és az Egyesült Királyság Nemzeti Egészségügyi Szolgálata – Szabályozás](./ukofficial/index.md) | Szabványoknak megfelelő infrastruktúra-minták és szabályzati védőkorlátok együttesét biztosítja, amelyek segítséget nyújtanak a UK OFFICIAL- és az Egyesült Királyság Nemzeti Egészségügyi Szolgálata által kiadott tanúsítvány megszerzéséhez. |
| [A felhőbevezetési keretrendszer alaprendszere](./caf-foundation/index.md) | Vezérlőket biztosít a felhőtulajdon az [Azure-hoz készült Microsoft Cloud bevezetési keretrendszerrel (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index) összhangban történő kezeléséhez. |
| [A felhőbevezetési keretrendszer migrálási célzónája](./caf-migrate-landing-zone/index.md) | Vezérlőket biztosít az első számítási feladat migrálásához és a felhőtulajdon az [Azure-hoz készült Microsoft Cloud bevezetési keretrendszerrel (CAF)](/azure/architecture/cloud-adoption/migrate/index) összhangban történő kezeléséhez. |

## <a name="samples-strategy"></a>Mintastratégia

![Tervminta-stratégia](../media/blueprint-samples-strategy.png)

A felhőbevezetési keretrendszer alaprendszere és a felhőbevezetési keretrendszer célzónájának terve azt feltételezi, hogy az ügyfél egy meglévő üres előfizetést készít elő a helyszíni objektumok vagy számítási feladatok Azure-ba történő migrálására.
(Az A és B régió a fenti ábrán).  

Lehetőség van a tervminták iterálására és az ügyfél által alkalmazott testreszabásokban történő mintakeresésre. Lehetőség van az olyan iparág-specifikus tervek proaktív kezelésére is, mint a pénzügyi szolgáltatások és az e-kereskedelem (a B régió felső része). Hasonlóképpen képzeljük el az olyan összetett architekturális szempontok terveinek létrehozását, mint a több előfizetés, a magas rendelkezésre állás, a régiók közötti erőforrások, valamint a vezérlőket meglévő előfizetésekben és erőforrásokban implementáló ügyfelek (C és D régió).

Léteznek tervminták azon ügyfélforgatókönyvek kezelésére, amelyekben a megfelelőségi követelmények magasak és az architekturális összetettség is jelentős (a fenti ábra E régiója). A fenti F régiót az ügyfelek és a partnerek tervminták használatával és azok egyedi igényekre történő testreszabásával fogják kezelni.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).