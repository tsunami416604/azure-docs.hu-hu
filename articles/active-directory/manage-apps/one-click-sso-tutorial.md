---
title: Az Azure Marketplace-alkalmazás egykattintásos, egyszeri bejelentkezési (SSO) konfigurációja | Microsoft dokumentumok
description: Az alkalmazás egyszeri konfigurálásának lépései az Azure Piactérről.
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
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67872425"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Egyszeri bejelentkezés egykattintásos alkalmazáskonfigurációja

 Ebben az oktatóanyagban megtudhatja, hogyan hajthatja végre az egykattintásos, egyszeri bejelentkezési (Egyszeri bejelentkezés) konfigurációsaml-támogató, Azure Active Directory (Azure AD) alkalmazások az Azure Marketplace-en.

## <a name="introduction-to-one-click-sso"></a>Bevezetés az egykattintásos összehacsisítás ba

Az egykattintásos egyszeri bejelentkezés funkció az SAML protokollt támogató Azure Marketplace-alkalmazások egyszeri bejelentkezésének konfigurálására szolgál. Az Azure AD SSO konfigurációs lapon ez a beállítás lehetővé teszi, hogy automatikusan konfigurálja az Azure AD metaadatokat az alkalmazás oldalán. Ily módon gyorsan beállíthatja az SSO-t minimális manuális erőfeszítéssel.

## <a name="advantages-of-one-click-sso"></a>Az egykattintásos szóhasználatának előnyei

- Az Azure Marketplace-alkalmazások gyors SSO-konfigurációja, amelyek az alkalmazás oldalon manuális beállítást igényelnek.
- Hatékonyabb és pontosabb SSO konfiguráció.
- A beállításhoz nincs szükség partnerkommunikációra vagy támogatásra. Az alkalmazás biztosítja az SAML-konfiguráció felhasználói felületét.

## <a name="prerequisites"></a>Előfeltételek

- Az alkalmazás aktív előfizetése az SSO-val konfigurálandó alkalmazáshoz. Rendszergazdai hitelesítő adatokra is szüksége van.
- A **Microsoft My Apps Secure bejelentkezési bővítménye** telepítve van a böngészőben. További információt az Alkalmazások portálon lévő [Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)talál.

## <a name="one-click-sso-configuration-steps"></a>Egykattintásos egyszeri szolgáltatás konfigurációs lépései

1. Adja hozzá az alkalmazást az Azure Piactérről.

2. Válassza **az Egyszeri bejelentkezés**lehetőséget.

3. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.

4. A kötelező konfigurációs értékek feltöltése az **Egyszerű SAML konfiguráció szakaszban.**

    > [!NOTE]
    > Ha az alkalmazás egyéni jogcímeket, amelyeket be kell állítania, kezelje őket, mielőtt egy kattintással egyszeri szolgáltató.

5. Ha az egykattintásos egyszeri szolgáltatás elérhető az Azure Marketplace-alkalmazáshoz, a következő képernyő jelenik meg. Előfordulhat, hogy telepítenie kell a **My Apps Secure Bejelentkezésböngésző bővítményt** **a Bővítmény telepítése**lehetőség kiválasztásával.

   ![A Saját alkalmazások biztonságos bejelentkezési böngészőbővítményének telepítése](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Miután hozzáadja a bővítményt a böngészőhöz, válassza a **Telepítőalkalmazás \<neve lehetőséget.\>** Miután átirányítja az alkalmazás felügyeleti portálra, jelentkezzen be rendszergazdaként.

   ![Telepítőalkalmazás neve](./media/one-click-sso-tutorial/setup-sso.png)

7. A böngészőbővítmény automatikusan konfigurálja az SSO-t az alkalmazásban. A megerősítés az Igen lehetőség kiválasztásával megerősítést **kér.**

   ![Az automatikusan kitöltött adatok mentése](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Ha az alkalmazás sso-konfigurációja további lépéseket igényel, kövesse a lépéseket a lépések végrehajtásához.

8. A konfiguráció befejezése után a módosítások mentéséhez válassza az **OK gombot.**

   ![Az automatikusan kitöltött adatok mentése](./media/one-click-sso-tutorial/save-data.png)

9. Egy megerősítő ablak jelenik meg, amely tudatja, hogy az SSO-beállítások sikeresen konfigurálva vannak.

   ![SSO konfigurálva](./media/one-click-sso-tutorial/sso-configured.png)

10. A konfiguráció sikeres befejezése után ki jelentkezett az alkalmazásból, és visszatért az Azure Portalra.

11. Az egyszeri bejelentkezés teszteléséhez a **Teszt** lehetőséget választva választhatja ki.

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Mi a My Apps Secure Bejelentkezési böngészőbővítmény?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
