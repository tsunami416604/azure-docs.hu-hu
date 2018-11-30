---
title: Az Azure Active Directory Identity Protection csatlakoztatása az Azure Security Centerben |} A Microsoft Docs
description: Ismerje meg, hogy az Azure Security Center hogyan integrálható az Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 2ed5791b352ee4b19f707e52bc6f8f78e1f0e5b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635305"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Az Azure Active Directory Identity Protection csatlakoztatása az Azure Security Centerben
Ez a dokumentum segít az Azure Active Directory (AD) Identity Protection és az Azure Security Center közötti integráció beállítása.

## <a name="why-connect-azure-ad-identity-protection"></a>Miért érdemes összekapcsolni az Azure AD Identity Protection?
[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) megkönnyíti a nehezen észlelhető a szervezet identitásait érintő lehetséges biztonsági résekről. Ha csatlakoztatva van, Ön megtekintheti az Azure AD Identity Protection riasztásai a Security Centerben. Ez az integráció lehetővé teszi a megtekintése, összehasonlítása és a hibrid felhőalapú számítási feladatokhoz, a Security Center összes biztonsági riasztások vizsgálata.

## <a name="how-do-i-configure-this-integration"></a>Hogyan konfigurálhatom ezt az integrációt?
Ha a szervezet már használja az Azure AD Identity Protection, hajtsa végre az integráció konfigurálásához az alábbi lépéseket:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali panelen kattintson a **biztonsági megoldások**. A Security Center automatikusan felderíti, ha az Azure AD Identity Protection engedélyezve van-e a szervezet számára.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Kattintson a **CONNECT**.
4. Az a **integrálása az Azure AD Identity Protection** lapon görgessen le, és válassza ki a megfelelő munkaterületet:

    ![munkaterület](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Kattintson a **Connect** (Csatlakozás) gombra.

Miután befejezte ezt a konfigurációt, az Azure AD Identity Protection megoldás megjelenik a **biztonsági megoldások** lap **csatlakoztatott megoldások**.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure AD Identity Protection csatlakoztatása a Security centerhez. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [A Microsoft Advanced Threat Analytics csatlakoztatása az Azure Security Centerhez](security-center-ata-integration.md)
* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-azure-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Az Azure Security Center által nyújtott adatbiztonság](security-center-data-security.md) – ismerje meg, hogy az adatokat, és hogyan gondoskodik a védelmükről a Security Centerben.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.
