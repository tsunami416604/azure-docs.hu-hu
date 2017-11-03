---
title: "Oktatóanyag: Azure Active Directoryval integrált ServiceNow |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a ServiceNow és a ServiceNow Express között."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Oktatóanyag: Azure Active Directoryval integrált ServiceNow
Ebben az oktatóanyagban elsajátíthatja a ServiceNow és a ServiceNow Express integrálása az Azure Active Directory (Azure AD).

A ServiceNow és a ServiceNow Express integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

* Szabályozhatja, aki hozzáfér a ServiceNow és a ServiceNow Express Azure AD-ben
* Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása aláírt a ServiceNow és a ServiceNow Express (egyszeri bejelentkezés) a saját Azure AD-fiókok
* Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek
Az Azure AD-integráció konfigurálása a ServiceNow és a ServiceNow Express, az alábbi elemek szükségesek:

* Az Azure AD szolgáltatásra
* A ServiceNow, egy példány vagy bérlő ServiceNow, Calgary verzió vagy újabb
* A ServiceNow expressz, ServiceNow kifejezett, Helsinki verzió példányának vagy újabb
* A ServiceNow bérlő kell rendelkeznie a [több szolgáltató egyszeri bejelentkezést a beépülő modul](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) engedélyezve van. Ezt úgy teheti [szolgáltatási kérelem elküldése](https://hi.service-now.com). 

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.
> 
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

* Ne használja az éles környezetben, ha ez nem szükséges.
* Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A ServiceNow hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés ServiceNow vagy ServiceNow Express

## <a name="adding-servicenow-from-the-gallery"></a>A ServiceNow hozzáadása a gyűjteményből
Az Azure AD integrálása a ServiceNow vagy ServiceNow Express konfigurálásához kell hozzáadnia a ServiceNow a gyűjteményből a felügyelt SaaS-alkalmazások listájára. 

**A ServiceNow hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**. 
   
    ![Active Directory][1]
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások][2]
4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazások][3]
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazások][4]
6. Írja be a keresőmezőbe, **ServiceNow**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. Az eredmények ablaktáblájában válassza **ServiceNow**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása és tesztelése az Azure AD az egyszeri bejelentkezés ServiceNow vagy ServiceNow Express "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a ServiceNow tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a ServiceNow közötti kapcsolat kapcsolatot kell létrehozni.
Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a ServiceNow. Az Azure AD egyszeri bejelentkezést a ServiceNow tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés beállítása a ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD az egyszeri bejelentkezés beállítása a ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
3. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
4. **[A ServiceNow tesztfelhasználó létrehozása](#creating-a-servicenow-test-user)**  - való Britta Simon egy megfelelője a ServiceNow, amely csatolva van rá, hogy az Azure AD ábrázolása.
5. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
6. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

> [!NOTE]
> Ha szeretne konfigurálni a ServiceNow hagyja el a 2. lépés. Hasonlóképpen ha azt szeretné, hogy a ServiceNow Express konfigurálásához 1. lépés kihagyása.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>A ServiceNow az Azure AD-egyszeri bejelentkezés konfigurálása
1. A klasszikus Azure AD portálon a a **ServiceNow** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC749323.png "egyszeri bejelentkezés konfigurálása")

2. Az a **hová felhasználók számára történő bejelentkezést ServiceNow** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC749324.png "egyszeri bejelentkezés konfigurálása")

3. Az a **Alkalmazásbeállítások konfigurálása** lapon, a következő lépésekkel:
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC769497.png "alkalmazás URL-CÍMEK konfigurálása")
   
    a. az a **ServiceNow bejelentkezési URL-cím** szövegmező, írja be az URL-címet használják-e a felhasználók bejelentkezés a ServiceNow alkalmazáshoz a következő mintát: `https://<instance-name>.service-now.com`.
   
    b. Az a **azonosító** szövegmező, írja be az URL-cím segítségével a felhasználók bejelentkezés a ServiceNow alkalmazáshoz a következő mintát: `https://<instance-name>.service-now.com`.
   
    c. Kattintson a **Tovább** gombra

