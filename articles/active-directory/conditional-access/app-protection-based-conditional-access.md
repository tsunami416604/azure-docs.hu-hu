---
title: Alkalmazás-védelmi szabályzatok feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan igényelhet alkalmazás-védelmi szabályzatot a felhőalapú alkalmazásokhoz való hozzáféréshez Azure Active Directory-ben való feltételes hozzáféréssel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080065"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Útmutató: az alkalmazás védelmi szabályzatának és a felhőalapú alkalmazások hozzáférésének megkövetelése feltételes hozzáféréssel

A felhasználók a személyes és munkahelyi feladatokhoz egyaránt rendszeresen használják a mobileszközök használatát. A munkavégzés hatékonysága érdekében a szervezetek emellett azt is meg szeretnék akadályozni, hogy a potenciálisan nem biztonságos alkalmazások adatvesztést okoznak. A feltételes hozzáféréssel a szervezetek a jóváhagyott (modern hitelesítésre képes) ügyfélalkalmazások hozzáférését a rájuk alkalmazott Intune app Protection-szabályzatokkal korlátozhatják.

Ez a cikk két forgatókönyvet mutat be a feltételes hozzáférési házirendek konfigurálásához olyan erőforrásokhoz, mint az Office 365, az Exchange Online és a SharePoint Online.

- [1. forgatókönyv: az Office 365-alkalmazásokban jóváhagyott alkalmazások szükségesek az alkalmazás-védelmi házirendekkel](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [2. forgatókönyv: az Exchange Online és a SharePoint Online esetében jóváhagyott ügyfélalkalmazás és alkalmazás-védelmi szabályzat szükséges](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

A feltételes hozzáférésben ezek az ügyfélalkalmazások ismertek, hogy védelmet kapnak az alkalmazás védelmi házirendjével. Az alkalmazás-védelmi szabályzatokkal kapcsolatos további információkért tekintse meg az [app Protection-házirendek áttekintése](/intune/apps/app-protection-policy) című cikket.

A jogosult ügyfélalkalmazások listáját a következő témakörben tekintheti meg: az [alkalmazás védelmére vonatkozó követelmény](concept-conditional-access-grant.md).

> [!NOTE]
>    A (vagy) záradék a szabályzaton belül használatos ahhoz, hogy a felhasználók olyan alkalmazásokat használjanak, amelyek támogatják az **alkalmazás-védelmi házirend megkövetelését** vagy a **jóváhagyott ügyfélalkalmazás** -engedélyezési vezérlőket. További információ arról, hogy mely alkalmazások támogatják az **alkalmazás-védelmi házirend megkövetelése** vezérlő használatát: az [alkalmazás védelmére vonatkozó követelmény](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>1\. forgatókönyv: az Office 365-alkalmazásokban jóváhagyott alkalmazások szükségesek az alkalmazás-védelmi házirendekkel

Ebben a forgatókönyvben a contoso úgy döntött, hogy az Office 365-erőforrásokhoz való összes mobil hozzáféréshez jóváhagyott ügyfélalkalmazások, például az Outlook Mobile, a OneDrive és a Microsoft Teams által védett alkalmazások férhetnek hozzá a hozzáférés megkezdése előtt. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő lépéseket kell végrehajtaniuk ahhoz, hogy a jóváhagyott ügyfélalkalmazás használatát kötelezővé lehessen tenni a mobileszközökön.

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Office 365-hez**

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy actions** ** > területen válassza az** **Office 365 (előzetes verzió)** lehetőséget.
1. A **feltételek**területen válassza az **eszközök platformok**elemet.
   1. Állítsa **az** **Igen**értékre.
   1. **Android** és **iOS**is.
1. A **feltételek**területen válassza az **ügyfélalkalmazások (előzetes verzió)** lehetőséget.
   1. Állítsa **az** **Igen**értékre.
   1. Válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.
1. A **hozzáférés-vezérlés** > **megadása**területen válassza ki a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **A kiválasztott vezérlők egyikének megkövetelése**
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az ActiveSync (EAS) segítségével**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **felhőalapú alkalmazások vagy műveletek** > a **következők**: válassza az **Office 365 Exchange Online**lehetőséget.
1. **Feltételek**:
   1. **Ügyfélalkalmazások (előzetes verzió)** :
      1. Állítsa **az** **Igen**értékre.
      1. Válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.
1. A **hozzáférés-vezérlés** > a **támogatás**területen válassza a **hozzáférés engedélyezése**, az **alkalmazás-védelmi házirend megkövetelése**, majd a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>2\. forgatókönyv: az Exchange Online és a SharePoint Online esetében jóváhagyott ügyfélalkalmazás és alkalmazás-védelmi szabályzat szükséges

Ebben az esetben a contoso úgy döntött, hogy a felhasználók csak akkor férhetnek hozzá a mobileszközök e-mail-és SharePoint-adataihoz, ha olyan jóváhagyott ügyfélalkalmazás-alkalmazást használnak, mint az alkalmazás-védelmi szabályzat által védett Outlook Mobile. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő három lépést kell végrehajtaniuk ahhoz, hogy egy jóváhagyott ügyfélalkalmazás használatát meg lehessen követelni a mobileszközök és az Exchange ActiveSync-ügyfelek számára.

**1. lépés: az Android-és iOS-alapú modern hitelesítési ügyfelekre vonatkozó szabályzat, amely a jóváhagyott ügyfélalkalmazás és az alkalmazás-védelmi szabályzat használatát igényli az Exchange Online-hoz és a SharePoint Online-hoz való hozzáféréshez.**

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **felhőalapú alkalmazások vagy műveletek** > a **következők**: válassza az **Office 365 Exchange Online** és az **Office 365 SharePoint Online**lehetőséget.
1. A **feltételek**területen válassza az **eszközök platformok**elemet.
   1. Állítsa **az** **Igen**értékre.
   1. **Android** és **iOS**is.
1. A **feltételek**területen válassza az **ügyfélalkalmazások (előzetes verzió)** lehetőséget.
   1. Állítsa **az** **Igen**értékre.
   1. Válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.
1. A **hozzáférés-vezérlés** > **megadása**területen válassza ki a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **A kiválasztott vezérlők egyikének megkövetelése**
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**2. lépés: a jóváhagyott ügyfélalkalmazás használatát igénylő Exchange ActiveSync-ügyfelek házirendje.**

1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **felhőalapú alkalmazások vagy műveletek** > a **következők**: válassza az **Office 365 Exchange Online**lehetőséget.
1. **Feltételek**:
   1. **Ügyfélalkalmazások (előzetes verzió)** :
      1. Állítsa **az** **Igen**értékre.
      1. Válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.
1. A **hozzáférés-vezérlés** > a **támogatás**területen válassza a **hozzáférés engedélyezése**, az **alkalmazás-védelmi házirend megkövetelése**, majd a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára.**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="next-steps"></a>Következő lépések

[Mi a feltételes hozzáférés?](overview.md)

[Feltételes hozzáférési összetevők](concept-conditional-access-policies.md)

[Általános feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)

