---
title: 'Oktatóanyag: A LinkedIn-elévülés felhasználói kiépítése – Azure AD'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a LinkedIn-elevate szolgáltatásba.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0a26eaeac431ed2c78c5bd938bbbe7dff14e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057413"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Oktatóanyag: A LinkedIn-elevate konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a LinkedIn-elevate és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és a LinkedIn-elevate-hoz való automatikus kiépítéséhez és a felhasználói fiókok kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* LinkedIn-bérlő
* Rendszergazdai fiók a LinkedIn-fiókban a LinkedIn-fiókközponthoz való hozzáféréssel

> [!NOTE]
> Az Azure Active Directory integrálható a LinkedIn Elevate az [SCIM](http://www.simplecloud.info/) protokoll használatával.

## <a name="assigning-users-to-linkedin-elevate"></a>Felhasználók hozzárendelése a LinkedIn-elévüléshez

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálódnak.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a LinkedIn-elevate-hoz. Miután eldöntötte, ezeket a felhasználókat a LinkedIn Elevate-hoz rendelheti az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Fontos tippek a felhasználók LinkedIn-elévüléshez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a LinkedIn-elevate-hoz a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a LinkedIn Elevate-hoz rendel, ki kell **választania** a Felhasználói szerepkört a hozzárendelési párbeszédpanelen. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>A felhasználó kiépítésének konfigurálása a LinkedIn-elévüléshez

Ez a szakasz végigvezeti az Azure AD-nek a LinkedIn-elítási rendszerhez való csatlakoztatásán a LinkedIn-fiók hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásán keresztül a linkedin-fiókhoz rendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a felhasználó- és csoporthozzárendelések alapján az Azure AD-ben.

**Tipp:** Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a LinkedIn-eloldalon. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>A LinkedIn-elévítás automatikus felhasználói fiókkiépítés konfigurálása az Azure AD-ben:

Az első lépés a LinkedIn-hozzáférési jogkivonat beolvasása. Ha Ön vállalati rendszergazda, önkiszolgáló hozzáférési jogkivonatot hozhat ki. A fiókközpontban nyissa meg a ** &gt; Beállítások globális beállításai lapot,** és nyissa meg az **SCIM telepítőpaneljét.**

> [!NOTE]
> Ha a fiókközpontot közvetlenül, nem pedig egy hivatkozáson keresztül éri el, a következő lépésekkel érheti el.

1. Jelentkezzen be az Ügyfélközpontba.

2. Válassza a **Rendszergazdai &gt; rendszergazdai beállítások lehetőséget** .

3. Kattintson a bal oldali oldalsáv speciális **integrációi** gombjára. A központba irányítja a kapcsolatot.

4. Kattintson **a + Új SCIM-konfiguráció hozzáadása** gombra, és kövesse az eljárást az egyes mezők kitöltésével.

    > [!NOTE]
    > Ha az automatikus hozzárendelési licencek nincsenek engedélyezve, az azt jelenti, hogy csak a felhasználói adatok lesznek szinkronizálva.

    ![LinkedIn felépítés](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Ha az automatikus licenc-hozzárendelés engedélyezve van, meg kell jegyeznie az alkalmazáspéldányt és a licenctípusát. A licenceket az első alkalommal, az első alkalommal rendelik hozzá, amíg az összes licencet el nem veszik.

    ![LinkedIn felépítés](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Kattintson **a Token létrehozása gombra.** A hozzáférési jogkivonat nak az **Access token** mező alatt kell látnia.

6. Mentse a hozzáférési jogkivonatot a vágólapra vagy a számítógépre, mielőtt elhagyja a lapot.

7. Ezután jelentkezzen be az [Azure Portalra](https://portal.azure.com), és keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

8. Ha már konfigurálta a LinkedIn Elevate-ot egyszeri bejelentkezéshez, keresse meg a LinkedIn Elevate példányát a keresőmező használatával. Ellenkező esetben válassza a **LinkedIn-elévülés** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza a LinkedIn Elevate elemet a keresési eredmények között, és vegye fel az alkalmazások listájára.

9. Válassza ki a LinkedIn-elevate példányát, majd válassza a **Kiépítés** lapot.

10. Állítsa a **létesítési módot** **Automatikus**ra.

    ![LinkedIn felépítés](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Töltse ki a következő mezőket a **Rendszergazdai hitelesítő adatok** csoportban:

    * A **Bérlő URL-címe** mezőbe írja be a mezőt. `https://api.linkedin.com`

    * A **Titkos jogkivonat** mezőbe írja be az **1.**

    * A portál jobb felső részén sikeres értesítést kell látnia.

12. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be az alábbi jelölőnégyzetet.

13. Kattintson a **Mentés** gombra.

14. Az **Attribútum-leképezések** szakaszban tekintse át a felhasználói és csoport attribútumok, amelyek szinkronizálni fogják az Azure AD linkedin elevate. Vegye figyelembe, hogy az **Egyező** tulajdonságokként kijelölt attribútumok a LinkedIn-elévülés felhasználói fiókjainak és csoportjainak megfelelően lesznek felhasználva a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

    ![LinkedIn felépítés](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Az Azure AD-kiépítési szolgáltatás linkedin-elevate engedélyezéséhez módosítsa a **Kiépítési állapot** ot **bekapcsolva** állapotra a **Beállítások** szakaszban

16. Kattintson a **Mentés** gombra.

Ezzel megkezdődik a Felhasználók és csoportok szakaszBan a LinkedIn Elevate-hoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálása. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a LinkedIn-elevate alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
