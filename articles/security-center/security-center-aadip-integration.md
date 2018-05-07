---
title: Csatlakozás az Azure Active Directory azonosító adatok védelmét az Azure Security Center |} Microsoft Docs
description: Ismerje meg, hogy az Azure Security Center hogyan integrálható az Azure Active Directory azonosító adatok védelmét.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Csatlakozás az Azure Active Directory azonosító adatok védelmét az Azure Security Center
Ez a dokumentum segítséget nyújt az Azure Active Directory (AD) identitás védelmét és az Azure Security Center közötti integráció konfigurálásához.

## <a name="why-connect-azure-ad-identity-protection"></a>Miért kell csatlakoztatni az Azure AD Identity Protection?
[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) megkönnyíti a nehezen észlelhető a szervezet identitásait érintő potenciális biztonsági réseket. Csatlakozás, megtörténik a Security Center az Azure AD Identity Protection-riasztások megtekintése. Ez az integráció lehetővé teszi nézetben összefüggéseket, és vizsgálja meg a hibrid felhő-munkaterhelések biztonsági központ kapcsolódó összes biztonsági értesítést. 

## <a name="how-do-i-configure-this-integration"></a>Ez az integráció konfigurálása
Ha a szervezet már használja az Azure AD Identity Protection, kövesse az alábbi lépéseket az integráció konfigurálásához:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali ablaktábláján kattintson **biztonsági megoldások**. A Security Center automatikusan észleli, hogy engedélyezve van-e az Azure AD Identity Protection a szervezet számára.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Kattintson a **CONNECT**.
4. Az a **integrálni Azure AD Identity Protection** lapon görgessen lefelé, és válassza ki a megfelelő munkaterületre:

    ![Munkaterület](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Kattintson a **Connect** (Csatlakozás) gombra.

Ez a konfiguráció befejezése után az Azure AD Identity Protection megoldás megjelenik a **biztonsági megoldások** lap **kapcsolódó megoldások**. 

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerte a Security Center az Azure AD Identity Protection csatlakozni. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [A Microsoft Advanced Threat Analytics csatlakoztatása az Azure Security Centerhez](security-center-ata-integration.md)
* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Az Azure Security Center adatainak biztonsági](security-center-data-security.md) -megtudhatja, hogyan adatok felügyelt és a Security Center védelmét.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági híreket és információkat.


