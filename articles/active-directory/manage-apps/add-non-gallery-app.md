---
title: Nem katalógusalkalmazás hozzáadása – Microsoft identity platform | Microsoft dokumentumok
description: Nem katalógusalkalmazás hozzáadása az Azure AD-bérlőhöz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063611"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Nem listázott (katalóguson kívüli) alkalmazás hozzáadása az Azure AD-szervezethez

Az [Azure AD-alkalmazásgyűjteményben](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)választható lehetőségek mellett lehetősége van egy **nem katalógusalkalmazás**hozzáadására is. Hozzáadhat bármely alkalmazást, amely már létezik a szervezetben, vagy bármely külső alkalmazás egy szállítótól, aki még nem része az Azure AD-katalógusban. A [licencszerződéstől](https://azure.microsoft.com/pricing/details/active-directory/)függően a következő képességek érhetők el:

- Bármely olyan alkalmazás önkiszolgáló integrációja, amely támogatja [a Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) identitásszolgáltatókat (SP által kezdeményezett vagy IdP által kezdeményezett)
- Bármely olyan webalkalmazás önkiszolgáló integrációja, amely [jelszóalapú egyszeri bejelentkezési](what-is-single-sign-on.md#password-based-sso) lapot használ,
- A Rendszer tartományok [közötti identitáskezelési (SCIM) protokollt](../app-provisioning/use-scim-to-provision-users-and-groups.md) használó alkalmazások önkiszolgáló csatlakoztatása a felhasználók kiépítéséhez
- Az [Office 365 alkalmazásindítójában](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) vagy az [Azure AD hozzáférési paneljén](what-is-single-sign-on.md#linked-sign-on) bármely alkalmazásra mutató hivatkozások hozzáadása

Ez a cikk ismerteti, hogyan adhat hozzá egy nem katalógusalkalmazást az **Enterprise Applications** az Azure Portalon kód írása nélkül. Ha ehelyett fejlesztői útmutatást keres az egyéni alkalmazások Azure AD-vel való integrálásához, olvassa el [az Azure AD hitelesítési forgatókönyvei című témakört.](../develop/authentication-scenarios.md) Ha olyan alkalmazást fejleszt, amely egy modern protokollt, például [az OpenId Connect/OAuth protokollt](../develop/active-directory-v2-protocols.md) használja a felhasználók hitelesítéséhez, regisztrálhatja azt a Microsoft-identitásplatformon az Azure Portalon az [alkalmazásregisztrációk](../develop/quickstart-register-app.md) használatával.

## <a name="add-a-non-gallery-application"></a>Nem katalógusalkalmazás hozzáadása

1. Jelentkezzen be az [Azure Active Directory portálon](https://aad.portal.azure.com/) a Microsoft identity platform rendszergazdai fiókjával.

2. Válassza **a Vállalati alkalmazások** > **új alkalmazása**lehetőséget.

3. (Nem kötelező, de ajánlott) Az **Azure AD Gallery böngészése** keresőmezőbe írja be az alkalmazás megjelenítendő nevét. 

4. Válassza **a Saját alkalmazás létrehozása**lehetőséget. Megjelenik **a Saját alkalmazás** létrehozása lap.

   ![Alkalmazás hozzáadása](media/add-non-gallery-app/create-your-own-application.png)

5. Kezdje el beírni az új alkalmazás megjelenítendő nevét. Ha vannak hasonló nevű galériaalkalmazások, azok megjelennek a keresési eredmények listájában.

   > [!NOTE]
   > Azt javasoljuk, hogy amikor csak lehetséges, használja az alkalmazás galéria verzióját. Ha a hozzáadni kívánt alkalmazás megjelenik a keresési eredmények között, jelölje ki az alkalmazást, és hagyja ki az eljárás további részét.

6. A **Mit keres, hogy köze van az alkalmazás?** válassza **integrálja más alkalmazás nem találja a galériában**. Ez a beállítás általában SAML és WS-Fed alkalmazásokhoz használatos.

   > [!NOTE]
   > A másik két lehetőség a következő esetekben használatos:
   >* **Alkalmazásproxy konfigurálása a helyszíni alkalmazások biztonságos távoli eléréséhez megnyitja** az Azure AD alkalmazásproxy és az összekötők konfigurációs lapját.
   >* **Regisztráljon egy alkalmazást, amelyen az Azure AD-vel való integrációhoz dolgozik,** megnyitja az **alkalmazásregisztrációk** lapot. Ezt a beállítást általában openId connect alkalmazásokhoz használják.

7. Kattintson a **Létrehozás** gombra. Megnyílik az alkalmazás **áttekintése** lap.

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

1. A **Tulajdonságok** lehetőséget választva nyissa meg a tulajdonságok ablaktáblát szerkesztésre.

    ![Tulajdonságok szerkesztése ablaktábla](media/add-non-gallery-app/edit-properties.png)

2. Adja meg az alábbi beállításokat annak meghatározásához, hogy az alkalmazáshoz hozzárendelt vagy hozzá nem rendelt felhasználók hogyan jelentkezhetnek be az alkalmazásba, és hogy a felhasználó láthatja-e az alkalmazást a hozzáférési panelen.

    - Az **Engedélyezett a felhasználók számára a bejelentkezés** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók bejelentkezhetnek-e.
    - **A szükséges felhasználóhozzárendelés** határozza meg, hogy az alkalmazáshoz nem rendelt felhasználók bejelentkezhetnek-e.
    - A **Felhasználók számára látható** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók láthatják-e az alkalmazást a hozzáférési panelen és az O365 indítójában.

      Működés **hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Engedélyezett a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a hozzárendelt felhasználók? | Látható az alkalmazás a hozzárendelt felhasználók számára?* |
       | igen | igen | igen | igen | igen  |
       | igen | igen | nem  | igen | nem   |
       | igen | nem  | igen | igen | igen  |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

      Működés **nem hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – nem hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Engedélyezve van a felhasználók számára a bejelentkezéshez? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
       | igen | igen | igen | nem  | nem   |
       | igen | igen | nem  | nem  | nem   |
       | igen | nem  | igen | igen | nem   |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

     *Látható az alkalmazás a felhasználó számára a hozzáférési panelen és az Office 365 alkalmazás indítójában?

3. Egyéni embléma használatához hozzon létre egy 215 x 215 képpont méretű emblémát, és mentse PNG formátumban. Ezután keresse meg az emblémát, és töltse fel.

    ![Embléma módosítása](media/add-non-gallery-app/change-logo.png)

4. Ha végzett, válassza a **Mentés gombot.**

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válasszon ki egy bejelentkezési módszert,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) amelyet használni szeretne, és olvassa el az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezéskonfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
