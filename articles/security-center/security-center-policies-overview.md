---
title: "Azure Security Center biztonsági szabályzatainak bemutatása |} Microsoft Docs"
description: "Tudnivalók az Azure Security Center biztonsági szabályzatainak, és annak főbb funkcióit."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 95ef2099cb16bcfd550ce2799428f1a16031f535
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="security-policies-overview"></a>Biztonsági házirendek – áttekintés
Ez a dokumentum a Security Center biztonsági házirendek áttekintést nyújt.

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. A Security Centerben a számítási feladatok típusának vagy az adatok bizalmasságának megfelelően határozhatja meg az Azure-előfizetésekre vonatkozó szabályzatokat. Például alkalmazások, amelyek használnak szabályozott adatokat például személyes azonosításra alkalmas adatok előfordulhat, hogy magasabb szintű biztonságot, mint a többi munkaterhelését. 

A Security Center-szabályzatok a következő összetevőkből állnak:

- Adatgyűjtés: ügynök kiépítés és [adatgyűjtés](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) beállításait.
- Biztonsági házirend: meghatározza, hogy mely vezérlők folyamatosan figyelemmel kísért és a Security Center által javasolt (szerkesztése a [biztonsági házirend](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) a Security Center vagy [Azure házirend](security-center-azure-policy.md), az új korlátozott előzetes definíciók, további házirendeket és a házirendek rendelhetők a felügyeleti csoportok között).
- E-mail értesítések: biztonsági névjegyeket és [értesítési e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) beállításait.
- IP-címek: szabad vagy standard [kijelölés árképzési](https://docs.microsoft.com/azure/security-center/security-center-pricing), amely meghatározza, hogy mely biztonsági központ funkciók érhetők el az erőforrások (előfizetések, erőforráscsoport-sablonok és munkaterületek adható meg) hatókörében. 


## <a name="who-can-edit-security-policies"></a>Ki is szerkesztheti a biztonsági szabályzatokat?
A Security Center használja a szerepköralapú hozzáférés-vezérlés (RBAC), amely beépített szerepkörök hozzárendelheti a felhasználók, csoportok és az Azure biztosít. A Security Center megnyitásakor a felhasználók csak azon erőforrások információit látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználó az előfizetés vagy az erőforrást tartalmazó erőforráscsoport tulajdonosi, közreműködői vagy olvasói szerepköréhez van hozzárendelve. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- Biztonsági olvasó: ehhez a szerepkörhöz tartozó felhasználói jogok Security Center, beleértve a javaslatok, a riasztások, a házirendhez és a rendszerállapot, megtekintheti, de nem fogja tudni módosítani.
- Biztonsági rendszergazda: ugyanaz, mint biztonsági olvasó, de az is frissítheti a biztonsági házirend hagyja figyelmen kívül javaslatokra és riasztásokra.


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megismerte az Azure Security Center biztonsági házirendeket. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Az Azure Security Center adatainak biztonsági](security-center-data-security.md) -megtudhatja, hogyan adatok felügyelt és a Security Center védelmét.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági híreket és információkat.


