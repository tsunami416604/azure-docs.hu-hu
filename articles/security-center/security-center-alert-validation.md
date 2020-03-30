---
title: Riasztásérvényesítés (EICAR tesztfájl) az Azure Security Centerben | Microsoft dokumentumok
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
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139997"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Riasztások érvényesítése (EICAR-tesztfájl) az Azure Security Centerben
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A riasztások olyan értesítések, amelyeket a Security Center hoz létre, ha fenyegetést észlel egy erőforrásban. Rangsorolja és felsorolja a riasztásokat a probléma gyors kivizsgálásához szükséges információkkal együtt. A Security Center javaslatokat is felkínál a támadás elhárítására.
További információt a [Biztonsági központ biztonsági riasztásai,](security-center-alerts-overview.md) valamint a biztonsági riasztások kezelése és [megválaszolása című témakörben talál.](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Riasztások érvényesítése

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Riasztások ellenőrzése Windows virtuális gépeken<a name="validate-windows"></a>

Miután a Security Center ügynök telepítve van a számítógépre, kövesse az alábbi lépéseket abból a számítógépből, amelyen a riasztás megtámadott erőforrását szeretné látni:

1. Egy végrehajtható fájlt (például **a calc.exe**fájlt) a számítógép asztalára vagy más, az Ön kényelmét szolgáló könyvtárba másolja, és nevezze át **ASC_AlertTest_662jfi039N.exe**.
1. Nyissa meg a parancssort, és hajtsa végre a fájlt egy argumentummal (csak egy hamis argumentum nevével), például:```ASC_AlertTest_662jfi039N.exe -foo```
1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Az alábbi [példához](#alert-validate) hasonló riasztást kell megjeleníteni:

> [!NOTE]
> A Windows tesztriasztásának ellenőrzésekén ellenőrizze, hogy az **Argumentumok naplózása engedélyezve** mező **értéke igaz-e.** Ha **hamis,** akkor engedélyeznie kell a parancssori argumentumok naplózását. Az engedélyezéshez használja a következő parancsot:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Riasztások ellenőrzése Linuxos virtuális gépeken<a name="validate-linux"></a>

Miután a Security Center ügynök telepítve van a számítógépre, kövesse az alábbi lépéseket abból a számítógépből, amelyen a riasztás megtámadott erőforrását szeretné látni:
1. A végrehajtható fájlokat egy kényelmes helyre másolhatja, és átnevezheti **./asc_alerttest_662jfi039n,** például:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Nyissa meg a parancssort, és hajtsa végre a fájlt:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Az alábbi [példához](#alert-validate) hasonló riasztást kell megjeleníteni:

### <a name="alert-example"></a>Példa riasztásra<a name="alert-validate"></a>

![Példa riasztás érvényesítése](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Riasztások ellenőrzése a Kubernetes-en<a name="validate-kubernetes"></a>

Ha az Azure Kubernetes-szolgáltatás integrálásának a Security Center előzetes verzióját használja, futtassa a következő kubectl parancsot a riasztások működésének teszteléséhez:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Az Azure Kubernetes szolgáltatás és az Azure Security Center integrációjáról ebben a cikkben olvashat [bővebben.](azure-kubernetes-service-integration.md)

## <a name="next-steps"></a>További lépések
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Az Azure Key Vault fenyegetésészlelésének érvényesítése az Azure Security Centerben](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – Ismerje meg, hogyan kezelheti a riasztásokat, és hogyan válaszolhat a biztonsági incidensekre a Security Centerben.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
* [A biztonsági riasztások ismertetése az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) – Ismerje meg a különböző típusú biztonsági riasztásokat.
