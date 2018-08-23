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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: jeedes
ms.openlocfilehash: 8ddcd66d0675603f4e130a9ca367cc4eed7353e7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42056673"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Oktatóanyag: Azure Active Directory-integráció az Amazon Web Services (AWS)

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Amazon Web Services (AWS) Azure Active Directoryval (Azure AD).

Az Amazon Web Services (AWS) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Amazon Web Services (AWS) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS)?](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_image.png)

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

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az Amazon Web Services (AWS) hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Az Amazon Web Services (AWS) hozzáadása a katalógusból
A-integráció konfigurálása az Amazon Web Services (AWS) Azure AD-be, szüksége a katalógusból az Amazon Web Services (AWS) hozzáadása a felügyelt SaaS-alkalmazások listájában.

**Az Amazon Web Services (AWS) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Amazon Web Services (AWS)**, jelölje be **Amazon Web Services (AWS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Amazon Web Services (AWS) a találatok listájában](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Amazon Web Services (AWS) nevű, "Britta Simon" tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó az Amazon Web Services (AWS) mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az Amazon Web Services (AWS) hivatkozás kapcsolatát kell létrehozni.

Az Amazon Web Services (AWS), rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Amazon Web Services (AWS) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Amazon Web Services (AWS) tesztfelhasználót](#create-an-amazon-web-services-aws-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon az Amazon Web Services (AWS), amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Amazon Web Services (AWS) alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Amazon Web Services (AWS), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Amazon Web Services (AWS)** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Az a **Amazon Web Services (AWS) tartomány és URL-címek** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral.

    ![Az Amazon Web Services (AWS) tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Az Amazon Web Services (AWS) alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![Egyszeri bejelentkezés attb konfigurálása](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)

5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Attribútum neve  | Attribútum értéke | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "A munkamenet időtartama az igényeknek száma értékének megadása" |  https://aws.amazon.com/SAML/Attributes |

    >[!TIP]
    >A felhasználók átadásának konfigurálása az Azure ad-ben a szerepkörök beolvasása az AWS konzolon kell. Tekintse meg az alábbi üzembe helyezési lépéseket.

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés addattb konfigurálása](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Az a **Namespace** szövegmezőbe írja be a névtér értéke a sorhoz látható.

    d. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png)

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/amazon-web-service-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban bejelentkezést az Amazon Web Services (AWS) vállalat webhelye rendszergazdaként.

9. Kattintson a **AWS kezdőlap**.

    ![Kezdőlap egyszeri bejelentkezés konfigurálása][11]

10. Kattintson a **identitás és hozzáférés-kezelés**.

    ![Egyszeri bejelentkezési identitás konfigurálása][12]

11. Kattintson a **Identitásszolgáltatók**, és kattintson a **létrehozása szolgáltató**.

    ![Egyszeri bejelentkezés szolgáltató konfigurálása][13]

12. Az a **konfigurálása szolgáltató** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés párbeszédpanel konfigurálása][14]

    a. Mint **szolgáltatótípus**válassza **SAML**.

    b. Az a **szolgáltatónevet** szövegmezőbe írja be a szolgáltató nevét (például: *fahulladékok*).

    c. A letöltött feltöltéséhez **metaadatait tartalmazó fájl** kattintson az Azure Portalról, **fájl kiválasztása**.

    d. Kattintson a **következő lépés**.

13. Az a **ellenőrizze a szolgáltató adatait** párbeszédpanel lap, kattintson a **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása ellenőrzése][15]

14. Kattintson a **szerepkörök**, és kattintson a **szerepkör létrehozása**.

    ![Egyszeri bejelentkezés szerepkörök konfigurálása][16]

15. Az a **szerepkör létrehozása** lapon, a következő lépésekkel:  

    ![Egyszeri bejelentkezés megbízhatósági kapcsolat beállítása][19]

    a. Válassza ki **SAML 2.0 összevonási** alatt **válassza ki a megbízható entitás típusa**.

    b. A **SAML 2.0-s szolgáltató szakasz választása**, jelölje be a **SAML-szolgáltató** korábban létrehozott (például: *fahulladékok*)

    c. Válassza ki **programozott engedélyezése és az AWS-felügyeleti konzol hozzáférés**.
  
    d. Kattintson a **tovább: engedélyek**.

16. Az a **engedélyházirend csatolása** párbeszédpanelen nem kell minden olyan szabályzat csatolása. Kattintson a **tovább: tekintse át**.  

    ![Egyszeri bejelentkezési szabályzat konfigurálása][33]

17. Az a **felülvizsgálati** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Konfigurálja az egyszeri bejelentkezés áttekintése][34]

    a. Az a **szerepkörnév** szövegmezőbe írja be a szerepkör nevét.

    b. Az a **szerepkör leírása** szövegmezőbe írja be a leírást.

    c. Kattintson a **szerepkör létrehozása**.

    d. Hozzon létre annyi szerepköröket, igény szerint, és megfeleltet az identitásszolgáltató.