4. Ahhoz, hogy az Azure AD automatikus konfigurálása a ServiceNow SAML-alapú hitelesítéshez, adja meg a ServiceNow példány nevét, a rendszergazda felhasználónevét és a rendszergazdai jelszó a **automatikus konfigurálása egyszeri bejelentkezéshez** kialakításához, és kattintson a *konfigurálása*. Vegye figyelembe, hogy a rendszergazdai felhasználónevet kell rendelkeznie a **security_admin** működéséhez a ServiceNow hozzárendelve szerepkör. Ellenkező esetben a SAML-Identitásszolgáltatóként az Azure AD használandó ServiceNow kézzel konfigurálásához kattintson **manuálisan állítsa be az alkalmazását az egyszeri bejelentkezés**, majd kattintson a **következő** és kövesse az alábbi lépéseket.
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "alkalmazás URL-CÍMEK konfigurálása")

5. A a **konfigurálhatja az egyszeri bejelentkezés ServiceNow** kattintson **tanúsítvánnyal letöltés**, mentse helyileg a számítógépen a tanúsítvány fájlt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC749325.png "egyszeri bejelentkezés konfigurálása")

6. Bejelentkezés a ServiceNow alkalmazást rendszergazdaként.

7. Aktiválja a *integrációs - több szolgáltató egyszeri bejelentkezés telepítő* beépülő modul a következő lépéseket követve:
   
    a. A bal oldali navigációs ablaktábláján válassza **rendszer Definition** szakaszt, és kattintson a **beépülő modulok**.
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "beépülő modul aktiválása")
   
    b. Keresse meg *integrációs - több szolgáltató egyszeri bejelentkezés telepítő*.
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "beépülő modul aktiválása")
   
    c. Válassza ki a beépülő modul. Rigth kattintson, és válassza ki **aktiválás/frissítése**.
   
    d. Kattintson a **aktiválás** gombra.

8. A bal oldali navigációs ablaktábláján kattintson **tulajdonságok**.  
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "alkalmazás URL-CÍMEK konfigurálása")

9. Az a **több SSO tulajdonságokat** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "alkalmazás URL-CÍMEK konfigurálása")
   
    a. Mint **több szolgáltató SSO engedélyezése**, jelölje be **Igen**.
   
    b. Mint **hibakeresési naplózás kapott a több szolgáltató SSO-integráció engedélyezése**, jelölje be **Igen**.
   
    c. A **a mezőt, a felhasználó táblázat...**  szövegmezőhöz típus **felhasználónév**.
   
    d. Kattintson a **Save** (Mentés) gombra.

10. A bal oldali navigációs ablaktábláján kattintson **x509 tanúsítványok**.
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "egyszeri bejelentkezés konfigurálása")

11. Az a **X.509-tanúsítványokat** párbeszédpanel, kattintson a **új**.
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "egyszeri bejelentkezés konfigurálása")

12. Az a **X.509-tanúsítványokat** párbeszédpanelen hajtsa végre a következő lépéseket:
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "egyszeri bejelentkezés konfigurálása")
    
     a. Kattintson az **Új** lehetőségre.
    
     b. Az a **neve** szövegmező, adja meg a konfiguráció nevét (pl.: **TestSAML2.0**).
    
     c. Válassza ki **aktív**.
    
     d. Mint **formátum**, jelölje be **PEM**.
    
     e. Mint **típus**, jelölje be **megbízható tároló Cert**.
    
     f. A Base64 kódolású tanúsítvány megnyitása a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be azt a **PEM tanúsítvány** szövegmező.
    
     g. Kattintson a **frissítés**.

13. A bal oldali navigációs ablaktábláján kattintson **identitás-szolgáltatóktól**.
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

14. Az a **identitás-szolgáltatóktól** párbeszédpanel, kattintson a **új**:
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "egyszeri bejelentkezés konfigurálása")

15. Az a **identitás-szolgáltatóktól** párbeszédpanel, kattintson a **egy SAML2 1. frissítés?**:
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "egyszeri bejelentkezés konfigurálása")

