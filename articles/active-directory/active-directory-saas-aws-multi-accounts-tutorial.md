---
title: 'Oktatóanyag: Azure Active Directory-integráció az Amazon Web Services (AWS) több fiók kapcsolódni |} Microsoft Docs'
description: Egyszeri bejelentkezés az Azure AD között konfigurálásával és több fiók Amazon Web Services (AWS).
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
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: a6a7861b95f82180b72045f10db99d5bc45eed73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Oktatóanyag: Azure Active Directoryval integrált több Amazon Web Services (AWS) fiók

Ebben az oktatóanyagban elsajátíthatja Azure Active Directory (Azure AD) integrálása több fiók Amazon Web Services (AWS).

Amazon Web Services (AWS) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az Amazon Web Services (AWS) (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Amazon Web Services (AWS), a következőkre van szükség:

- Az Azure AD prémium vagy alapszintű előfizetés
- Amazon Web Services (AWS) több egyszeri bejelentkezés engedélyezett fiókok

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

    ![Amazon Web Services (AWS) az eredménylistában](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Az alkalmazás hozzáadása után Ugrás **tulajdonságok** lapjáról, és másolja a **Objektumazonosító**.

    ![Amazon Web Services (AWS) az eredménylistában](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Amazon Web Services (AWS) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói Amazon Web Services (AWS) közötti kapcsolat kapcsolatot kell létrehozni.

Az Amazon Web Services (AWS), rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Amazon Web Services (AWS) alkalmazásban.

**Az Amazon Web Services (AWS) konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Amazon Web Services (AWS)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Az a **Amazon Web Services (AWS) tartományhoz és URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához.

    ![Az egyszeri bejelentkezés információk amazon Web Services (AWS) tartományhoz és URL-címek](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Az Amazon Web Services (AWS) alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve  | Attribútum értéke | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Szeretne beállítani, hogy a felhasználók átadása a szerepkörök beolvasása a AWS konzol az Azure AD-ben. Tekintse meg az alábbi üzembe helyezési lépéseket.

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Az a **Namespace** szövegmező, írja be a névtér értéke az adott sorhoz feltüntetett.
    
    d. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban bejelentkezés az Amazon Web Services (AWS) vállalati helyre rendszergazdaként.

9. Kattintson a **AWS otthoni**.
   
    ![Egyszeri bejelentkezés otthoni konfigurálása][11]

10. Kattintson a **IAM** (identitás és hozzáférés-kezelés). 
   
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

14. Kattintson a **szerepkörök**, és kattintson a **szerepkör létrehozása**. 
    
    ![Egyszeri bejelentkezés szerepkörök konfigurálása][16]

15. Az a **szerepkör létrehozása** lapon, a következő lépésekkel:  
    
    ![Egyszeri bejelentkezés megbízhatósági kapcsolat beállítása][19] 

    a. Válassza ki **SAML 2.0 összevonási** alatt **válassza ki a megbízható entitás típusa**.

    b. A **válassza a SAML 2.0-s szolgáltató szakasz**, jelölje be a **SAML-szolgáltató** korábban létrehozott (például: *fahulladékok*)

    c. Válassza ki **programozott engedélyezése és AWS felügyeleti konzol eléréséhez**.
  
    d. Kattintson a **tovább: engedélyek**.

16. A a **csatolása engedélyek házirendek** párbeszédpanel, kattintson a **tovább: felülvizsgálati**.  
    
    ![Egyszeri bejelentkezés házirend konfigurálása][33]

17. Az a **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:   
    
    ![Egyszeri bejelentkezés felülvizsgálati konfigurálása][34] 

    a. Az a **szerepkörnév** szövegmező, írja be a szerepkör nevét.

    b. Az a **szerepkör leírása** szövegmező, adja meg a leírását.

    a. Kattintson a **szerepkör létrehozása**.

    b. Igény szerint annyi szerepköröket hozhat létre, és az identitásszolgáltató való hozzárendelése.

18. Kijelentkezés az aktuális AWS fiók és más fiókra, amelyhez egyszeri bejelentkezési konfigurálása az Azure AD-val történő bejelentkezés.

19. Hajtsa végre. lépés – 9. lépés-17 kívánt beállítása ehhez a fiókhoz több szerepkör létrehozásához. Ha több mint két fiókot, hajtson végre szerepkörök létrehozása a számukra a fiókok ugyanazokat a lépéseket.

20. Ha a szerepkörök jöttek létre a fiókokat, azok megjelennek a **szerepkörök** ezek a fiókok listája.

    ![Szerepkörök beállítása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Igazolnia kell a szerepkör-információ és a megbízható entitások rögzítése a szerepkörök összes a fiók, amely igazolnia kell manuálisan megfeleltetni az Azure AD-alkalmazást. 

22. Kattintson a szerepkörök másolása az **szerepkör információ** és **megbízható entitások** értékeket. Ezeket az értékeket szükség van az Azure AD-ben létrehozásához szükséges szerepköröket.

    ![Szerepkörök beállítása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Hajtsa végre a fenti lépést az összes fiókot a szerepkörök és az összes formátumban tárolják a **szerepkör információ, a megbízható entitások** egy Jegyzettömb. 

24. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    a. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.

    b. Kell rendelkeznie a szerepkörök létrehozásához szükséges engedélyekkel. Kattintson a **módosítsa az engedélyeket** lekérni a szükséges engedélyekkel. 

    ![Graph explorer párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Jelölje be a következő engedélyeket a listáról (ha ezek már nem rendelkezik) "Engedélyek módosítása" gombra 

    ![Graph explorer párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Ez ekkor megkérdezi, hogy újból bejelentkezni, és fogadja el a hozzájárulásukat adják. A hozzájárulási elfogadása, miután bejelentkezett a Graph Explorer újra.

    e. A verzió legördülő menüből való módosítása **beta**. A szolgáltatás rendszerbiztonsági tagok beolvasni a bérlő, használja a következő lekérdezést:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Ha több címtárral használ, majd alábbi felhasználhatja mintát, amely azt az elsődleges tartománya `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph explorer párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Használhatja a következő lekérdezés a **objektumazonosító** , amely a megfelelő szolgáltatásnév segítségével az Azure AD-tulajdonságok oldalról másolta.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph explorer párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból. 

    ![Graph explorer párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Most szeretne létrehozni az alkalmazás új szerepköröket. 

    i. Alább JSON példája appRoles objektum. Hozzon létre egy hasonló objektumot a kívánt alkalmazás szerepkörök hozzáadásához. 

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
    > Csak akkor adhat hozzá új szerepkörök után a **msiam_access** a javítás művelet. Is ahányat csak szeretne, a szervezet kell egy tetszőleges számú szerepkörök is hozzáadhat. Az Azure AD fog küldeni a **érték** a fenti két szerepkör SAML-válasz a jogcím értéke.
    
    j. Lépjen vissza a diagramon Explorer és a módszer váltása **beolvasása** való **javítás**. Javítás az egyszerű objektum azáltal, hogy frissíti a fenti példában látható módon a hasonló appRoles tulajdonság szerepkörök van szükség. Kattintson a **lekérdezés futtatása** a javítás művelet végrehajtásához. A sikerről szóló üzenetet megerősíti, hogy a szerepkör az Amazon Web Services-alkalmazás létrehozása.

    ![Graph explorer párbeszédpanel](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Után az egyszerű szolgáltatás van telepítve a további szerepkörök, felhasználók vagy csoportok rendelhet szerepét. Ezt megteheti portal fog, és az Amazon Web Services alkalmazásának megnyitásával. Kattintson a **felhasználók és csoportok** a legfelső lap. 

26. Azt javasoljuk, hogy hozzon létre új csoportokat minden AWS szerepkörhöz, úgy, hogy a csoport adott szerepkörhöz rendelhető. Vegye figyelembe, hogy ez egy-egy leképezést egy csoport egy szerepkörhöz. Majd adja hozzá a csoporthoz tartozó tagjai.

27. A csoportok létrehozása után válassza ki azt a csoportot, és rendelje hozzá az alkalmazást. 

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. A szerepkör hozzárendeléséhez a csoportot, válassza ki a szerepkört, majd kattintson a **hozzárendelése** gombra az oldal alján.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Vegye figyelembe, hogy szeretné-e a munkamenet során az Azure portálhoz, és új szerepkörök frissítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Amazon Web Services (AWS) csempére kattint, Amazon Web Services (AWS) alkalmazás lapon kiválaszthatja azokat a szerepkör szerezheti be.

![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Azt is ellenőrizheti, hogy átadta-e jogcímekként a szerepkörök SAML-válasz.

![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

