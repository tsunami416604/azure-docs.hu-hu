---
title: "Oktatóanyag: Azure Active Directory-integráció Amazon Web Services (AWS) |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Amazon Web Services (AWS) között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 1678d44fc7769c2015c3779ce713870af7a40de9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Oktatóanyag: Azure Active Directory-integráció Amazon Web Services (AWS)

Ebben az oktatóanyagban elsajátíthatja Amazon Web Services (AWS) integrálása az Azure Active Directory (Azure AD).

Amazon Web Services (AWS) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az Amazon Web Services (AWS) (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Amazon Web Services (AWS), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Amazon Web Services (AWS) egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Amazon Web Services (AWS) hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) hozzáadása a gyűjteményből
Az Azure AD-be az Amazon Web Services (AWS)-integráció konfigurálása szüksége Amazon Web Services (AWS) hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Adja hozzá az Amazon Web Services (AWS) a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure Portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Amazon Web Services (AWS)**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. Az eredmények panelen válassza ki a **Amazon Web Services (AWS)**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Amazon Web Services (AWS) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói Amazon Web Services (AWS) közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Amazon Web Services (AWS).

Az Azure AD egyszeri bejelentkezést az Amazon Web Services (AWS) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Az Amazon Web Services tesztfelhasználó létrehozása](#creating-an-amazon-web-services-test-user)**  - kell rendelkeznie a Britta Simon megfelelője az Amazon Web Services (AWS), amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Amazon Web Services (AWS) alkalmazásban.

**Az Amazon Web Services (AWS) konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Amazon Web Services (AWS)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** válasszon **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. Az a **Amazon Web Services (AWS) tartományhoz és URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. Az Amazon Web Services (AWS) alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve  | Attribútum értéke | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://AWS.amazon.com/SAML/Attributes |
    | Szerepkör            | User.assignedroles |  https://AWS.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Szeretne beállítani, hogy a felhasználók átadása a szerepkörök beolvasása a AWS konzol az Azure AD-ben. Tekintse meg az alábbi üzembe helyezési lépéseket.

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke. A Namespace érték hozzáadásával a fent megadott.
    
    d. Kattintson az **OK** gombra.

7. Kattintson a **mentése** gombra kattintva mentse a beállításokat az Azure-on.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban bejelentkezés az Amazon Web Services (AWS) vállalati helyre rendszergazdaként.

9. Kattintson a **konzol otthoni**.
   
    ![Egyszeri bejelentkezés konfigurálása][11]

10. Kattintson a **IAM** a **biztonsági, identitás- & megfelelőségi** szolgáltatás.
   
    ![Egyszeri bejelentkezés konfigurálása][12]

11. Kattintson a **identitás-szolgáltatóktól**, és kattintson a **létrehozása szolgáltató**.
   
    ![Egyszeri bejelentkezés konfigurálása][13]

12. Az a **szolgáltató konfigurálása** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása][14]
 
    a. Mint **szolgáltatótípust**, jelölje be **SAML**.

    b. Az a **szolgáltatónevet** szövegmező, írja be a szolgáltató nevét (pl.: *fahulladékok*).

    c. A letöltött metaadat-fájl feltöltése, kattintson a **Choose File**.

    d. Kattintson a **következő lépés**.

13. Az a **ellenőrizze a szolgáltató adatait** párbeszédpanel lap, kattintson a **létrehozása**. 
    
    ![Egyszeri bejelentkezés konfigurálása][15]

14. Kattintson a **szerepkörök**, és kattintson a **hozzon létre új szerepkör**. 
    
    ![Egyszeri bejelentkezés konfigurálása][16]

15. Az a **szerepkörnév beállítása** párbeszédpanelen hajtsa végre a következő lépéseket: 
    
    ![Egyszeri bejelentkezés konfigurálása][17] 

    a. Az a **szerepkörnév** szövegmező, írja be a szerepkör nevét (pl.: *tesztfelhasználó néven*). 

    b. Kattintson a **következő lépés**.

16. Az a **szerepkör típusának kiválasztása** párbeszédpanelen hajtsa végre a következő lépéseket: 
    
    ![Egyszeri bejelentkezés konfigurálása][18] 

    a. Válassza ki **Identity Provider hozzáférési szerepkör**. 

    b. Az a **SAML-szolgáltatók Grant webes egyszeri bejelentkezés (WebSSO) hozzáférést** területen kattintson **válasszon**.

17. Az a **létre megbízható** párbeszédpanelen hajtsa végre a következő lépéseket:  
    
    ![Egyszeri bejelentkezés konfigurálása][19] 

    a. SAML-szolgáltatóként, válassza ki a korábban létrehozott SAML-szolgáltató (pl.: *fahulladékok*)
  
    b. Kattintson a **következő lépés**.

