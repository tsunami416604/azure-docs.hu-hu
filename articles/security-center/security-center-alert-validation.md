---
title: Riasztás (EICAR tesztfájlt) érvényesítése az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum az Azure Security Center biztonsági riasztásainak érvényesítését ismerteti.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626297"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Riasztások érvényesítése (EICAR tesztfájlt) az Azure Security Centerben
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
Riasztások az értesítéseket, amelyek a Security Center állít elő, amikor a rendszer a fenyegetéseket az erőforrások. Rangsorolja és a szükséges, hogy a probléma gyors vizsgálatára vonatkozó információk a riasztások listája. A Security Center is a támadás elhárításával hogyan vonatkozó javaslatokkal szolgál.
További információkért lásd: [az Azure Security Center biztonsági riasztásainak](security-center-alerts-overview.md) és [kezelése és válaszadás a biztonsági riasztások az Azure Security Centerben](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Riasztások érvényesítése

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Riasztási Windows virtuális gépen <a name="validate-windows"></a>

Után a Security Center az ügynök telepítve van a számítógépen kövesse az alábbi lépéseket a számítógépen kívánja a riasztás a jelentett megtámadott erőforrás:

1. Másoljon egy végrehajtható fájlt (például **calc.exe**) a számítógép asztalára vagy más könyvtárba, és nevezze át, **ASC_AlertTest_662jfi039N.exe**.
1. Nyissa meg a parancssort, és hajtsa végre a fájlt egy argumentummal (csak egy hamis argumentum nevét), például: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Hasonló riasztást a [példa](#alert-validate) alábbi üzenetnek kell megjelennie:

> [!NOTE]
> Ha a teszt a riasztás áttekintése Windows, ellenőrizze, hogy a mező **argumentumok naplózása beállítás** van **igaz**. Ha **hamis**, majd engedélyezze a parancssori argumentumok naplózását. Annak engedélyezéséhez használja a következő parancsot:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Riasztási Linux rendszerű virtuális gépen <a name="validate-linux"></a>

Után a Security Center az ügynök telepítve van a számítógépen kövesse az alábbi lépéseket a számítógépen kívánja a riasztás a jelentett megtámadott erőforrás:
1. Másoljon egy végrehajtható fájlt egy tetszés szerinti helyre, és nevezze át, hogy **. / asc_alerttest_662jfi039n**, például:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Nyissa meg a parancssort, és hajtsa végre ezt a fájlt:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Hasonló riasztást a [példa](#alert-validate) alábbi üzenetnek kell megjelennie:

### Riasztási példa <a name="alert-validate"></a>

![Riasztások érvényesítése példa](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Lásd még
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
