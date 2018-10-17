---
title: 'Oktatóanyag: Azure Active Directory-integráció az Amazon Web Services (AWS) való kapcsolódáshoz, több fiók |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure AD között, és több fiókot, az Amazon Web Services (AWS).
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
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: a9acb9539497c85f408ce7417fa5983072ea80b9
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365662"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Oktatóanyag: Azure Active Directory-integráció az Amazon Web Services (AWS) több fiók

Ebben az oktatóanyagban elsajátíthatja az Azure Active Directory (Azure AD) integrálása több fiókot, az Amazon Web Services (AWS).

Az Amazon Web Services (AWS) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Amazon Web Services (AWS) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

![Az Amazon Web Services (AWS) a találatok listájában](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Ne feledje, az AWS-fiókok egy AWS-alkalmazás csatlakoztatása a nem az ajánlott módszer. Ehelyett azt javasoljuk, használhatja [ez](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) megközelítést több példánya AWS-fiók több példányra, az AWS-alkalmazások konfigurálása az Azure ad-ben.

**Vegye figyelembe, hogy nem ajánlott ezt a módszert használja a következő okok miatt:**

* A Graph Explorer módszer használatához az alkalmazás a szerepkörök javítására van. A jegyzékfájl módszer használata nem ajánlott.

* Úgy találtuk, hogy a felvett alkalmazás-szerepkörök ~ 1200-as egyetlen AWS alkalmazás esetében, az alkalmazás a megadott műveletet el a hibákat reporting ügyfelek kapcsolatos mérete. Nincs rögzített korlátja az alkalmazásobjektumban méretét.

* Manuálisan frissíteni a szerepkör, mert a szerepkörök hozzáadja bármely, a partnerek, azaz egy cserélje le a módszert, és nem Hozzáfűzés sajnos rendelkezik. Is, ha a fiókok növekvő majd ez lesz a partnerek és a szerepkörök n kapcsolat x n.

* Az AWS-fiókok fogja használni az ugyanazon összevonási metaadatainak XML-fájlt, és a tanúsítványváltás időpontjában frissíteni a tanúsítványt az AWS-fiókok egyszerre nagy gyakorlathoz meghajtó kell

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Amazon Web Services (AWS), a következőkre van szükség:

- Az Azure AD prémium vagy alapszintű előfizetés
- Az Amazon Web Services (AWS) több egyszeri bejelentkezés engedélyezve van a fiókok

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az Amazon Web Services (AWS) hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Az Amazon Web Services (AWS) hozzáadása a katalógusból
A-integráció konfigurálása az Amazon Web Services (AWS) Azure AD-be, szüksége a katalógusból az Amazon Web Services (AWS) hozzáadása a felügyelt SaaS-alkalmazások listájában.

**Az Amazon Web Services (AWS) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/aws-multi-accounts-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/aws-multi-accounts-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/aws-multi-accounts-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Amazon Web Services (AWS)**, jelölje be **Amazon Web Services (AWS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Az alkalmazás hozzáadása után lépjen a **tulajdonságok** lapjáról, és másolja a **Objektumazonosító**.

    ![Az Amazon Web Services (AWS) a találatok listájában](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Amazon Web Services (AWS) nevű, "Britta Simon" tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó az Amazon Web Services (AWS) mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az Amazon Web Services (AWS) hivatkozás kapcsolatát kell létrehozni.

Az Amazon Web Services (AWS), rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Amazon Web Services (AWS) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Amazon Web Services (AWS) alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Amazon Web Services (AWS), hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **Amazon Web Services (AWS)** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/aws-multi-accounts-tutorial/B1_B2_Select_SSO.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/aws-multi-accounts-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/aws-multi-accounts-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Az Amazon Web Services (AWS) alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![image](./media/aws-multi-accounts-tutorial/i4-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név)  | Adatforrás-attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Adjon meg egy értéket 900 másodperc (15 perc) között 43200 másodperc (12 óra)" |  https://aws.amazon.com/SAML/Attributes |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/aws-multi-accounts-tutorial/i2-attribute.png)

    ![image](./media/aws-multi-accounts-tutorial/i3-attribute.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Adja meg a **Namespace** értéket.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  , és mentse a számítógépre.

    ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

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

18. Jelentkezzen ki a jelenlegi AWS-fiók és bejelentkezés másik fiókkal kívánja az Azure AD egyszeri bejelentkezés konfigurálása.

19. Hajtsa végre. lépés – 9. lépés – 17 hozhat létre, amelyeket szeretne beállítani a fiók több szerepkört. Ha több mint két fiókot, hajtson végre ugyanezekkel a lépésekkel hozhat létre szerepköröket azokat a fiókokat.

20. Miután az összes szerepkört jönnek létre a fiókokat, azok megjelennek a **szerepkörök** azok a fiókok listáját.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Rögzítheti a szerepkör információ és a megbízható entitások összes szerepköre összes fiókot, amely kell manuálisan leképezése az Azure AD-alkalmazás között meg kell. 

22. Kattintson a szerepkörök másolása a **szerepkör információ** és **megbízható entitások** értékeket. Ezeket az értékeket a szerepköröket kell létrehoznia az Azure ad-ben van szükség.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

23. Hajtsa végre a fenti lépést az összes fiók összes szerepköre, és tárolja azokat **szerepkör információ, a megbízható entitások** a Jegyzettömbben.

24. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    a. Jelentkezzen be a Graph Explorer hely a bérlőhöz tartozó globális rendszergazdai vagy társadminisztrátori hitelesítő adatok használatával.

    b. Szüksége lesz a megfelelő engedélyekkel a szerepkörök létrehozásához. Kattintson a **engedélyek módosítása** beolvasni a szükséges engedélyekkel.

    ![Graph explorer-párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Válassza ki a listáról (ha ezek még nem rendelkezik) engedélyeket a következő "Engedélyek módosítása" gombra 

    ![Graph explorer-párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Ez fogja kérni, hogy jelentkezzen be újra, és fogadja el a feltételeket. Elfogadja a feltételeket, miután bejelentkezett a Graph Explorer újra.

    e. Módosítsa a verzió legördülő **béta**. A szolgáltatásnevek beolvasni a bérlőről, használja a következő lekérdezést:

     `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több címtárat használ, majd használhatja a következő mintát, amely rendelkezik, az elsődleges tartomány `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph explorer-párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. A listából beolvasott szolgáltatásnevek módosítania kell egy get. A Ctrl + F használatával keresse meg a listában szereplő szolgáltatásnevek az alkalmazás. Használhatja a következő lekérdezést a **objektumazonosító** amelyek lekérni, a megfelelő egyszerű szolgáltatást az Azure AD tulajdonságok oldaláról másolta.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph explorer-párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Bontsa ki a appRoles tulajdonság szolgáltatásnév-objektum.

    ![Graph explorer-párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Most létre kell hoznia az alkalmazás új szerepkörök. 

    i. Alább JSON, amelyek appRoles objektum. Hozzon létre egy hasonló objektumot a kívánt alkalmazás szerepkörök hozzáadását.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Csak akkor adhat hozzá új szerepkörök után a **msiam_access** a patch művelethez. Is ahogy a szervezet szükség szerint kívánja annyi szerepkörök is hozzáadhat. Az Azure AD elküldi a **érték** ilyen szerepkörbe, mint a jogcím értékét az SAML-válasz.

    j. Lépjen vissza a Graph Explorer és a módszert a **első** való **javítására**. Az egyszerű szolgáltatás objektumhoz van szükség szerepkörök appRoles tulajdonság a példában a fent látható hasonló frissítésével javítások. Kattintson a **lekérdezés futtatása** a javítási művelet végrehajtásához. A sikert jelző üzenet megerősíti, hogy a szerepkör az Amazon Web Services-alkalmazás létrehozása.

    ![Graph explorer-párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Után az egyszerű szolgáltatás javított tartományvezérlőről érkezett, a további szerepkörök, felhasználók vagy csoportok rendelhet szerepét. Ez a Ugrás a portálra, és ellenőrizheti, hogy az Amazon Web Services-alkalmazás által elvégezhető. Kattintson a **felhasználók és csoportok** lap tetején. 

26. Azt javasoljuk, hogy hozzon létre új csoportokat minden AWS szerepkör, így hozzárendelheti az adott szerepkörhöz, hogy a csoportban. Vegye figyelembe, hogy ez egy-egy leképezési egy csoport egy szerepkört. Ezután hozzáadhatja a csoporthoz tartozó tagokat.

27. A csoportok létrehozása után válassza ki azt a csoportot, és rendelje hozzá az alkalmazást.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

> [!Note]
> Beágyazott csoportokat nem támogatottak csoportok hozzárendelése során.

28. A szerepkör hozzárendelése a csoporthoz, válassza ki a szerepkört, majd kattintson a **hozzárendelése** gombot a lap alján.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Vegye figyelembe, hogy szeretne-e a munkamenet az Azure Portalon új szerepkörök megtekintéséhez frissítse.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Amazon Web Services (AWS) csempére kattint, Amazon Web Services (AWS) alkalmazás oldalán válassza ki a szerepkört lehetőség szerezheti be.

![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Az SAML-válasz megtekintéséhez a szerepkörök jogcímekként átadott is ellenőrizheti.

![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/