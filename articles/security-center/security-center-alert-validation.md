---
title: Riasztás érvényesítése (EICAR-teszt fájl) a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum az Azure Security Center biztonsági riasztásainak érvényesítését ismerteti.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d3ea0adb4adf88e557ca360799836555cb90078e
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520939"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Riasztás érvényesítése (EICAR-teszt fájl) Azure Security Center
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A riasztások a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. Rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is tartalmaz a támadások megoldásához.
További információ: [biztonsági riasztások a Security Centerban](security-center-alerts-overview.md) , valamint a [biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Riasztások érvényesítése

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Riasztások ellenőrzése Windows rendszerű virtuális gépen<a name="validate-windows"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:

1. Másoljon egy végrehajtható fájlt (például a **Calc. exe**fájlt) a számítógép asztalára vagy más kényelmi címtárba, és nevezze át **ASC_AlertTest_662jfi039N. exe**néven.
1. Nyissa meg a parancssort, és hajtsa végre a fájlt argumentummal (csak egy hamis argumentum neve), például: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Az alábbi [példához](#alert-validate) hasonló riasztást kell megjeleníteni:

> [!NOTE]
> A Windows rendszerhez készült teszt-riasztások megtekintésekor ellenőrizze, hogy az **argumentumok naplózása engedélyezve** van-e **igaz**. Ha **hamis**, akkor engedélyeznie kell a parancssori argumentumok naplózását. Az engedélyezéshez használja a következő parancsot:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Riasztások ellenőrzése Linux rendszerű virtuális gépen<a name="validate-linux"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:
1. Másolja a végrehajtható fájlt egy kényelmes helyre, és nevezze át a **./asc_alerttest_662jfi039n**névre, például:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Nyissa meg a parancssort, és hajtsa végre a következő fájlt:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Az alábbi [példához](#alert-validate) hasonló riasztást kell megjeleníteni:

### Riasztási példa<a name="alert-validate"></a>

![Példa riasztás érvényesítésére](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Riasztások ellenőrzése a Kubernetes<a name="validate-kubernetes"></a>

Ha az Azure Kubernetes szolgáltatás integrálásának Security Center előzetes verzióját használja, futtassa a következő kubectl-parancsot a riasztások működésének ellenőrzéséhez:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Az Azure Kubernetes Service és a Azure Security Center integrálásával kapcsolatos további információkért tekintse meg [ezt a cikket](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>További lépések
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – Ismerje meg, hogyan kezelheti a riasztásokat, és hogyan reagálhat a Security Center biztonsági incidensekre.
* A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
* A [biztonsági riasztások megismerése Azure Security Centerban](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) – a különböző típusú biztonsági riasztások ismertetése.
* [Azure Security Center hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) – a Security Center gyakori problémáinak elhárítása.
* [Azure Security Center GYIK](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
