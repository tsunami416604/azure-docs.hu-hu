---
title: Konfigurálni egy több kattintson az egyszeri bejelentkezés az alkalmazáshoz az Azure AD-alkalmazásgyűjtemény |} A Microsoft Docs
description: Egy kattintson az egyszeri bejelentkezés konfigurálása az alkalmazáshoz az Azure AD-alkalmazásgyűjtemény a lépéseket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057847"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Az Azure AD-katalógusban alkalmazások egyetlen kattintson az egyszeri bejelentkezési funkcióval

 Ebben az oktatóanyagban elsajátíthatja, hogyan hajthat végre egy kattintson az egyszeri bejelentkezés az összes a SAML-alkalmazáshoz, amely a felhasználói Felületet biztosít egyszeri bejelentkezési konfiguráció.

## <a name="introduction-to-one-click-sso"></a>Egyszeri bejelentkezés egyetlen kattintással bemutatása

Az egyszeri bejelentkezés az Azure AD katalógusban SAML-protokoll támogató alkalmazások esetében konfigurálhatja egy kattintson az egyszeri bejelentkezés funkció jelenik meg. Az Azure AD SSO konfigurációs lapján adtunk meg ezzel a beállítással lehetővé teszi ügyfeleink számára, hogy az Azure AD-metaadatok automatikus konfigurálása az alkalmazás oldalán. A célja segítségével az ügyfelek beállításával SSO gyorsan minimális manuális törekvésekhez társítva. 

## <a name="advantages-of-the-one-click-sso"></a>A előnyei kattintson az egyszeri bejelentkezés

- A katalógus alkalmazásokat, ahol az ügyfelek manuális beállítást az alkalmazás oldalán kell gyors egyszeri bejelentkezés konfigurációja.
- Több hatékony és pontos módszer konfigurálása.
- Nincs partner kommunikáció vagy az alkalmazás telepítéséhez szükséges támogatás a felhasználói Felületet biztosít SAML-konfigurációja.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetés rendszergazdai hitelesítő adataival, amely OneClick SSO-val konfigurálni szeretné az alkalmazás.
- **Saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** telepítve van a böngészőben a Microsofttól. Ha szeretne többet megtudni a bővítmény, tekintse meg a [hivatkozás](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>Egy egyszeri bejelentkezési kattintson a szolgáltatás részletes részletei

1. Adja hozzá az alkalmazást az Azure AD-alkalmazásgyűjtemény.

2. Kattintson az egyszeri bejelentkezést.

3. Kattintson az engedélyezni az egyszeri bejelentkezés.

4. Adja meg a kötelező konfigurációs értékeket alapszintű SAML konfigurációs szakaszban.

    > [!NOTE] 
    > Ha az alkalmazás egyéni jogcímek konfigurációs van szüksége, állítsa be őket OneClick egyszeri bejelentkezés végrehajtása előtt.

5. Ha egy kattintson az egyszeri bejelentkezés funkció minden katalógusból származó alkalmazásra valósul meg, a következő képernyőn láthatja. Ha a **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** van még nem telepítette, akkor kattintson a **a bővítmény telepítése** lehetőséget.

    ![Saját alkalmazások biztonságos bejelentkezési webböngésző-bővítmény telepítése](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. A bővítmény hozzáadása a böngészőre, után kattintson a **telepítő alkalmazásnév** amely átirányítja az alkalmazás-felügyeleti portálon. Jelentkezzen be rendszergazdaként az alkalmazások beszerzése a szüksége.

    ![A telepítő alkalmazás neve](./media/one-click-sso-tutorial/setup-sso.png)

7. A webböngésző-bővítmény most már automatikusan konfigurálja az alkalmazás az Ön számára. Először kéri a megerősítés Ha folytatni kívánja. Kattintson a **Yes** (Igen) gombra.

    ![Az automatikus mentését kitölti az adatokat](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Ha bármely alkalmazás extra nagivation vagy lépéseket igényel, ezek a lépések végrehajtásához, amely felkéri megfelelő üzenetek kell megjelennie. 

8. Miután végzett a konfiguráció megadásával, kattintson a **Ok** menti a módosításokat.

    ![Az automatikusan adatok mentése](./media/one-click-sso-tutorial/save-data.png)

9. Sikeres megerősítés felbukkanó üzenet jelenik meg, és az egyszeri bejelentkezési beállításainak konfigurálása sikerült. Tesztelje az alkalmazást.

    ![Egyszeri bejelentkezés konfigurálása](./media/one-click-sso-tutorial/sso-configured.png)

10. Miután a konfiguráció sikeresen befejeződött, az alkalmazás ki lesz léptetve, a rendszer visszalépteti vissza az Azure Portalon.

11. Kattintson az alsó teszt gombra, az egyszeri bejelentkezés teszteléséhez.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Mi az a saját alkalmazások biztonságos bejelentkezési böngészőbővítmény](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 