18. A a **szerepkör megbízható ellenőrizze** párbeszédpanel, kattintson a **tovább**.
    
    ![Egyszeri bejelentkezés konfigurálása][32]

19. Az a **csatolása házirend** párbeszédpanel, kattintson a **tovább**.
    
    ![Egyszeri bejelentkezés konfigurálása][33]

20. Az a **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása][34]
 
    a. Kattintson a **szerepkör létrehozása**.

    b. Igény szerint annyi szerepköröket hozhat létre, és az identitásszolgáltató való hozzárendelése.

21. Mostantól konfigurálhatja a felhasználót a szerepkörök beolvasása a AWS kiépítése

    a. Az AWS konzol a gyökér-fiókkal történő bejelentkezés a

    b. Jobb felső sarokban kattintson a nevére, és kattintson a **saját biztonsági hitelesítő adatok** lehetőséget. Figyelmeztető üzenet, ekkor megnyílik egy olyan képernyőt. Kattintson a gombra **biztonsági hitelesítő adatok** gomb a képernyő.
        
       ![Egyszeri bejelentkezés konfigurálása][36]

       ![Egyszeri bejelentkezés konfigurálása][37]

    c. A Tárelérési kulcsok szakaszban kattintson a **új kulcs létrehozása** gombra. Ezt követően a hozzáférési kulcs Azonosítóját és a token értékét.
    
       ![Egyszeri bejelentkezés konfigurálása][38]

    d. Másolja a következő két értéket, és töltse le a is maga után, hogy ne vesszenek el.

    e. Az Azure portálon, az alkalmazás integrációs Amazon Web Services (AWS) lapon kattintson a **kiépítési**.
        
       ![Egyszeri bejelentkezés konfigurálása][35]

    f. A kiépítési mód beállítása legyen **automatikus**
        
       ![Egyszeri bejelentkezés konfigurálása][39]

    g. Jelenleg a **clientsecret** és **titkos Token** illessze be a megfelelő értékek, amelyeket az AWS konzol másolta.
    
    h. Kattintson a **kapcsolat tesztelése** a kapcsolat tesztelése gombra. Ha a művelet sikeres majd elindíthatja az üzembe helyezési összekötő.
       
       ![Egyszeri bejelentkezés konfigurálása][40]

    i. Most már engedélyezheti a kiépítési állapot **a**. A szerepkörök beolvasása a az alkalmazás elindul.

       ![Egyszeri bejelentkezés konfigurálása][41]

    > [!NOTE]
    > Az Azure AD-kiépítés szolgáltatásának futtatásakor minden szinkronizálása AWS a szerepköröket egy kis idő múlva. Meg kell jelennie az identitásszolgáltató AWS szerepkörök csatolva az Azure AD-be, és az alkalmazások felhasználók vagy csoportok kijelölése közben is használhatja őket.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-amazon-web-services-test-user"></a>Az Amazon Web Services tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD felhasználók jelentkezzenek be az Amazon Web Services (AWS), akkor ki kell építenie az Amazon Web Services (AWS). Esetén Amazon Web Services (AWS), a manuális tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Amazon Web Services (AWS)** vállalati hely rendszergazdaként.

2. Kattintson a **konzol otthoni** ikonra. 
   
    ![Egyszeri bejelentkezés konfigurálása][11]

3. Kattintson az identitás és hozzáférés-kezelést. 
   
    ![Egyszeri bejelentkezés konfigurálása][28]

4. Az irányítópulton kattintson **felhasználók**, és kattintson a **hozzon létre új felhasználók**. 
   
    ![Egyszeri bejelentkezés konfigurálása][29]

5. A felhasználó létrehozása párbeszédpanelen hajtsa végre a következő lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][30]   
    
    a. Az a **adja meg a felhasználói neveket** szövegmezőből, írja be a Brita Simon felhasználónevet (userprincipalname), az Azure ad-ben.

    b. Kattintson a **létrehozása.**
        
### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés nyújtó az Amazon Web Services (AWS) használatára.

![Felhasználó hozzárendelése][200] 

**Az Amazon Web Services (AWS) Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Amazon Web Services (AWS)**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. A **Szerepkörválasztás** lapra, válassza ki a megfelelő szerepkört a felhasználó számára. Ezek a szerepkörök jelennek meg a szerepkör nevét és az identitás-szolgáltató neve. Így egyszerűbb azonosítani a AWS szerepköröket.

8. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Amazon Web Services (AWS) csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Amazon Web Services (AWS) alkalmazáshoz. 

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
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
