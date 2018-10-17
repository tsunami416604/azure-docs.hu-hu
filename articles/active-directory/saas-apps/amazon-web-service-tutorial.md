---
title: 'Oktatóanyag: Azure Active Directory-integráció az Amazon Web Services (AWS) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Amazon Web Services (AWS) között.
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
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 8e91fbf0befaef9088e9afaa6e69c0cb29ad4858
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363759"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Oktatóanyag: Azure Active Directory-integráció az Amazon Web Services (AWS)

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Amazon Web Services (AWS) Azure Active Directoryval (Azure AD).

Az Amazon Web Services (AWS) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Amazon Web Services (AWS) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS)?](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Az alábbi, több azonosítók több példány esetén is beállíthatja. 

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Ezekkel az értékekkel, az Azure AD eltávolítja az értékét **#** és a megfelelő értéket küldése `https://signin.aws.amazon.com/saml` a a SAML-jogkivonat célközönség URL-címként.

**Ezt a módszert használja a következő esetekben javasoljuk:**

a. Minden alkalmazás, az egyedi X509 tanúsítvány, és így minden példány egy másik tanúsítvány lejárati dátuma is rendelkezhet, és az egyes AWS fiók alapján kezelheti a biztosít. A teljes tanúsítványváltás lesz könnyen ebben az esetben.

b. Engedélyezheti a felhasználók átadásának az AWS-alkalmazáshoz az Azure ad-ben, és hogy szolgáltatásunkat, majd beolvassa a szerepkörök, az AWS-fiókból. Nem kell manuálisan adja hozzá, vagy frissíteni az AWS-szerepkörök az alkalmazást.

c. Az alkalmazás tulajdonosa egyedileg az alkalmazás számára az alkalmazás közvetlenül az Azure ad-ben kezelésére jogosult rendelhet hozzá.

> [!Note]
> Ellenőrizze, hogy csak a katalógus-alkalmazással

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Amazon Web Services (AWS), a következőkre van szükség:

- Azure AD-előfizetés
- Az Amazon Web Services (AWS) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Ha meg szeretné integrálása több AWS-fiókok egy Azure-fiókba való egyszeri bejelentkezéshez, olvassa el [ez](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) cikk.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az Amazon Web Services (AWS) hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Az Amazon Web Services (AWS) hozzáadása a katalógusból
A-integráció konfigurálása az Amazon Web Services (AWS) Azure AD-be, szüksége a katalógusból az Amazon Web Services (AWS) hozzáadása a felügyelt SaaS-alkalmazások listájában.

**Az Amazon Web Services (AWS) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/amazon-web-service-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/amazon-web-service-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/amazon-web-service-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Amazon Web Services (AWS)**, jelölje be **Amazon Web Services (AWS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Amazon Web Services (AWS) nevű, "Britta Simon" tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó az Amazon Web Services (AWS) mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az Amazon Web Services (AWS) hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Amazon Web Services (AWS) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Amazon Web Services (AWS) tesztfelhasználót](#create-an-amazon-web-services-aws-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon az Amazon Web Services (AWS), amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Amazon Web Services (AWS) alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Amazon Web Services (AWS), hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **Amazon Web Services (AWS)** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/amazon-web-service-tutorial/B1_B2_Select_SSO.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/amazon-web-service-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/amazon-web-service-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Ha több példány konfigurál, adjon meg azonosító értékét. A második példány az újabb verziók esetében adjon meg azonosító értékét a következő formátumban. Használjon egy **#** SPN egyedi értéket adjon meg bejelentkezni. 

    `https://signin.aws.amazon.com/saml#2`

    ![Az Amazon Web Services (AWS) tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Az Amazon Web Services (AWS) alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![image](./media/amazon-web-service-tutorial/i4-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név)  | Adatforrás-attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Adjon meg egy értéket 900 másodperc (15 perc) között 43200 másodperc (12 óra)" |  https://aws.amazon.com/SAML/Attributes |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/amazon-web-service-tutorial/i2-attribute.png)

    ![image](./media/amazon-web-service-tutorial/i3-attribute.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Adja meg a **Namespace** értéket.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  , és mentse a számítógépre.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

9. Egy másik böngészőablakban bejelentkezést az Amazon Web Services (AWS) vállalat webhelye rendszergazdaként.

10. Kattintson a **AWS kezdőlap**.

    ![Kezdőlap egyszeri bejelentkezés konfigurálása][11]

11. Kattintson a **identitás és hozzáférés-kezelés**.

    ![Egyszeri bejelentkezési identitás konfigurálása][12]

12. Kattintson a **Identitásszolgáltatók**, és kattintson a **létrehozása szolgáltató**.

    ![Egyszeri bejelentkezés szolgáltató konfigurálása][13]

13. Az a **konfigurálása szolgáltató** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés párbeszédpanel konfigurálása][14]

    a. Mint **szolgáltatótípus**válassza **SAML**.

    b. Az a **szolgáltatónevet** szövegmezőbe írja be a szolgáltató nevét (például: *fahulladékok*).

    c. A letöltött feltöltéséhez **metaadatait tartalmazó fájl** kattintson az Azure Portalról, **fájl kiválasztása**.

    d. Kattintson a **következő lépés**.

14. Az a **ellenőrizze a szolgáltató adatait** párbeszédpanel lap, kattintson a **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása ellenőrzése][15]

15. Kattintson a **szerepkörök**, és kattintson a **szerepkör létrehozása**.

    ![Egyszeri bejelentkezés szerepkörök konfigurálása][16]

16. Az a **szerepkör létrehozása** lapon, a következő lépésekkel:  

    ![Egyszeri bejelentkezés megbízhatósági kapcsolat beállítása][19]

    a. Válassza ki **SAML 2.0 összevonási** alatt **válassza ki a megbízható entitás típusa**.

    b. A **SAML 2.0-s szolgáltató szakasz választása**, jelölje be a **SAML-szolgáltató** korábban létrehozott (például: *fahulladékok*)

    c. Válassza ki **programozott engedélyezése és az AWS-felügyeleti konzol hozzáférés**.
  
    d. Kattintson a **tovább: engedélyek**.

17. Az a **engedélyházirend csatolása** párbeszédpanelen nem kell minden olyan szabályzat csatolása. Kattintson a **tovább: tekintse át**.  

    ![Egyszeri bejelentkezési szabályzat konfigurálása][33]

18. Az a **felülvizsgálati** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Konfigurálja az egyszeri bejelentkezés áttekintése][34]

    a. Az a **szerepkörnév** szövegmezőbe írja be a szerepkör nevét.

    b. Az a **szerepkör leírása** szövegmezőbe írja be a leírást.

    c. Kattintson a **szerepkör létrehozása**.

    d. Hozzon létre annyi szerepköröket, igény szerint, és megfeleltet az identitásszolgáltató.

19. A szerepkörök beolvasása az AWS-fiók az Azure AD-felhasználó kiépítési használni az AWS szolgáltatásfiók hitelesítő adatai. Ehhez nyissa meg az otthoni AWS konzolon.

20. Kattintson a **szolgáltatások** -> **Security, Identity & Compliance** -> **IAM**.

    ![a szerepkörök beolvasása az AWS-fiókból](./media/amazon-web-service-tutorial/fetchingrole1.png)

21. Válassza ki a **házirendek** lap IAM szakaszában.

    ![a szerepkörök beolvasása az AWS-fiókból](./media/amazon-web-service-tutorial/fetchingrole2.png)

22. Új szabályzat létrehozásához kattintson a **szabályzat létrehozása** az AWS-fiók az Azure AD-felhasználó kiépítési a szerepkörök beolvasása.

    ![Új házirend létrehozása](./media/amazon-web-service-tutorial/fetchingrole3.png)

23. A szerepkörök beolvasása az AWS-fiókok a következő lépések elvégzésével a saját szabályzat létrehozása:

    ![Új házirend létrehozása](./media/amazon-web-service-tutorial/policy1.png)

    a. Az a **"Szabályzat létrehozása"** szakaszban kattintson a **"JSON"** fülre.

    b. A házirend-dokumentum, adja hozzá az alábbi JSON-ban.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. Kattintson a **tekintse át a házirend gomb** érvényesíteni a szabályzatot.

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy5.png)