16. Egy SAML2 1. frissítés tulajdonságai párbeszédpanelen hajtsa végre a következő lépéseket:
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "egyszeri bejelentkezés konfigurálása")

    a. az a **neve** szövegmező, adja meg a konfiguráció nevét (pl.: **SAML 2.0**).

    b. Az a **felhasználói mező** szövegmezőhöz típus **e-mail** vagy **felhasználónév**, attól függően, melyik mezőt a ServiceNow környezetben a felhasználók egyedi azonosítására szolgál. 

    > [!NOTE] 
    > Az Azure AD configue hozható létre az Azure AD felhasználói azonosító (egyszerű felhasználónév) vagy az e-mail cím a SAML-jogkivonat egyedi azonosítóként a is a **ServiceNow > attribútumok > egyszeri bejelentkezés** a klasszikus Azure portálra, és a kívánt mezőt leképezési a **nameidentifier** attribútum. A kiválasztott attribútumnak az Azure AD (pl. egyszerű felhasználónév) tárolt érték meg kell egyeznie a megadott mező (pl. felhasználónév) ServiceNow tárolt érték

    c. A klasszikus Azure AD portálon, másolja a **identitás Szolgáltatóazonosító** értékét, és illessze be azt a **identitási szolgáltató URL-cím** szövegmező.

    d. A klasszikus Azure AD portálon, másolja a **hitelesítési kérelem URL-cím** értékét, és illessze be azt a **identitásszolgáltató AuthnRequest** szövegmező.

    e. A klasszikus Azure AD portálon, másolja a **egyetlen Sign-Out URL-címe** értékét, és illessze be azt a **identitásszolgáltató SingleLogoutRequest** szövegmező.

    f. Az a **ServiceNow kezdőlap** szövegmezőhöz a ServiceNow példány kezdőlap URL-címét.

    > [!NOTE] 
    > A ServiceNow példány kezdőlapon összefűzése a **ServieNow bérlői URL-cím** és **/navpage.do** (pl.:`https://fabrikam.service-now.com/navpage.do`).

    g. Az a **Entitásazonosító / kibocsátó** szövegmezőhöz a ServiceNow bérlői URL-címét.

    h. Az a **célközönség URL-cím** szövegmezőhöz a ServiceNow bérlői URL-címét. 

    i. Az a **protokoll kötése az IDP SingleLogoutRequest** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:2.0:bindings:HTTP-átirányítási**.

    j. Írja be a NameID házirend szövegmező **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: nem meghatározott**.

    k. Kapcsolja ki **hozzon létre egy AuthnContextClass**.

    l. Az a **AuthnContextClassRef metódus**, típus `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Ez csak akkor szükséges, ha egyetlen szervezet felhőbeli. Használata a helyszíni AD FS vagy MFA hitelesítés akkor ne konfigurálja ezt az értéket. 

    m. A **óra döntés** szövegmezőhöz típus **60**.

    n. Mint **egyszeri bejelentkezést a parancsfájl**, jelölje be **MultiSSO_SAML2_Update1**.

    o. Mint **x509 tanúsítvány**, válassza ki az előző lépésben létrehozott tanúsítványt.

    p. Kattintson a **nyújt**. 

1. A klasszikus Azure AD portálon válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **következő**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "egyszeri bejelentkezés konfigurálása")

2. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "egyszeri bejelentkezés konfigurálása")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>A ServiceNow expressz az Azure AD-egyszeri bejelentkezés konfigurálása
1. A klasszikus Azure AD portálon a a **ServiceNow** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC749323.png "egyszeri bejelentkezés konfigurálása")

2. Az a **hová felhasználók számára történő bejelentkezést ServiceNow** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC749324.png "egyszeri bejelentkezés konfigurálása")

3. Az a **Alkalmazásbeállítások konfigurálása** lapon, a következő lépésekkel:
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC769497.png "alkalmazás URL-CÍMEK konfigurálása")
   
    a. az a **ServiceNow bejelentkezési URL-cím** szövegmező, írja be az URL-címet használják-e a felhasználók bejelentkezés a ServiceNow alkalmazáshoz a következő mintát: `https://<instance-name>.service-now.com`.
   
    b. Az a **kiállítójának URL-címe** szövegmező, írja be az URL-cím segítségével a felhasználók bejelentkezés a ServiceNow alkalmazásra mintát a következő `https://<instance-name>.service-now.com`.
   
    c. Kattintson a **Tovább** gombra

4. Kattintson a **manuálisan állítsa be az alkalmazását az egyszeri bejelentkezés**, majd kattintson a **következő** és kövesse az alábbi lépéseket.
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "alkalmazás URL-CÍMEK konfigurálása")

5. A a **konfigurálhatja az egyszeri bejelentkezés ServiceNow** kattintson **tanúsítvánnyal letöltés**, mentse helyileg a számítógépen a tanúsítvány fájlt, és kattintson **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC749325.png "egyszeri bejelentkezés konfigurálása")

