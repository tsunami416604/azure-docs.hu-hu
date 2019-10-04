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
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 32f67fb94b207735e77583a6db62f7c8703dd991
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202745"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Riasztás érvényesítése (EICAR-teszt fájl) Azure Security Center
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A riasztások a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. Rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is tartalmaz a támadások megoldásához.
További információkért tekintse meg a biztonsági [riasztások a Azure Security Center](security-center-alerts-overview.md) és a [biztonsági riasztások kezelése és válaszadás a Azure Security Center](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Riasztások érvényesítése

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Riasztás ellenőrzése Windows rendszerű virtuális gépen<a name="validate-windows"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:

1. Másoljon egy végrehajtható fájlt (például a **Calc. exe**fájlt) a számítógép asztalára vagy más kényelmi címtárba, és nevezze át **ASC_AlertTest_662jfi039N. exe**néven.
1. Nyissa meg a parancssort, és hajtsa végre a fájlt argumentummal (csak egy hamis argumentum neve), például:```ASC_AlertTest_662jfi039N.exe -foo```
1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Az alábbi [példához](#alert-validate) hasonló riasztást kell megjeleníteni:

> [!NOTE]
> A Windows rendszerhez készült teszt-riasztások megtekintésekor ellenőrizze, hogy az **argumentumok naplózása engedélyezve** van-e **igaz**. Ha **hamis**, akkor engedélyeznie kell a parancssori argumentumok naplózását. Az engedélyezéshez használja a következő parancssort:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Riasztás ellenőrzése Linux rendszerű virtuális gépen<a name="validate-linux"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:
1. Másolja a végrehajtható fájlt egy kényelmes helyre, és nevezze át a **./asc_alerttest_662jfi039n**névre, például:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Nyissa meg a parancssort, és hajtsa végre a következő fájlt:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Az alábbi [példához](#alert-validate) hasonló riasztást kell megjeleníteni:

### Riasztási példa<a name="alert-validate"></a>

![Példa riasztás érvényesítésére](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Lásd még
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