24. Adja meg a **új szabályzat** a következő lépések végrehajtásával:

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy2.png)

    a. Adja meg a **házirendnév** , **AzureAD_SSOUserRole_Policy**.

    b. Megadhat **leírás** , a szabályzat **Ez a szabályzat lehetővé teszi a szerepkörök beolvasása az AWS-fiókok**.

    c. Kattintson a **"Házirend létrehozása"** gombra.

25. Hozzon létre egy új felhasználói fiók az AWS IAM-Service-ben a következő lépések végrehajtásával:

    a. Kattintson a **felhasználók** navigációs az AWS IAM-konzolon.

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy3.png)

    b. Kattintson a **felhasználó hozzáadása** gombra kattintva hozzon létre egy új felhasználót.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/policy4.png)

    c. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet, **AzureADRoleManager**.

    * A hozzáférés típusa, válassza ki a **programozás alapú hozzáférést** lehetőséget. Ezzel a módszerrel a felhasználó elindíthat az API-k és a szerepkörök beolvasása az AWS-fiókból.

    * Kattintson a **tovább engedélyek** gombra a jobb alsó sarokban.

26. Most hozzon létre egy új szabályzatot a felhasználó a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser2.png)

    a. Kattintson a **a meglévő szabályzatok közvetlen csatolása** gombra.

    b. Keresse meg az újonnan létrehozott szabályzat szakaszban **AzureAD_SSOUserRole_Policy**.

    c. Válassza ki a **házirend** majd kattintson a a **tovább: tekintse át** gombra.

