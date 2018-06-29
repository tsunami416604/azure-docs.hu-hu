---
title: 'Oktatóanyag: Cisco Spark konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikus kiépítése és leépíti a Cisco külső felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: 74907693270e6cd340d3b34585a80077aa87f0f7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059072"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Cisco Spark konfigurálása


Ez az oktatóanyag célja az Cisco Spark és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan ellátásához, majd leépíti a felhasználók számára a Cisco Spark végrehajtandó lépések bemutatása.


> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek teljesülését:

*   Az Azure AD-bérlő
*   A Cisco Spark bérlők
*   A Cisco Spark rendszergazdai jogosultságokkal rendelkező felhasználói fiókot


> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [Cisco Spark Webservice](https://developer.webex.com/getting-started.html), amely Cisco Spark csapat rendelkezésére áll.

## <a name="adding-cisco-spark-from-the-gallery"></a>Cisco Spark hozzáadása a gyűjteményből
Mielőtt konfigurálná a Cisco Spark az Azure ad-vel történő üzembe helyezéséhez automatikus felhasználói, kell hozzáadnia Cisco Spark az Azure AD alkalmazás-galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco Spark az Azure AD alkalmazás-galériából hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]

3. Cisco Spark hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Cisco Spark**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. Az eredmények panelen válassza ki a **Cisco Spark**, majd kattintson a **Hozzáadás** gombra kattintva vegye fel a Cisco Spark a SaaS-alkalmazásokhoz.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Cisco külső felhasználók hozzárendelése

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva.

Mielőtt engedélyezése a felhasználó automatikus kiépítés és konfigurálása, meg kell határoznia, melyik felhasználónak az Azure AD hozzá kell férnie a Cisco Spark. Ha úgy döntött, itt utasításokat követve hozzárendelheti ezek a felhasználók Cisco Spark:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Felhasználók hozzárendelése Cisco Spark fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Cisco Spark tesztfelhasználó az automatikus létesítési konfiguráció. További felhasználók rendelt később.

*   Amikor egy felhasználó rendel a Cisco Spark, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Felhasználólétesítés automatikus Cisco Spark konfigurálása

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók a Cisco Spark az Azure AD-felhasználó hozzárendelések alapján történő konfigurálásáról.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése Cisco Spark az Azure AD-ben:


1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a Cisco Spark a SaaS-alkalmazások listája.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Válassza ki a **kiépítési** fülre.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **bérlői URL-cím**, és **titkos Token** a Cisco Spark-fiók.

    *   Az a **bérlői URL-cím** mezőbe tölthető fel a Cisco Spark SCIM API URL-CÍMÉT a bérlő számára, amely nyilvánul `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, ahol `[Tenant ID]` alfanumerikus karakterlánc van, a 6. lépésben leírtak szerint.

    *   Az a **titkos Token** mezőben, a titkos kulcs Token feltöltése, 6. lépésben leírtak szerint.

1. A **Bérlőazonosító** és **titkos Token** a Cisco Spark fiók is található jelentkezzen be a [Cisco Spark fejlesztői hely](https://developer.webex.com/) rendszergazdai fiókkal. Bejelentkezett egyszer -
    * Lépjen a [első lépések lap](https://developer.webex.com/getting-started.html)
    * Görgessen le a [hitelesítési szakasz](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Spark hitelesítési jogkivonat](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
    * A mezőbe a alfanumerikus karakterlánc a **titkos Token**. Ez a token másolása a vágólapra
    * Lépjen a [beolvasása a saját részleteit megjelenítő oldalra](https://developer.webex.com/endpoint-people-me-get.html)
        * Győződjön meg arról, hogy vizsgálati üzemmód ON
        * Írja be a "Tulajdonos" szót követően egy szóközt szót, majd illessze be a titkos kulcs Token az engedélyezés mező ![Cisco Spark hitelesítési jogkivonat](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
        * Kattintson a Futtatás
    * A válasz szöveg jobb a **Bérlőazonosító** "orgId" jelenik meg:

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. A program kitölti a mezőket az 5. lépés, után kattintson a **kapcsolat tesztelése** biztosításához az Azure AD Cisco Spark kapcsolódhatnak. Ha nem sikerül, győződjön meg arról, a Cisco Spark fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot kell az üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet - **e-mailben értesítést küld, ha hiba lép fel**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználók a Cisco Spark**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Tekintse át a felhasználói attribútumok, Cisco a Spark on Azure ad-lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Cisco Spark a frissítési műveleteket. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../active-directory-saas-scoping-filters.md).

13. Az Azure AD szolgáltatás a Cisco Spark kiépítését engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Válassza ki a kívánt értékeket a Cisco Spark azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/Save.png)


Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a Cisco Spark szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png