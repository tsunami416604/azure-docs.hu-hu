---
title: "Az Azure Security Center biztonsági szabályzatainak bemutatása |} Microsoft Docs"
description: "További tudnivalók az Azure Security Center biztonsági házirendek és a főbb funkciók."
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
ms.openlocfilehash: 60cc65bb94e05da1c0b7ee20930c0530f46e71ec
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="security-policies-overview"></a>Biztonsági házirendek – áttekintés
Ez a cikk áttekintést nyújt a Security Center biztonsági házirendeket.

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. Az Azure Security Centerben szabályzatok készítése az Azure-előfizetésekkel, és testre is szabhatja azokat a típusú vagy az adatok érzékenységének. Például szabályozott adatokat, például a személyes azonosításra alkalmas adatokat használó alkalmazásokat lehet szükség magasabb szintű biztonságot, mint a többi munkaterhelését. 

A Security Center-szabályzatok a következő összetevőkből állnak:

- **Adatgyűjtés**: meghatározza, hogy a kiépítési ügynök és [adatgyűjtés](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) beállításait.
- **Biztonsági házirend**: azt határozza meg, amely szabályozza a Security Center figyeli, és javasolja. Szerkesztheti a [biztonsági házirend](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) a biztonsági központban. Is [Azure házirend](security-center-azure-policy.md) (a korlátozott előzetes verzió) új definíciókat hozhat létre, további házirendeket definiálhat, és rendelje hozzá a házirendek felügyeleti csoporttal.
- **E-mail értesítések**: meghatározza, hogy a biztonsági névjegyeket és [értesítési e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) beállításait.
- **IP-címek**: határozza meg a szabad vagy standard [kijelölés árképzési](https://docs.microsoft.com/azure/security-center/security-center-pricing). A réteg úgy dönt, határozza meg, mely a Security Center funkciók érhetők el az erőforrások hatókörében. Megadhat egy réteget a előfizetések, erőforráscsoport-sablonok és a munkaterületek között. 


## <a name="who-can-edit-security-policies"></a>Ki is szerkesztheti a biztonsági szabályzatokat?
A Security Center használja a szerepköralapú hozzáférés-vezérlés (RBAC), amely beépített szerepkörök hozzárendelheti a felhasználók, csoportok és az Azure biztosít. Amikor a felhasználók megnyitják a Security Center, láthatják csak erőforrásokhoz férhet hozzá, kapcsolatos információkat. Ez azt jelenti, hogy a felhasználók vannak hozzárendelve szerepe *tulajdonos*, *közreműködő*, vagy *olvasó* erőforrás tartozik előfizetés vagy az erőforrás-csoporthoz. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- **Biztonsági olvasó**: rendelkezik view rights Security Center, beleértve a javaslatok, a riasztások, a házirendhez és a rendszerállapot, de azokat nem módosíthatja.
- **Biztonsági rendszergazda**: megegyező nézet jogokkal rendelkezik *biztonsági olvasó*, és azok is a biztonsági házirend frissítése, és hagyja figyelmen kívül javaslatokra és riasztásokra.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megismerte az Azure Security Center biztonsági házirendeket. Azure Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md): Útmutató: az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md): megtudhatja, hogyan Security Center javaslatait segítenek az Azure-erőforrások védelme.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Az Azure Security Center adatainak biztonsági](security-center-data-security.md): megtudhatja, hogyan kezeli a Security Center, és megvédi az adatokat.
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/): a legújabb Azure biztonsági híreket és információkat.