6. Bejelentkezés a ServiceNow Express alkalmazást rendszergazdaként.

7. A bal oldali navigációs ablaktábláján kattintson **egyszeri bejelentkezés**.  
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "alkalmazás URL-CÍMEK konfigurálása")

8. Az a **egyszeri bejelentkezés** párbeszédpanel, kattintson a jobb felső konfigurációs ikonjára, és állítsa be a következő tulajdonságokat:
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "alkalmazás URL-CÍMEK konfigurálása")
   
    a. Váltás **több szolgáltató SSO engedélyezése** jobbra.
   
    b. Váltás **engedélyezése hibakeresési naplózás több szolgáltatójának SSO integrációs** jobbra.
   
    c. A **a mezőt, a felhasználó táblázat...**  szövegmezőhöz típus **felhasználónév**.
9. Az a **egyszeri bejelentkezés** párbeszédpanel, kattintson a **új tanúsítvány hozzáadása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "egyszeri bejelentkezés konfigurálása")
10. Az a **X.509-tanúsítványokat** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "egyszeri bejelentkezés konfigurálása")
    
    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (pl.: **TestSAML2.0**).
    
    b. Válassza ki **aktív**.
    
    c. Mint **formátum**, jelölje be **PEM**.
    
    d. Mint **típus**, jelölje be **megbízható tároló Cert**.
    
    e. Hozzon létre egy Base64-kódolású fájlt a letöltött tanúsítvány.
    
    > [!NOTE]
    > További részletekért lásd: [bináris tanúsítvány szöveg fájlba való konvertálása](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. A Base64 kódolású tanúsítvány megnyitása a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be azt a **PEM tanúsítvány** szövegmező.
    
    g. Kattintson a **frissítés**.
11. Az a **egyszeri bejelentkezés** párbeszédpanel, kattintson a **hozzáadása új IdP**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "egyszeri bejelentkezés konfigurálása")
12. Az a **hozzáadása új identitásszolgáltató** párbeszédpanelen, a **identitásszolgáltató konfigurálása**, hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "egyszeri bejelentkezés konfigurálása")

    a. az a **neve** szövegmező, adja meg a konfiguráció nevét (pl.: **SAML 2.0**).

    b. A klasszikus Azure AD portálon, másolja a **identitás Szolgáltatóazonosító** értékét, és illessze be azt a **identitási szolgáltató URL-cím** szövegmező.

    c. A klasszikus Azure AD portálon, másolja a **hitelesítési kérelem URL-cím** értékét, és illessze be azt a **identitásszolgáltató AuthnRequest** szövegmező.

    d. A klasszikus Azure AD portálon, másolja a **egyetlen Sign-Out URL-címe** értékét, és illessze be azt a **identitásszolgáltató SingleLogoutRequest** szövegmező.

    e. Mint **szolgáltató Identitástanúsítvány**, válassza ki az előző lépésben létrehozott tanúsítványt.


1. Kattintson a **speciális beállítások**, majd a **további identitás szolgáltató tulajdonságai**, hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "egyszeri bejelentkezés konfigurálása")
   
    a. Az a **protokoll kötése az IDP SingleLogoutRequest** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:2.0:bindings:HTTP-átirányítási**.
   
    b. Az a **NameID házirend** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: nem meghatározott**.    
   
    c. Az a **AuthnContextClassRef metódus**, típus **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Kapcsolja ki **hozzon létre egy AuthnContextClass**.

