---
title: Delegált erőforrások figyelése nagy méretekben
description: Ismerje meg, hogyan használhatja hatékonyan az Azure Monitor naplók at skálázható módon az ügyfél-bérlők által felügyelt.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985201"
---
# <a name="monitor-delegated-resources-at-scale"></a>Delegált erőforrások figyelése nagy méretekben

Szolgáltatóként előfordulhat, hogy több ügyfél-bérlőt is beszervezett az Azure delegált erőforrás-kezeléséhez. [Az Azure Lighthouse](../overview.md) lehetővé teszi a szolgáltatók számára, hogy egyszerre több bérlőn keresztül hajtsanak végre műveleteket, hatékonyabbá téve a felügyeleti feladatokat.

Ez a témakör bemutatja, hogyan [használhatja](../../azure-monitor/platform/data-platform-logs.md) az Azure Monitor naplók skálázható módon az ügyfél-bérlők által felügyelt.

## <a name="create-log-analytics-workspaces"></a>Log Analytics-munkaterületek létrehozása

Az adatok gyűjtéséhez létre kell hoznia a Log Analytics-munkaterületeket. Ezek a Log Analytics-munkaterületek az Azure Monitor által gyűjtött adatok egyedi környezetei. Minden munkaterület saját adattárházzal és konfigurációval rendelkezik, és az adatforrások és megoldások úgy vannak konfigurálva, hogy az adataikat egy adott munkaterületen tárolják.

Javasoljuk, hogy ezeket a munkaterületeket közvetlenül az ügyfél-bérlőkben hozza létre. Így az adataik a bérlőikben maradnak, ahelyett, hogy az Önébe exportálnák őket. Ez lehetővé teszi a Log Analytics által támogatott erőforrások vagy szolgáltatások központosított figyelését is, így nagyobb rugalmasságot biztosít a figyelt adatok típusai tekintetében.

A Log Analytics-munkaterületet az [Azure Portalon,](../../azure-monitor/learn/quick-create-workspace.md)az [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)használatával vagy az [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)használatával hozhatja létre.

## <a name="deploy-policies-that-log-data"></a>Adatokat naplózó házirendek telepítése

Miután létrehozta a Log Analytics-munkaterületeket, üzembe helyezheti az [Azure-szabályzatot](../../governance/policy/index.yml) az ügyfélhierarchiákközött, hogy a diagnosztikai adatokat a rendszer minden bérlő megfelelő munkaterületére küldje. A pontos anamminda,hogy milyen erőforrástípusokat szeretne figyelni.

A házirendek létrehozásáról az [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítése című](../../governance/policy/tutorials/create-and-manage.md)témakörben olvashat bővebben. Ez [a közösségi eszköz](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) egy parancsfájlt biztosít, amely segít a kiválasztott erőforrástípusok figyelésére vonatkozó házirendek létrehozásában.

Miután megállapította, hogy mely szabályzatokat kell telepítenie, [a delegált előfizetések nagy méretekben telepítheti őket.](policy-at-scale.md)

## <a name="analyze-the-gathered-data"></a>Az összegyűjtött adatok elemzése

Miután telepítette a szabályzatokat, az adatok az egyes ügyfelek bérlői ben létrehozott Log Analytics-munkaterületeken kerülnek naplózásra. Az összes felügyelt ügyfél számára betekintést nyerhet, olyan eszközökkel, mint például [az Azure Monitor Munkafüzetek](../../azure-monitor/platform/workbooks-overview.md) segítségével több adatforrásból származó információk összegyűjtésére és elemzésére.

## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitorról.](../../azure-monitor/index.yml)
- További információ az [Azure Figyelő naplóiról.](../../azure-monitor/platform/data-platform-logs.md)
- További információ a [bérlők közötti felügyeleti élményekről.](../concepts/cross-tenant-management-experience.md)
