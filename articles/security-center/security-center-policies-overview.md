---
title: Az Azure Security Center beállítások |} A Microsoft Docs
description: Az Azure Security Center beállításainak konfigurálása.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666494"
---
# <a name="security-center-settings"></a>A Security Center beállításai
Ez a cikk áttekintést a Security Center beállításait.

A biztonsági házirend érhető el a következő beállításokat:

- **Az adatgyűjtés**: Meghatározza az ügynökkiépítés és [adatgyűjtés](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) beállításait.
- **Biztonsági házirend**: Azt határozza meg, amely szabályozza a Security Center figyeli, és javasolja. Szerkesztheti a [biztonsági házirend](tutorial-security-policy.md) a Security Centerben. Is [Azure Policy](tutorial-security-policy.md) létrehozhat új meghatározásokat, meghatározhat további szabályzatokat, és hozzárendelhet szabályzatokat a felügyeleti csoportok. 
- **E-mail-értesítések**: Meghatározza, hogy a biztonsági felelősök kapcsolati adatait, és [e-mailes értesítés](security-center-provide-security-contact-details.md) beállításait.
- **A tarifacsomag**: Határozza meg az ingyenes vagy standard [díjszabás kiválasztása](security-center-pricing.md). A kiválasztott csomag határozza meg, hogy a Security Center mely szolgáltatásai érhetők el a hatókörbe eső erőforrásokhoz. Megadhat egy előfizetések és munkaterületek szintet.

> [!NOTE]
> Mindegyik említett előfizetésenként állíthatja be. A munkaterületek beállíthatja a csak az adatok gyűjtése és a tarifacsomag.
>


## <a name="who-can-edit-security-policies"></a>Ki szerkeszthet biztonsági szabályzatok?
A Security Center szerepköralapú hozzáférés-vezérlés (RBAC), amely biztosít beépített szerepkörök, felhasználók, csoportok és Azure-szolgáltatások rendelhető használ. A Security Center megnyitásakor látnak csak ők is hozzáférhetnek az erőforrásokhoz kapcsolódó információkat. Ami azt jelenti, hogy a felhasználók vannak hozzárendelve a szerepköre *tulajdonosa*, *közreműködői*, vagy *olvasó* az előfizetéshez az erőforrás tartozik. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- **Biztonsági olvasó**: Jogosultságok megtekintése a Security Centernek, amely tartalmazza a javaslatok, riasztások, a házirend és egészségügyi, rendelkezik, de azok nem végezhet módosításokat.
- **Biztonsági rendszergazda**: Megegyező nézet jogokkal rendelkezik *biztonsági olvasó*, és azok is a biztonsági házirend módosítása és javaslatok és riasztások bezárása.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett az Azure Security Centerben a biztonsági szabályzatokat. Azure Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetést.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md): Megtudhatja, hogyan Security Center javaslatait az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center által nyújtott adatbiztonság](security-center-data-security.md): Útmutató a Security Center felügyeli, és gondoskodik az adatok.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): A legújabb Azure biztonsági hírek és információ beolvasása.
