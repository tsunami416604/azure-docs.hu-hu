---
title: Az Azure piactéren elérhető alkalmazás egyetlen kattintással, egyszeri bejelentkezéses (SSO) konfigurációs |} A Microsoft Docs
description: Egykattintásos konfiguráció SSO-t az alkalmazás az Azure Marketplace-ről lépései.
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872425"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Egykattintásos az egyszeri bejelentkezés konfigurálása

 Ebben az oktatóanyagban elsajátíthatja egy, az egyszeri bejelentkezés (SSO) beállításainak végrehajtani az SAML-támogatása, Azure Active Directory (Azure AD-) alkalmazásokat az Azure Marketplace-ről.

## <a name="introduction-to-one-click-sso"></a>Egykattintásos SSO bemutatása

Az egykattintásos egyszeri bejelentkezési szolgáltatás konfigurálása egyszeri bejelentkezéshez az Azure Marketplace-alkalmazások, amelyek támogatják a SAML-protokoll célja. Az Azure AD egyszeri bejelentkezési konfiguráció oldalon a beállítás lehetővé teszi az Azure AD-metaadatok automatikus konfigurálásához, az alkalmazás oldalán. Így gyorsan állíthat be egyszeri bejelentkezés manuális csatlakozhatnak.

## <a name="advantages-of-one-click-sso"></a>Egykattintásos egyszeri bejelentkezés előnyei

- Gyors egyszeri bejelentkezési konfiguráció manuális beállítása az alkalmazás oldalán igénylő alkalmazások Azure Marketplace-en.
- Hatékony és pontos egyszeri bejelentkezés konfigurálása.
- Nincs partner kommunikáció vagy telepítéséhez szükséges támogatás. Az alkalmazás a felhasználói Felületet biztosít SAML-konfigurációja.

## <a name="prerequisites"></a>Előfeltételek

- Az alkalmazás konfigurálása az egyszeri bejelentkezés aktív előfizetés. Rendszergazdai hitelesítő adatok is szükséges.
- A **saját alkalmazások biztonságos bejelentkezési bővítménye** telepítve van a böngészőben a Microsofttól. További információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Egykattintásos SSO konfigurációs lépések

1. Vegye fel az alkalmazást az Azure Marketplace-ről.

2. Válassza ki **egyszeri bejelentkezési**.

3. Válassza ki **egyszeri bejelentkezés engedélyezése**.

4. Töltse ki a kötelező konfigurációs értékeit a **alapszintű SAML-konfigurációja** szakaszban.

    > [!NOTE]
    > Ha az alkalmazás egyéni jogcímek, konfigurálnia kell, kezelni azokat egyetlen kattintással egyszeri bejelentkezés végrehajtása előtt.

5. Ha az egy kattintással egyszeri bejelentkezés funkció érhető el az Azure piactéren elérhető alkalmazás, a következő képernyőn láthatja. Előfordulhat, hogy telepítenie kell a **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kiválasztásával **a bővítmény telepítése**.

   ![Saját alkalmazások biztonságos bejelentkezési webböngésző-bővítmény telepítése](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Után adja hozzá a bővítményt a böngészőben, jelölje be az **telepítő \<alkalmazásnév\>** . Miután a program átirányítja az alkalmazás felügyeleti portálján, jelentkezzen be rendszergazdaként.

   ![A telepítő alkalmazás neve](./media/one-click-sso-tutorial/setup-sso.png)

7. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazás egyszeri Bejelentkezéssel. Győződjön meg arról, kiválasztásával **Igen**.

   ![Az automatikusan kitölti adatok mentése](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Ha az alkalmazás egyszeri bejelentkezési konfiguráció szükséges további lépéseket követve az utasításokat a lépések végrehajtásához.

8. Miután a konfiguráció véget ért, válassza ki a **OK** menti a módosításokat.

   ![Az automatikusan kitölti adatok mentése](./media/one-click-sso-tutorial/save-data.png)

9. Egy megerősítési ablakban jeleníti meg, hogy tudja, hogy az egyszeri bejelentkezés beállítást sikerült konfigurálni.

   ![Egyszeri bejelentkezés konfigurálása](./media/one-click-sso-tutorial/sso-configured.png)

10. Miután a konfiguráció sikeres, meg az alkalmazás kijelentkezik és visszakerül az Azure Portalon.

11. Választhat **tesztelése** egyszeri bejelentkezés teszteléséhez.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Mi az a saját alkalmazások biztonságos bejelentkezési böngészőbővítmény?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