2. A **további szolgáltató tulajdonságai**, hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "egyszeri bejelentkezés konfigurálása")
   
    a. Az a **ServiceNow kezdőlap** szövegmezőhöz a ServiceNow példány kezdőlap URL-címét.
   
    > [!NOTE]
    > A ServiceNow példány kezdőlapon összefűzése a **ServieNow bérlői URL-cím** és **/navpage.do** (pl.: `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. Az a **Entitásazonosító / kibocsátó** szövegmezőhöz a ServiceNow bérlői URL-címét.
   
    c. Az a **célközönség URI** szövegmezőhöz a ServiceNow bérlői URL-címét. 
   
    d. A **óra döntés** szövegmezőhöz típus **60**.
   
    e. Az a **felhasználói mező** szövegmezőhöz típus **e-mail** vagy **felhasználónév**, attól függően, melyik mezőt a ServiceNow környezetben a felhasználók egyedi azonosítására szolgál.
   
    > [!NOTE]
    > Az Azure AD configue hozható létre az Azure AD felhasználói azonosító (egyszerű felhasználónév) vagy az e-mail cím a SAML-jogkivonat egyedi azonosítóként a is a **ServiceNow > attribútumok > egyszeri bejelentkezés** a klasszikus Azure portálra, és a kívánt mezőt leképezési a **nameidentifier** attribútum. A kiválasztott attribútumnak az Azure AD (pl. egyszerű felhasználónév) tárolt érték meg kell egyeznie a megadott mező (pl. felhasználónév) ServiceNow tárolt érték
    > 
    > 
   
    f. Kattintson a **Save** (Mentés) gombra. 

3. A klasszikus Azure AD portálon válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **következő**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "egyszeri bejelentkezés konfigurálása")

4. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "egyszeri bejelentkezés konfigurálása")

## <a name="configuring-user-provisioning"></a>Felhasználók átadására
Ez a szakasz célja engedélyezése a felhasználók átadása, az Active Directory felhasználói fiókokat a ServiceNow felvázoló.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:
1. A klasszikus Azure felügyeleti portálon a a **ServiceNow** alkalmazás integráció lapján, kattintson a **konfigurálja, a felhasználók átadása**. 
   
    ![A felhasználók átadása](./media/active-directory-saas-servicenow-tutorial/IC769498.png "a felhasználók átadása")

2. Az a **ServiceNow hitelesítő adatait ahhoz, hogy a felhasználók automatikus átadása** lapján adja meg a következő konfigurációs beállításokat:
   
     a. Az a **ServiceNow példánynév** szövegmező, írja be a ServiceNow-példány nevét.
   
     b. Az a **ServiceNow rendszergazda felhasználóneve** szövegmező, írja be a ServiceNow rendszergazda fiók nevét.
   
     c. Az a **ServiceNow rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.
   
     d. Kattintson a **érvényesítése** a konfiguráció ellenőrzése.
   
     e. Kattintson a **következő** gombra kattintva nyissa meg a **további lépések** lap.
   
     f. Ha minden felhasználó számára az alkalmazás telepítéséhez, jelölje be "**a könyvtárban ezen alkalmazás összes felhasználói fiók automatikusan létesítsen**". 
   
    ![További lépések](./media/active-directory-saas-servicenow-tutorial/IC698804.png "további lépések")
   
     g. Az a **további lépések** kattintson **Complete** kattintva mentse a konfigurációt.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ebben a szakaszban Britta Simon neve a klasszikus portálon tesztfelhasználó létrehozása.

![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. A felhasználó típusát válassza ki az új felhasználót a szervezetében.
   
    b. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.
   
    c. Kattintson a **Tovább** gombra.

6. Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel:
   
   ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. Az a **Utónév** szövegmezőhöz típus **Britta**.  
   
   b. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**.
   
   c. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.
   
   d. Az a **szerepkör** listáról válassza ki **felhasználói**.
   
   e. Kattintson a **Tovább** gombra.

7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Jegyezze fel az értéket a **új jelszó**.
   
    b. Kattintson a **Befejezés** gombra.   

### <a name="creating-a-servicenow-test-user"></a>A ServiceNow tesztfelhasználó létrehozása
Ebben a szakaszban a ServiceNow Britta Simon nevű felhasználó hoz létre. Ebben a szakaszban a ServiceNow Britta Simon nevű felhasználó hoz létre. Ha nem tudja a felhasználó hozzáadása a ServiceNow vagy ServiceNow Express fiókját, lépjen kapcsolatba a terméktámogatással a ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése
Ebben a szakaszban engedélyezze Britta Simon szerint saját hozzáférést biztosít a ServiceNow Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**A ServiceNow Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portál megnyitásához az alkalmazások megtekintése a könyvtár nézetben kattintson **alkalmazások** a felső menüben.
   
    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ServiceNow**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. Kattintson a felső menüben **felhasználók**.
   
    ![Felhasználó hozzárendelése][203] 

4. Válassza a minden felhasználó lista **Britta Simon**.

5. Kattintson az alsó eszköztár **hozzárendelése**.
   
    ![Felhasználó hozzárendelése][205]

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ServiceNow csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a ServiceNow alkalmazásba.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
