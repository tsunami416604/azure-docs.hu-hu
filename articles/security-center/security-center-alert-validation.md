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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: ac76ce5d4fb788e6c2fb3dc9ec01c8d88bfb55cb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268070"
---
# <a name="alert-validation-in-azure-security-center"></a>Riasztások érvényesítése Azure Security Center
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A riasztások olyan értesítések, amelyeket a Security Center hoz létre, ha fenyegetést észlel egy erőforrásban. Rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is felkínál a támadás elhárítására.
További információ: [biztonsági riasztások a Security Centerban](security-center-alerts-overview.md) , valamint a [biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Riasztások érvényesítése

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Riasztások ellenőrzése Windows rendszerű virtuális gépeken <a name="validate-windows"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:

1. Másoljon egy végrehajtható fájlt (például **calc.exe**) a számítógép asztalára vagy más kényelmi címtárba, és nevezze át **ASC_AlertTest_662jfi039N.exeként **.
1. Nyissa meg a parancssort, és hajtsa végre a fájlt argumentummal (csak egy hamis argumentum neve), például: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Riasztásnak kell megjelennie.

> [!NOTE]
> A Windows rendszerhez készült teszt-riasztások megtekintésekor ellenőrizze, hogy az **argumentumok naplózása engedélyezve** van-e **igaz**. Ha **hamis**, akkor engedélyeznie kell a parancssori argumentumok naplózását. Az engedélyezéshez használja a következő parancsot:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Riasztások ellenőrzése Linux rendszerű virtuális gépeken <a name="validate-linux"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:
1. Másolja a végrehajtható fájlt egy kényelmes helyre, és nevezze át a (z **)./asc_alerttest_662jfi039nre**, például:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Nyissa meg a parancssort, és hajtsa végre a következő fájlt:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Riasztásnak kell megjelennie.


## <a name="validate-alerts-on-kubernetes"></a>Riasztások ellenőrzése a Kubernetes <a name="validate-kubernetes"></a>

Ha az Azure Kubernetes szolgáltatást a Security Centerrel integrálta, tesztelheti, hogy a riasztások a következő kubectl-paranccsal működnek-e:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

A Kubernetes-csomópontok és-fürtök védelmével kapcsolatos további információkért lásd: [Bevezetés az Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) használatába

## <a name="next-steps"></a>Következő lépések
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Azure Key Vault veszélyforrások észlelésének ellenőrzése Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti a riasztásokat, és hogyan reagálhat a Security Center biztonsági incidensekre.
* A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
* A [biztonsági riasztások megismerése Azure Security Centerban](security-center-alerts-type.md) – a különböző típusú biztonsági riasztások ismertetése.