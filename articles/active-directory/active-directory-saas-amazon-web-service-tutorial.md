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
ms.date: 1/3/2017
ms.author: jeedes
ms.openlocfilehash: b4d96df72fd7f8f817140e7599e22a63ddd79910
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Oktatóanyag: Azure Active Directory-integráció Amazon Web Services (AWS)

Ebben az oktatóanyagban elsajátíthatja Amazon Web Services (AWS) integrálása az Azure Active Directory (Azure AD).

Amazon Web Services (AWS) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az Amazon Web Services (AWS) (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Amazon Web Services (AWS), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az Amazon Web Services (AWS) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Amazon Web Services (AWS) hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) hozzáadása a gyűjteményből
Az Azure AD-be az Amazon Web Services (AWS)-integráció konfigurálása szüksége Amazon Web Services (AWS) hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Adja hozzá az Amazon Web Services (AWS) a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Amazon Web Services (AWS)**, jelölje be **Amazon Web Services (AWS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Amazon Web Services (AWS) az eredménylistában](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Amazon Web Services (AWS) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói Amazon Web Services (AWS) közötti kapcsolat kapcsolatot kell létrehozni.

Az Amazon Web Services (AWS), rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Az Amazon Web Services (AWS) tesztfelhasználó létrehozása](#create-an-amazon-web-services-aws-test-user)**  - kell rendelkeznie a Britta Simon megfelelője az Amazon Web Services (AWS), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Amazon Web Services (AWS) alkalmazásban.

**Az Amazon Web Services (AWS) konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Amazon Web Services (AWS)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Az a **Amazon Web Services (AWS) tartományhoz és URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához.

    ![Az egyszeri bejelentkezés információk amazon Web Services (AWS) tartományhoz és URL-címek](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Az Amazon Web Services (AWS) alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.

    ![Egyszeri bejelentkezés attb konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve  | Attribútum értéke | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://AWS.amazon.com/SAML/Attributes |
    | Szerepkör            | User.assignedroles |  https://AWS.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Szeretne beállítani, hogy a felhasználók átadása a szerepkörök beolvasása a AWS konzol az Azure AD-ben. Tekintse meg az alábbi üzembe helyezési lépéseket.

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés addattb konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban bejelentkezés az Amazon Web Services (AWS) vállalati helyre rendszergazdaként.

9. Kattintson a **konzol otthoni**.
   
    ![Egyszeri bejelentkezés otthoni konfigurálása][11]

10. Kattintson a **identitás és hozzáférés-kezelés**. 
   
    ![Az egyszeri bejelentkezési identitás][12]

11. Kattintson a **identitás-szolgáltatóktól**, és kattintson a **létrehozása szolgáltató**. 
   
    ![Egyszeri bejelentkezés szolgáltató konfigurálása][13]

12. Az a **szolgáltató konfigurálása** párbeszédpanel lapon, a következő lépésekkel: 
   
    ![Egyszeri bejelentkezés párbeszédpanel konfigurálása][14]
 
    a. Mint **szolgáltatótípust**, jelölje be **SAML**.

    b. Az a **szolgáltatónevet** szövegmező, írja be a szolgáltató nevét (például: *fahulladékok*).

    c. Töltse fel a letöltött **metaadatfájl** Azure-portálon, kattintson a **Choose File**.

    d. Kattintson a **következő lépés**.

13. Az a **ellenőrizze a szolgáltató adatait** párbeszédpanel lap, kattintson a **létrehozása**. 
    
    ![Egyszeri bejelentkezés konfigurálása ellenőrzése][15]

14. Kattintson a **szerepkörök**, és kattintson a **hozzon létre új szerepkör**. 
    
    ![Egyszeri bejelentkezés szerepkörök konfigurálása][16]

15. Az a **szerepkörnév beállítása** párbeszédpanelen hajtsa végre a következő lépéseket: 
    
    ![Egyszeri bejelentkezés nevének beállítása][17] 

    a. Az a **szerepkörnév** szövegmező, írja be a szerepkör nevét (például: *tesztfelhasználó néven*). 

    b. Kattintson a **következő lépés**.

16. Az a **szerepkör típusának kiválasztása** párbeszédpanelen hajtsa végre a következő lépéseket: 
    
    ![Egyszeri bejelentkezés felhasználóiszerepkör-típus konfigurálása][18] 

    a. Válassza ki **Identity Provider hozzáférési szerepkör**. 

    b. Az a **SAML-szolgáltatók Grant webes egyszeri bejelentkezés (WebSSO) hozzáférést** területen kattintson **válasszon**.

17. Az a **létre megbízható** párbeszédpanelen hajtsa végre a következő lépéseket:  
    
    ![Egyszeri bejelentkezés megbízhatósági kapcsolat beállítása][19] 

    a. SAML-szolgáltatóként, válassza ki a korábban létrehozott SAML-szolgáltató (például: *fahulladékok*) 
  
    b. Kattintson a **következő lépés**.

18. A a **szerepkör megbízható ellenőrizze** párbeszédpanel, kattintson a **tovább**. 
    
    ![Egyszeri bejelentkezés szerepkört megbízhatósági kapcsolat beállítása][32]

19. Az a **csatolása házirend** párbeszédpanel, kattintson a **tovább**.  
    
    ![Egyszeri bejelentkezés házirend konfigurálása][33]

20. Az a **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:   
    
    ![Egyszeri bejelentkezés felülvizsgálati konfigurálása][34] 

    a. Kattintson a **szerepkör létrehozása**.

    b. Igény szerint annyi szerepköröket hozhat létre, és az identitásszolgáltató való hozzárendelése.

21. A szerepkörök beolvasása a Azure AD-felhasználó kiépítési AWS-fiókot használni AWS szolgáltatásfiók hitelesítő adatai. Ehhez nyissa meg a kezdőkönyvtár AWS konzol.

22. Kattintson a **szolgáltatások** -> **biztonsági, identitás- & megfelelőségi** -> **IAM**.

    ![a szerepkörök beolvasása AWS-fiókból](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Válassza ki a **házirendek** lap IAM szakaszában.

    ![a szerepkörök beolvasása AWS-fiókból](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Hozzon létre egy új házirendet kattintva **házirend létrehozása**.

    ![Új házirend létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. A szerepkörök beolvasása AWS fiókokat a saját házirend létrehozása. Az a **létrehozása a saját házirendjének** szakaszban kattintson a **válasszon** gombra.
    
    ![Új házirend létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

26. Adja meg az új szabályzat a következő lépések elvégzésével:

    ![Az új szabályzat definiálása](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Adja meg a **házirendnév** , **AzureAD_SSOUserRole_Policy**.

    b. Megadhatja a **leírás** , a házirendhez **ezzel a házirend lehetővé teszi a szerepkörök beolvasása a AWS fiókok**.
    
    c. A házirend-dokumentum, adja hozzá az alábbi JSON.
    
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
    
    d. Győződjön meg arról, hogy ellenőrizze a **használható formázáskor házirend szerkesztésre**.
    
    e. Kattintson a **házirend ellenőrzése** panel alján.
    
    f. Ha a házirend az ellenőrzött megfelelően ezt követően kattinthat a **házirend létrehozása** gombra.
    
27. Hozzon létre egy új felhasználói fiókot az AWS IAM-szolgáltatás a következő lépések elvégzésével:

    a. Kattintson a **felhasználók** navigációs az AWS IAM-konzolon.

    ![Az új szabályzat definiálása](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Kattintson a **felhasználó hozzáadása** új felhasználó létrehozása gomb.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Az a **felhasználó hozzáadása** területen tegye a következőket:
    
    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Adja meg a felhasználónevet, **AzureADRoleManager**.
    
    * A hozzáférés típusa, válassza ki a **programozott hozzáférés** lehetőséget. Ily módon a felhasználó elindíthat a API-k és a szerepkörök beolvasása a AWS fiók.
    
    * Kattintson a **tovább engedélyeket** gombra a jobb alsó sarokban.

28. Most hozzon létre egy új házirendet a felhasználó az alábbi lépések elvégzésével:

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Kattintson a **közvetlenül csatolni a meglévő házirendek** gombra.

    b. Keressen rá az újonnan létrehozott házirend szűrő szakaszában **AzureAD_SSOUserRole_Policy**.
    
    c. Válassza ki a **házirend** majd kattintson a a **tovább: felülvizsgálati** gombra.

29. Tekintse át a szabályzatot, a csatlakoztatott felhasználói lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Tekintse át a felhasználónevet, a hozzáférés típusa és a hozzárendelve a felhasználóhoz házirend.
    
    b. Kattintson a **a felhasználó létrehozása** alsó a felhasználó létrehozása gomb.

30. Töltse le a felhasználói hitelesítő adatokat egy olyan felhasználó lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Másolja a felhasználói **hozzáférési kulcs azonosítója** és **titkos hívóbetű**.
    
    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó szakasz kiépítése a szerepkörök beolvasása a AWS konzol.
    
    c. Kattintson a **Bezárás** panel alján.

31. Navigáljon a **Felhasználólétesítés** Amazon Web Services-alkalmazás az Azure AD felügyeleti portál szakaszban.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Adja meg a **hozzáférési kulcs** és **titkos** a a **Ügyfélkulcs** és **titkos Token** rendre mezőben.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Adja meg az AWS felhasználói hozzáférési kulcsot a a **clientsecret** mező.
    
    b. Írja be az AWS felhasználói titkos kulcsot a **titkos Token** mező.
    
    c. Kattintson a **kapcsolat tesztelése** gombra kattintva kell sikeresen tesztelni az ehhez a kapcsolathoz.

    d. A beállítás mentése, kattintson a **mentése** gombra az oldal tetején.
 
33. Ellenőrizze, hogy, hogy engedélyezze a kiépítési állapot **a** hajtsanak végre a kapcsolót a, és kattintson a beállítások szakaszában a **mentése** gombra az oldal tetején.

    ![Felhasználó hozzáadása](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Az Amazon Web Services (AWS) tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Amazon Web Services (AWS) nevű felhasználót létrehozni. Amazon Web Services (AWS) nem kell a felhasználót, hogy hozható létre a rendszer az egyszeri bejelentkezés, így nem kell itt bármely művelet elvégzésére.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférés biztosítása az Amazon Web Services (AWS) által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Az Amazon Web Services (AWS) Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Amazon Web Services (AWS)**.

    ![Az Amazon Web Services (AWS) hivatkozásra az alkalmazások listáját](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Amazon Web Services (AWS) csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Amazon Web Services (AWS) alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

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