18. A szerepkörök beolvasása az AWS-fiók az Azure AD-felhasználó kiépítési használni az AWS szolgáltatásfiók hitelesítő adatai. Ehhez nyissa meg az otthoni AWS konzolon.

19. Kattintson a **szolgáltatások** -> **Security, Identity & Compliance** -> **IAM**.

    ![a szerepkörök beolvasása az AWS-fiókból](./media/amazon-web-service-tutorial/fetchingrole1.png)

20. Válassza ki a **házirendek** lap IAM szakaszában.

    ![a szerepkörök beolvasása az AWS-fiókból](./media/amazon-web-service-tutorial/fetchingrole2.png)

21. Új szabályzat létrehozásához kattintson a **szabályzat létrehozása** az AWS-fiók az Azure AD-felhasználó kiépítési a szerepkörök beolvasása.

    ![Új házirend létrehozása](./media/amazon-web-service-tutorial/fetchingrole3.png)

22. A szerepkörök beolvasása az AWS-fiókok a következő lépések elvégzésével a saját szabályzat létrehozása:

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

23. Adja meg a **új szabályzat** a következő lépések végrehajtásával:

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy2.png)

    a. Adja meg a **házirendnév** , **AzureAD_SSOUserRole_Policy**.

    b. Megadhat **leírás** , a szabályzat **Ez a szabályzat lehetővé teszi a szerepkörök beolvasása az AWS-fiókok**.

    c. Kattintson a **"Házirend létrehozása"** gombra.

24. Hozzon létre egy új felhasználói fiók az AWS IAM-Service-ben a következő lépések végrehajtásával:

    a. Kattintson a **felhasználók** navigációs az AWS IAM-konzolon.

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy3.png)

    b. Kattintson a **felhasználó hozzáadása** gombra kattintva hozzon létre egy új felhasználót.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/policy4.png)

    c. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet, **AzureADRoleManager**.

    * A hozzáférés típusa, válassza ki a **programozás alapú hozzáférést** lehetőséget. Ezzel a módszerrel a felhasználó elindíthat az API-k és a szerepkörök beolvasása az AWS-fiókból.

    * Kattintson a **tovább engedélyek** gombra a jobb alsó sarokban.

25. Most hozzon létre egy új szabályzatot a felhasználó a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser2.png)

    a. Kattintson a **a meglévő szabályzatok közvetlen csatolása** gombra.

    b. Keresse meg az újonnan létrehozott szabályzat szakaszban **AzureAD_SSOUserRole_Policy**.

    c. Válassza ki a **házirend** majd kattintson a a **tovább: tekintse át** gombra.

26. Tekintse át a szabályzatot a csatolt felhasználóhoz a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználónevet, a hozzáférés típusa, és a szabályzat hozzárendelve a felhasználóhoz.

    b. Kattintson a **felhasználó létrehozása** gombra, és hozza létre a felhasználót a jobb alsó sarokban.

27. Töltse le a felhasználói hitelesítő adatait, a felhasználó a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja ki a felhasználó **hozzáférési kulcs azonosítója** és **titkos hívóbetűje**.

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó kiépítésére szakasz a szerepkörök beolvasása az AWS-konzolról.

    c. Kattintson a **Bezárás** gombra a lap alján.

28. Navigáljon a **Felhasználókiépítés** Amazon Web Services-alkalmazás az Azure AD felügyeleti portál szakaszában.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning.png)

29. Adja meg a **hozzáférési kulcs** és **titkos** a a **titkos Ügyfélkód** és **titkos jogkivonat** mező jelölik.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Adja meg az AWS felhasználói hozzáférési kulcsot a a **clientsecret** mező.

    b. Adja meg az AWS – felhasználói titkos a **titkos jogkivonat** mező.

    c. Kattintson a **kapcsolat tesztelése** gombra kattintva kell sikeresen tesztelheti ezt a kapcsolatot.

    d. A beállítás mentéséhez kattintson a a **mentése** gombra az oldal tetején.

30. Most győződjön meg arról, hogy engedélyezze-e az üzembe helyezési állapotra **a** a kapcsoló meg és kattintson a beállítások szakaszában a **mentése** gombra az oldal tetején.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/amazon-web-service-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/amazon-web-service-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/amazon-web-service-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-amazon-web-services-aws-test-user"></a>Az Amazon Web Services (AWS) tesztfelhasználó létrehozása

Ez a szakasz célja egy Britta Simon az Amazon Web Services (AWS) nevű felhasználó létrehozásához. Az Amazon Web Services (AWS) nem kell a felhasználót, hogy hozhatók létre a rendszer flexibilitását egyszeri Bejelentkezést, így nem szükséges itt bármely művelet elvégzésére.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az Amazon Web Services (AWS) Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon Amazon Web Services (AWS) való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Amazon Web Services (AWS)**.

    ![Az Amazon Web Services (AWS) hivatkozásra az alkalmazások listáját](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Amazon Web Services (AWS) csempére kattint, akkor kell lekérése automatikusan bejelentkezett az Amazon Web Services (AWS) alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/amazon-web-service-tutorial/tutorial_general_203.png
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

