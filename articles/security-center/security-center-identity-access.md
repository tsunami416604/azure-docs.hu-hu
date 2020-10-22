---
title: Identitás és hozzáférés figyelése az Azure Security Centerben | Microsoft Docs
description: Itt megtudhatja, hogyan használható az Azure Security Center identitási és hozzáférési funkciója a felhasználók hozzáférési tevékenységeinek és identitással kapcsolatos problémáinak figyelésére.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 575c139a3b417eb9429695d3ea6be26bf5625de5
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371069"
---
# <a name="monitor-identity-and-access"></a>Identitás és hozzáférés monitorozása

A biztonsági szegély egy hálózati kerületből lett kialakulóban egy identitási peremhálózaton. Ezzel a fejlesztéssel a biztonság kevésbé foglalkozik a hálózat védelmével, és többet tudhat meg az alkalmazások, az információk és a felhasználók biztonságának kezeléséről.

Az identitással kapcsolatos tevékenységek és konfigurációs beállítások figyelésével proaktív műveleteket hajthat végre az incidensek megkezdése előtt, illetve a megkísérelt támadások leállításához szükséges reaktív műveleteket.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Milyen identitási és hozzáférési óvintézkedéseket Security Center nyújtani? 

Azure Security Center két dedikált biztonsági vezérlővel rendelkezik, amelyekkel biztosíthatja, hogy megfeleljen a szervezete identitásának és biztonsági követelményeinek: 

 - **Hozzáférés és engedélyek kezelése** – javasoljuk, hogy fogadja el a [legkevésbé privilegizált hozzáférési modellt](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) , és gondoskodjon arról, hogy a felhasználók csak a feladataik elvégzéséhez szükséges hozzáférést biztosítsanak. Ez a vezérlő a [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) megvalósítására vonatkozó javaslatokat is tartalmaz az erőforrásokhoz való hozzáférés szabályozásához.
 
 - Többtényezős hitelesítés **engedélyezése** – az [MFA](https://www.microsoft.com/security/business/identity/mfa) engedélyezve van, a fiókok biztonságosabbak, és a felhasználók továbbra is elvégezhetik az egyszeri bejelentkezéssel szinte bármilyen alkalmazást.

### <a name="example-recommendations-for-identity-and-access"></a>Példa az identitásra és a hozzáférésre vonatkozó javaslatok

Példák a Security Center **ajánlásai** oldalon látható két vezérlőre:

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az elavult fiókokat el kell távolítani az előfizetésből (az elavult fiókok olyan fiókok, amelyek már nem szükségesek, és a bejelentkezés nem engedélyezett Azure Active Directory)

> [!TIP]
> További információ ezekről az ajánlásokról és a vezérlőkben esetlegesen megjelenő további információkról: [identitás-és hozzáférési javaslatok](recommendations-reference.md#recs-identity).

### <a name="limitations"></a>Korlátozások

A Security Center identitás-és hozzáférés-védelmének bizonyos korlátai vannak:

- Az Identity javaslatok nem érhetők el több mint 600 fiókkal rendelkező előfizetésekhez. Ilyen esetekben ezek a javaslatok a "nem elérhető értékelések" területen jelennek meg.
- Az identitásra vonatkozó javaslatok nem érhetők el a Cloud Solution Provider (CSP) partner rendszergazdai ügynökei számára.
- Az identitással kapcsolatos javaslatok nem azonosítják azokat a fiókokat, amelyeket a rendszer az emelt szintű Identity Management (PIM) rendszerrel felügyel. Ha PIM-eszközt használ, pontatlan eredmények jelenhetnek meg a **hozzáférés és engedélyek kezelése** vezérlőelemben.

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Multi-Factor Authentication (MFA) és Azure Active Directory 

Az MFA engedélyezéséhez [Azure Active Directory (ad) bérlői engedélyek](../active-directory/roles/permissions-reference.md)szükségesek.

- Ha prémium szintű AD-kiadással rendelkezik, a [feltételes hozzáférés](../active-directory/conditional-access/concept-conditional-access-policy-common.md)használatával engedélyezze az MFA-t.
- Ha az AD ingyenes kiadást használja, engedélyezze a **biztonsági alapértelmezéseket** a [Azure Active Directory dokumentációjában](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)leírtak szerint.

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>A multi-Factor Authentication (MFA) engedélyezése nélküli fiókok azonosítása

A következő Azure Resource Graph-lekérdezéssel megtekintheti, hogy mely fiókok esetében nem engedélyezett az MFA használata. A lekérdezés az összes nem megfelelő erőforrást adja vissza – a "MFA engedélyezése az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokban". 

1. Nyissa meg az **Azure Resource Graph Explorert**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Az Azure Resource Graph Explorer * * ajánlási oldalának elindítása" :::

1. Adja meg a következő lekérdezést, és válassza a **lekérdezés futtatása**lehetőséget.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. A `additionalData` tulajdonság felfedi az MFA által kényszerített fiókokhoz tartozó fiókazonosító-azonosítók listáját. 

    > [!NOTE]
    > A fiókok neve helyett objektumazonosítókként jelennek meg a fiókok tulajdonosainak védelme érdekében.

> [!TIP]
> Alternatív megoldásként használhatja a Security Center REST API metódusának [értékelését – Get](/rest/api/securitycenter/assessments/get).


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikket:

- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)