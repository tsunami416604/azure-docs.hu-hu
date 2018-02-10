---
title: "Oktatóanyag: Google alkalmazások konfigurálása az Azure Active Directoryval történő üzembe helyezéséhez automatikus felhasználói |} Microsoft Docs"
description: "Megtudhatja, hogyan automatikusan ellátásához, majd leépíti a felhasználói fiókok Azure ad-Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: a90fe2c919e0c5452072c4129dcb7a1135d9ec25
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>Oktatóanyag: A Google Apps konfigurálása automatikus a felhasználók átadása

A jelen oktatóanyag célja bemutatja, hogyan tudja automatikusan telepíteni és leépíti a Google Apps Azure Active Directory (Azure AD) lévő felhasználói fiókok.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban a feltételezzük, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Google Apps munka vagy a Google Apps oktatási célokra egy érvényes bérlőt. Egy ingyenes próbafiók vagy szolgáltatáshoz is használhatja.
*   Egy felhasználói fiókot a Google Apps Team rendszergazdai engedélyekkel.

## <a name="assign-users-to-google-apps"></a>Google Apps felhasználók hozzárendelése

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok számára "rendelt" az Azure AD alkalmazás szinkronizálva.

Beállítása, és a létesítési szolgáltatás engedélyezése előtt kell döntse el, hogy mely felhasználók vagy csoportok az Azure ad-ben a Google Apps alkalmazásba hozzáférésre van szükségük. Ez a döntés elkészítése után rendelhet ezek a felhasználók a Google Apps alkalmazásba utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Javasoljuk, hogy egyetlen Azure AD-felhasználó hozzárendelni Google Apps teszteli a telepítési konfigurációt. További felhasználók és csoportok később is rendelhető.

> Ha egy felhasználó rendel Google Apps, válassza ki a **felhasználói** vagy **csoport** szerepkör-hozzárendelés párbeszédpanel. A **alapértelmezett hozzáférési** szerepkör kialakítási nem működik.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódni a felhasználói fiók API Google Apps kiépítési folyamat. Emellett segít a létesítési szolgáltatás létrehozása, frissítése és tiltsa le a hozzárendelt felhasználói fiókok a Google Apps, a felhasználó és az Azure AD-csoport-hozzárendelés alapján konfigurálhatja.

