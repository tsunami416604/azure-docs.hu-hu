---
title: "Oktatóanyag: Azure Active Directory-integráció Amazon Web Services (AWS) |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Amazon Web Services (AWS) között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Oktatóanyag: Azure Active Directory-integráció Amazon Web Services (AWS)

Ebben az oktatóanyagban elsajátíthatja Amazon Web Services (AWS) integrálása az Azure Active Directory (Azure AD).

Amazon Web Services (AWS) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírni Amazon Web Services (AWS) az Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Amazon Web Services (AWS), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az Amazon Web Services (AWS) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Amazon Web Services (AWS) hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Adja hozzá az Amazon Web Services (AWS) a gyűjteményből
Az Azure AD-be az Amazon Web Services (AWS)-integráció konfigurálása szüksége Amazon Web Services (AWS) hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből Amazon Web Services (AWS) hozzáadásához tegye a következőket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs ablaktábláján válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Amazon Web Services (AWS)**. Válassza ki **Amazon Web Services (AWS)** az eredmények panelen, és válassza a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Amazon Web Services (AWS) az eredménylistában](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó Amazon Web Services (AWS) egy felhasználó számára az Azure AD. Más szóval kell Amazon Web Services (AWS) az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

Kapcsolatot szeretne létrehozni a, Amazon Web Services (AWS), adja meg az érték **felhasználónév** azonos érték, mint a **felhasználónév** az Azure ad-ben. 

Az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Az Amazon Web Services (AWS) tesztfelhasználó létrehozása](#create-an-amazon-web-services-aws-test-user) kell rendelkeznie a Britta Simon megfelelője az Amazon Web Services (AWS), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Amazon Web Services (AWS) alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása az Amazon Web Services (AWS), a következő lépéseket:**

1. Az Azure portálon a a **Amazon Web Services (AWS)** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés** párbeszédpanel a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Az a **Amazon Web Services (AWS) tartományhoz és URL-címek** szakaszban, a felhasználó nem rendelkezik bármely lépésekre, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![Az egyszeri bejelentkezés információk amazon Web Services (AWS) tartományhoz és URL-címek](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Az Amazon Web Services (AWS) alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakaszban az alkalmazás-integráció lapján. Az alábbi képernyőfelvételen szereplő példán látható:

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. Az a **felhasználói attribútumok** szakasz a **egyszeri bejelentkezés** konfigurálja az SAML-jogkivonat attribútum az előző ábrán látható módon, és ezután a következő lépéseket:
    
    | Attribútum neve  | Attribútum-érték | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Konfigurálja a felhasználók átadása a szerepkörök beolvasása az Amazon Web Services (AWS) konzol a Azure AD-ben. Tekintse át az alábbi üzembe helyezési lépéseket.

    a. Lehetőségre a **attribútum hozzáadása** párbeszédpanelen jelölje ki **Hozzáadás attribútum**.

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** mezőbe írja be az adott sorhoz feltüntetett attribútumot nevet.

    c. Az a **érték** kilistázásához írja be az adott sorhoz feltüntetett attribútumot érték.

    d. Az a **Namespace** mezőbe írja be a névtér értéke, amely az adott sorhoz feltüntetett.
    
    d. Válassza ki **Ok**.

6. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés mentési gomb konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban jelentkezzen be az Amazon Web Services (AWS) vállalati hely rendszergazdaként.

9. Válassza ki **konzol otthoni**.
   
    ![Egyszeri bejelentkezés otthoni konfigurálása][11]

10. Válassza ki **identitás és hozzáférés-kezelés**. 
   
    ![Az egyszeri bejelentkezési identitás][12]

11. Válassza ki **identitás-szolgáltatóktól**. Válassza ki **létrehozása szolgáltató**. 
   
    ![Egyszeri bejelentkezés szolgáltató konfigurálása][13]

12. Az a **szolgáltató konfigurálása** párbeszédpanel mezőben a következő lépéseket: 
   
    ![Egyszeri bejelentkezési-n párbeszédpanel konfigurálása][14]
 
    a. A **szolgáltatótípust**, jelölje be **SAML**.

    b. Az a **szolgáltatónevet** mezőbe írja be a szolgáltató nevét (például: *fahulladékok*).

    c. Töltse fel a letöltött **metaadatfájl** válassza ki az Azure-portálon **Choose File**.

    d. Válassza ki **következő lépés**.

13. Az a **ellenőrizze a szolgáltató adatait** párbeszédpanelen jelölje ki **létrehozása**. 
    
    ![Egyszeri bejelentkezés ellenőrzése konfigurálása][15]

14. Válassza ki **szerepkörök**. Válassza ki **hozzon létre új szerepkör**. 
    
    ![Egyszeri bejelentkezés szerepkörök konfigurálása][16]

15. Az a **szerepkörnév beállítása** párbeszédpanel mezőben a következő lépéseket: 
    
    ![Egyszeri bejelentkezés nevének beállítása][17] 

    a. Az a **szerepkörnév** mezőbe írja be a szerepkör nevét (például *tesztfelhasználó néven*). 

    b. Válassza ki **következő lépés**.

16. Az a **szerepkör típusának kiválasztása** párbeszédpanel mezőben a következő lépéseket: 
    
    ![Egyszeri bejelentkezés felhasználóiszerepkör-típus konfigurálása][18] 

    a. Válassza ki **Identity Provider hozzáférési szerepkör**. 

    b. Az a **SAML-szolgáltatók Grant webes egyszeri bejelentkezés (WebSSO) hozzáférést** területen kattintson **válasszon**.

17. Az a **létre megbízható** párbeszédpanel mezőben a következő lépéseket:  
    
    ![Egyszeri bejelentkezés megbízhatósági kapcsolat beállítása][19] 

    a. Válassza ki a korábban létrehozott SAML-szolgáltató (például: *fahulladékok*). 
  
    b. Válassza ki **következő lépés**.

18. Az a **szerepkör megbízható ellenőrizze** párbeszédpanelen jelölje ki **következő lépésre**. 
    
    ![Egyszeri bejelentkezés szerepkört megbízhatósági kapcsolat beállítása][32]

19. Az a **csatolása házirend** párbeszédpanelen jelölje ki **tovább**.  
    
    ![Egyszeri bejelentkezés házirend konfigurálása][33]

20. Az a **felülvizsgálati** párbeszédpanel mezőben a következő lépéseket:   
    
    ![Egyszeri bejelentkezés felülvizsgálati konfigurálása][34] 

    a. Válassza ki **szerepkör létrehozása**.

    b. Szükség szerint annyi szerepköröket hozhat létre, és képezze az identitásszolgáltató.

21. Amazon Web Services (AWS) szolgáltatásfiók hitelesítő adatainak használata a szerepkörök beolvasása az Azure AD a felhasználók átadása az Amazon Web Services (AWS) fiókból. Ez a feladat elindítása, nyissa meg az otthoni Amazon Web Services (AWS) konzolt.

22. Válassza ki **szolgáltatások** > **biztonsági, identitás- & megfelelőségi** > **IAM**.

    ![A szerepkörök beolvasása a Amazon Web Services (AWS) fiók](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. A IAM területen válassza ki a **házirendek** fülre.

    ![A szerepkörök beolvasása a Amazon Web Services (AWS) fiók](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Hozzon létre egy új házirendet, válassza ki a **házirend létrehozása**.

    ![Új házirend létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. A szerepkörök beolvasása Amazon Web Services (AWS) fiókok a saját házirend létrehozásához a következő lépéseket:

    ![Új házirend létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. Az a **házirend létrehozása** szakaszban jelölje be a **JSON** fülre.

    b. A házirend-dokumentum adja hozzá a következő JSON:
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Érvényesíteni a házirendet, válassza ki a **felülvizsgálati házirend gomb**.

    ![Az új szabályzat definiálása](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Adja meg a **új házirend** az alábbi lépések megtételével:

    ![Az új szabályzat definiálása](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Adja meg a **házirendnév** , **AzureAD_SSOUserRole_Policy**.

    b. Megadhatja a következő **leírás** a házirend: **ezzel a házirend lehetővé teszi a szerepkörök beolvasása a AWS fiókok**.
    
    c. Válassza ki a **házirend létrehozása** gombra.
        
27. Egy új felhasználói fiók létrehozása az Amazon Web Services (AWS) IAM-szolgáltatásban, a következő lépéseket:

    a. Válassza ki **felhasználók** az Amazon Web Services (AWS) IAM-konzolon.

    ![Az új szabályzat definiálása](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b.To hozzon létre egy új felhasználót, jelölje be a **felhasználó hozzáadása** gombra.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Az a **felhasználó hozzáadása** területen tegye a következőket:
    
    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Adja meg **AzureADRoleManager** a felhasználó neve mezőben.
    
    * A hozzáférés típusa, válassza a **programozott hozzáférés** lehetőséget. Ily módon a felhasználó elindíthat a API-k és a szerepkörök beolvasása az Amazon Web Services (AWS) fiókból.
    
    * Válassza ki a **tovább engedélyeket** jobb alsó sarokban lévő gombjára.

28. Hozzon létre egy új házirendet a felhasználó az alábbi lépések megtételével:

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Válassza ki a **közvetlenül csatolni a meglévő házirendek** gombra.

    b. Keressen rá az újonnan létrehozott házirend szűrő szakaszában **AzureAD_SSOUserRole_Policy**.
    
    c. Válassza ki a **házirend**. Válassza ki a **tovább: felülvizsgálati** gombra.

29. Az alábbi lépések megtételével tekintse át a csatlakoztatott felhasználóra vonatkozó szabályzattal:

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Tekintse át a felhasználónevet, a hozzáférés típusa és a házirendet, amely a felhasználó van leképezve.
    
    b. A felhasználó létrehozásához válassza a **a felhasználó létrehozása** a jobb alsó a felhasználó létrehozása gomb.

30. Töltse le a felhasználó hitelesítő adatait az alábbi lépések megtételével:

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Másolja a felhasználói **hozzáférési kulcs azonosítója** és **titkos hívóbetű**.
    
    b. Adjon meg ezeket a hitelesítő adatokat az Azure AD-felhasználó szakasz kiépítése a szerepkörök beolvasása az Amazon Web Services (AWS) konzolról.
    
    c. Válassza ki a **Bezárás** jobb alsó sarokban lévő gombjára.

31. Keresse meg a **Felhasználólétesítés** az Amazon Web Services (AWS) alkalmazás az Azure AD felügyeleti portál szakaszban.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Adja meg a **hozzáférési kulcs** és **titkos** a a **Ügyfélkulcs** és **titkos Token** rendre mezőben.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Adja meg az Amazon Web Services (AWS) felhasználói hozzáférési kulcsot a a **clientsecret** mező.
    
    b. Írja be az Amazon Web Services (AWS) felhasználói titkos kulcsot a **titkos Token** mező.
    
    c. Válassza ki a **kapcsolat tesztelése** gombra. Meg kell tudni sikeresen tesztelje a kapcsolatot.

    d. A beállítás kiválasztásával mentése a **mentése** gombra az oldal tetején.
 
33. Győződjön meg arról, hogy kapcsolja a kiépítési állapotát **a** a **beállítások**. Ehhez kiválasztásával **a**, és jelölje be a **mentése** gombra az oldal tetején.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** fülre. Majd hozzáférni a beágyazott dokumentáció keresztül a **konfigurációs** szakasz alján. További, a beágyazott dokumentáció szolgáltatásról [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Az Amazon Web Services (AWS) tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Amazon Web Services (AWS) nevű felhasználót létrehozni. Amazon Web Services (AWS) nem kell a felhasználót, hogy a rendszer hozhatók létre single-sign-on, így nem kell itt bármely művelet elvégzésére.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó Amazon Web Services (AWS) való hozzáférés biztosítása őket.

![A felhasználói szerepkör hozzárendelése][200] 

**Az Amazon Web Services (AWS) Britta Simon hozzárendeléséhez a következő lépéseket:**

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. Ezután nyissa meg a könyvtár nézet, és válassza ki **vállalati alkalmazások**. Válassza ki, **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Amazon Web Services (Amazon Web Services (AWS)**.

    ![Az Amazon Web Services (AWS) hivatkozásra az alkalmazások listáját](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra. 

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az Amazon Web Services (AWS) csempe a hozzáférési panelen válassza ki, amikor meg kell beolvasni automatikusan bejelentkezve az Amazon Web Services (AWS) alkalmazáshoz. A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