27. Tekintse át a szabályzatot a csatolt felhasználóhoz a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználónevet, a hozzáférés típusa, és a szabályzat hozzárendelve a felhasználóhoz.

    b. Kattintson a **felhasználó létrehozása** gombra, és hozza létre a felhasználót a jobb alsó sarokban.

28. Töltse le a felhasználói hitelesítő adatait, a felhasználó a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja ki a felhasználó **hozzáférési kulcs azonosítója** és **titkos hívóbetűje**.

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó kiépítésére szakasz a szerepkörök beolvasása az AWS-konzolról.

    c. Kattintson a **Bezárás** gombra a lap alján.

29. Navigáljon a **Felhasználókiépítés** Amazon Web Services-alkalmazás az Azure AD felügyeleti portál szakaszában.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning.png)

30. Adja meg a **hozzáférési kulcs** és **titkos** a a **titkos Ügyfélkód** és **titkos jogkivonat** mező jelölik.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Adja meg az AWS felhasználói hozzáférési kulcsot a a **clientsecret** mező.

    b. Adja meg az AWS – felhasználói titkos a **titkos jogkivonat** mező.

    c. Kattintson a **kapcsolat tesztelése** gombra kattintva kell sikeresen tesztelheti ezt a kapcsolatot.

    d. A beállítás mentéséhez kattintson a a **mentése** gombra az oldal tetején.

31. Most győződjön meg arról, hogy engedélyezze-e az üzembe helyezési állapotra **a** a kapcsoló meg és kattintson a beállítások szakaszában a **mentése** gombra az oldal tetején.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/amazon-web-service-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/amazon-web-service-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/amazon-web-service-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Az Amazon Web Services (AWS) tesztfelhasználó létrehozása

Ez a szakasz célja egy Britta Simon az Amazon Web Services (AWS) nevű felhasználó létrehozásához. Az Amazon Web Services (AWS) nem kell a felhasználót, hogy hozhatók létre a rendszer flexibilitását egyszeri Bejelentkezést, így nem szükséges itt bármely művelet elvégzésére.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az Amazon Web Services (AWS) Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/amazon-web-service-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Amazon Web Services (AWS)**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/amazon-web-service-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/amazon-web-service-tutorial/d_assign_user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_users.png)

6. A a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson a **válassza** gombra a képernyő alján.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_roles.png)

    >[!NOTE]
    >Miután engedélyezte a felhasználó az alkalmazás-mel, kell várnia kell 30 percig megszerezni a szerepkörök az Amazon Web Services (AWS) akkor szükséges, frissítse az oldalt, és majd a felhasználók és csoportok az megjelenik a szerepkörök használatára az alkalmazás hozzárendelése során r.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_assign.png)
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Amazon Web Services (AWS) csempére kattint, akkor kell lekérése automatikusan bejelentkezett az Amazon Web Services (AWS) alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
