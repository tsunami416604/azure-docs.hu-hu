---
title: 'Oktatóanyag: Legfontosabb feladatai közé tartoznak OnDemand konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikus kiépítése és leépíti a legfontosabb feladatai közé tartoznak OnDemand felhasználói fiókok.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8e31400363800f557c6f7c81060c59ac3defb184
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215154"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés legfontosabb feladatai közé tartoznak OnDemand konfigurálása


Ez az oktatóanyag célja az legfontosabb feladatai közé tartoznak OnDemand és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan ellátásához, majd leépíti a felhasználók és/vagy csoportok legfontosabb feladatai közé tartoznak OnDemand végrehajtandó lépések bemutatása.


> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek teljesülését:

*   Az Azure AD-bérlő
*   A legfontosabb feladatai közé tartoznak OnDemand bérlő
*   A legfontosabb feladatai közé tartoznak OnDemand rendszergazdai engedélyekkel rendelkező felhasználói fiók


> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [legfontosabb feladatai közé tartoznak OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), amely legfontosabb feladatai közé tartoznak OnDemand csapat rendelkezésére áll.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Legfontosabb feladatai közé tartoznak OnDemand hozzáadása a gyűjteményből
Mielőtt konfigurálná a legfontosabb feladatai közé tartoznak OnDemand automatikus felhasználói az Azure ad-vel történő üzembe helyezéséhez, kell hozzáadnia OnDemand legfontosabb feladatai közé tartoznak az Azure AD alkalmazás-galériából a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD application gallery legfontosabb feladatai közé tartoznak OnDemand hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Legfontosabb feladatai közé tartoznak OnDemand hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **legfontosabb feladatai közé tartoznak OnDemand**.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/AppSearch.png)

5. Az eredmények panelen válassza ki a **legfontosabb feladatai közé tartoznak OnDemand**, és kattintson a **Hozzáadás** gombra kattintva vegye fel OnDemand legfontosabb feladatai közé tartoznak a SaaS-alkalmazásokhoz.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Felhasználók hozzárendelése legfontosabb feladatai közé tartoznak kötegmérete

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva. 

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, mielőtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok legfontosabb feladatai közé tartoznak OnDemand hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok legfontosabb feladatai közé tartoznak OnDemand itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Felhasználók hozzárendelése legfontosabb feladatai közé tartoznak OnDemand fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve OnDemand legfontosabb feladatai közé tartoznak a kiépítés konfigurációs automatikus felhasználó teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó rendel a legfontosabb feladatai közé tartoznak OnDemand, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Automatikus felhasználók átadására a legfontosabb feladatai közé tartoznak kötegmérete

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók és/vagy a csoportokat az Azure AD-ben a felhasználói és/vagy a csoport-hozzárendelések alapján legfontosabb feladatai közé tartoznak OnDemand-konfigurálásának lépéseit.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése OnDemand legfontosabb feladatai közé tartoznak az Azure AD-ben:


1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a legfontosabb feladatai közé tartoznak OnDemand Szolgáltatottszoftver-alkalmazáshoz a listából.
 
    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/Successcenter2.png)

3. Válassza ki a **kiépítési** fülre.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználóneve**, **rendszergazdai jelszó**, és **tartomány** , a legfontosabb feladatai közé tartoznak OnDemand fiók.

    *   Az a **rendszergazda felhasználóneve** mezőben, a rendszergazda fiók a legfontosabb feladatai közé tartoznak OnDemand tenant tartomány\felhasználónév feltöltéséhez. Példa: contoso\admin.

    *   Az a **rendszergazdai jelszó** mezőbe a jelszót a rendszergazdai felhasználónév megfelelő feltöltéséhez.

    *   Az a **tartomány** mezőben, a webszolgáltatás URL-címe a legfontosabb feladatai közé tartoznak OnDemand bérlő feltöltéséhez. Példa: A szolgáltatás nem található: `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, amely a Contoso tartomány `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Hogyan lehet lekérni a webszolgáltatás URL-címe további információkért lásd: [Itt](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. A program kitölti a mezőket az 5. lépés, után kattintson a **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat OnDemand legfontosabb feladatai közé tartoznak. Ha nem sikerül, győződjön meg arról, a legfontosabb feladatai közé tartoznak OnDemand fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot kell az üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet - **e-mailben értesítést küld, ha hiba lép fel**.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Save** (Mentés) gombra.

9. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználók számára legfontosabb feladatai közé tartoznak OnDemand**.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Tekintse át a felhasználói attribútumok, az Azure AD a legfontosabb feladatai közé tartoznak OnDemand lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat az OnDemand legfontosabb feladatai közé tartoznak a frissítési műveletek. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

12. Az Azure AD szolgáltatás OnDemand legfontosabb feladatai közé tartoznak a kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Válassza ki a kívánt értékeket a legfontosabb feladatai közé tartoznak OnDemand azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Legfontosabb feladatai közé tartoznak OnDemand kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)


Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a legfontosabb feladatai közé tartoznak OnDemand szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).
## <a name="connector-limitations"></a>Összekötő korlátozásai

* A legfontosabb feladatai közé tartoznak OnDemand **pozíció** attribútum egy értéket, amely megfelel a legfontosabb feladatai közé tartoznak OnDemand-portál szerepkörök vár. Érvényes listájának **pozíció** értékek útvonalon érhető el **felhasználói rekord szerkesztése > szervezeti felépítés > pozíció** a legfontosabb feladatai közé tartoznak OnDemand-portálon.
    ![Felhasználó legfontosabb feladatai közé tartoznak OnDemand kiépítés szerkesztése](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![pozíció kiépítés legfontosabb feladatai közé tartoznak OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![legfontosabb feladatai közé tartoznak OnDemand pozíciók lista kiépítése](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)
    
## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