>[!TIP]
>Előfordulhat, hogy meg utasításait követve SAML-alapú egyszeri bejelentkezés Google Apps, engedélyezze a [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurálja az automatikus felhasználói fiók kiépítése

> [!NOTE]
> Egy másik kivitelezhető lehetőség, a felhasználók a Google Apps átadása automatizálásához [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). A helyszíni Active Directory identitások Google Apps GADS látja el. Ezzel ellentétben ebben az oktatóanyagban a megoldás látja el az Azure Active Directoryban (felhő) felhasználók és a levelezési csoportokat a Google Apps. 

1. Jelentkezzen be a [Google Apps felügyeleti konzol](http://admin.google.com/) a rendszergazdai fiókot, és válassza a **biztonsági**. Ha nem látja a hivatkozásra, akkor előfordulhat, hogy rejtve alatt a **további vezérlők** menü a képernyő alján.
   
    ![Válassza ki a biztonsági.][10]

2. Az a **biztonsági** lapon jelölje be **API-referencia**.
   
    ![Válassza ki az API-hivatkozás.][15]

3. Válassza ki **engedélyezése API-hozzáférés**.
   
    ![Válassza ki az API-hivatkozás.][16]

    > [!IMPORTANT]
    > Minden felhasználó esetén, melyet szeretne kiépíteni a Google Apps, a felhasználónév az Azure Active Directoryban *kell* egyéni tartományt összekapcsolását. Például a felhasználó nevét, hogy keresse meg például a bob@contoso.onmicrosoft.com Google Apps használata nem engedélyezett. Másrészről bob@contoso.com el van fogadva. A tulajdonságok módosítása az Azure AD egy meglévő felhasználó tartományi módosíthatja. Jelenleg szerepel az egyéni tartománynév beállítása az Azure Active Directory és a Google Apps a következő lépésekben vonatkozó utasításokat.
      
4. Ha nem adott meg egy egyéni tartománynevet az Azure Active Directory még, majd tegye a következőket:
  
    a. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs panelen válassza ki a **Active Directory**. A könyvtár listában válassza ki a címtárát. 

    b. Válassza ki **tartománynév** a bal oldali navigációs panelen, és válassza a **Hozzáadás**.
     
     ![Tartomány](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![tartomány hozzáadása](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Írja be a tartomány nevét a **tartománynév** mező. Ezt a tartománynevet szeretne használni a Google Apps tartományi megegyező nevet kell lennie. Válassza ki a **tartomány hozzáadása** gombra.
     
     ![Tartománynév](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Válassza ki **következő** az ellenőrzési lap megnyitásához. Győződjön meg arról, hogy Ön a tulajdonosa ezt a tartományt, szerkessze a tartomány DNS-rekordok az ezen a lapon lévő értékek alapján. Választhat, hogy ellenőrizze az **MX-rekordok** vagy **TXT rekord**mi ki az attól függően, hogy a **rekordtípus** lehetőséget. 
    
    Hogyan lehet ellenőrizni az Azure AD-tartománynevek átfogóbb utasításokért lásd: [saját tartománynév hozzáadása az Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Tartomány](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Ismételje meg az előző lépéseket a könyvtárhoz hozzáadni kívánt összes tartományát.

    > [!NOTE]
    A felhasználók átadásához a Google Apps egyéni tartománynak meg kell egyeznie a forrás az Azure AD tartománynevét. Ha nem egyeznek, esetleg attribútum leképezési testreszabás megoldani a problémát.


5. Most, hogy az Azure ad-vel a tartományok ellenőrzését, ellenőriznie kell őket újra a Google Apps. Minden tartományhoz, amely már nincs regisztrálva a Google Apps tegye a következőket:
   
    a. Az a [Google Apps felügyeleti konzol](http://admin.google.com/), jelölje be **tartományok**.
     
     ![Select Domains][20]

    b. Válassza ki **hozzáadása egy tartományhoz vagy egy tartományi alias**.
     
     ![Új tartomány hozzáadása][21]

    c. Válassza ki **egy másik tartomány hozzáadása**, majd írja be a nevében a tartományt, amelyikhez hozzá szeretné adni.
     
     ![Type in your domain name][22]

    d. Válassza ki **folytatja, és ellenőrizze a tartomány tulajdonosa**. Kövesse a lépéseket, hogy Ön a tulajdonosa a tartománynév ellenőrzése. Ellenőrizze a tartományt a Google Apps átfogó útmutatást lásd: [ellenőrizze a hely tulajdonjoga, a Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Ismételje meg a fenti lépéseket minden olyan további tartományt, amelyet lemezképfájlforrásként kíván hozzáadni a Google Apps.
     
     > [!WARNING]
     > Ha az elsődleges tartomány módosítja a Google Apps bérlő számára, és ha már konfigurált az egyszeri bejelentkezés az Azure AD, majd ismételje meg a #3 alapján, hogy [2. lépés: az egyszeri bejelentkezés engedélyezése](#step-two-enable-single-sign-on).
       
6. Az a [Google Apps felügyeleti konzol](http://admin.google.com/), jelölje be **rendszergazdai szerepkörök**.
   
     ![Select Google Apps][26]

7. Határozza meg, melyik kezelheti a felhasználók átadása használandó rendszergazdai fiókot. Az a **rendszergazdai szerepkörrel** -fiók, szerkesztheti a **jogosultságokkal** adott szerepkörhöz. Ügyeljen arra, hogy az összes **rendszergazda API jogosultságokkal** , hogy ez a fiók kiépítése is használható.
   
     ![Select Google Apps][27]
   
    > [!NOTE]
    > Ha konfigurál egy éles környezetben, az ajánlott eljárás, ha egy rendszergazdai fiók a Google Apps kifejezetten a ezt a lépést. Ezek a fiókok rendszergazda szerepkörrel hozzájuk társított, amely rendelkezik a szükséges API-jogosultságokkal kell rendelkeznie.
     
8. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** szakasz.

9. Ha már beállította az egyszeri bejelentkezés Google Apps, keresse meg a Google Apps példánya a keresési mező használatával. Máskülönben válassza **Hozzáadás**, majd keresse meg a **Google Apps** az alkalmazás katalógusában. Válassza ki **Google Apps** a keresési eredmények közül, majd hozzá az alkalmazások listáját.

10. A Google Apps példányát, majd válassza ki és a **kiépítési** fülre.

11. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

     ![Kiépítés](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Az a **rendszergazdai hitelesítő adataival** szakaszban jelölje be **engedélyezés**. A Google Apps engedélyezési párbeszédpanel egy új böngészőablakban nyílik meg.

13. Győződjön meg arról, hogy szeretné-e hajtani a Google Apps-bérlő Azure Active Directory engedélyt. Válassza ki **elfogadása**.
    
     ![Ellenőrizze az engedélyeket.][28]

14. Válassza ki az Azure-portálon **kapcsolat tesztelése** annak érdekében, hogy az Azure AD csatlakozhat a Google Apps alkalmazást. Ha nem sikerül, győződjön meg arról, hogy a Google Apps-fiók Team rendszergazdai jogosultságokkal rendelkezik. Ismételje meg a **engedélyezés** léptessen ismét.

15. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mező. Ezután jelölje be a jelölőnégyzetet.

16. Válassza ki **mentéséhez.**

17. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználók a Google Apps**.

18. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumokat a Google Apps szinkronizált Azure AD-ből. Az attribútumokat, amelyek **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Google Apps a frissítési műveleteket. Válassza ki **mentése** véglegesíteni a módosításokat.

19. Google Apps szolgáltatás kiépítését az Azure AD engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a **beállítások**.

20. Kattintson a **Mentés** gombra.

A kiszolgáló megkezdi a felhasználók és csoportok, a felhasználók és csoportok szakaszban Google Apps rendelt kezdeti szinkronizálása. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, miközben fut a szolgáltatás-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve a tevékenységi naplóit kiépítésére. Ezek a naplók a Google Apps-alkalmazások létesítési szolgáltatás által elvégzett összes műveleteket írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
