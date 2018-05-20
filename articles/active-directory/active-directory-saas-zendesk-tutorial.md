---
title: 'Oktatóanyag: Azure Active Directoryval integrált Zendesk |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Zendesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: bdb52039879c23c418af002fa8525e669a715022
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Oktatóanyag: Azure Active Directoryval integrált Zendesk

Ebben az oktatóanyagban elsajátíthatja Zendesk integrálása az Azure Active Directory (Azure AD).

Zendesk integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáférhet ehhez az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Zendesk (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az Azure AD-integrációs konfigurálni, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Zendesk egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Zendesk hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a gyűjteményből
Az Azure AD integrálása a Zendesk konfigurálásához kell hozzáadnia ehhez a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá ehhez a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Zendesk**, jelölje be **Zendesk** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Ehhez az eredménylistában](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Zendesk.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Zendesk tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Zendesk közötti kapcsolat kapcsolatot kell létrehozni.

A Zendesk, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Zendesk tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Zendesk tesztfelhasználó létrehozása](#create-a-zendesk-test-user)**  - való Britta Simon valami Zendesk, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Zendesk-alkalmazásban.

**A Zendesk konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Zendesk** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Az a **Zendesk-tartomány és az URL-címek** területen tegye a következőket:

    ![Zendesk-tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.zendesk.com`

    b. Az a **azonosító** szövegmező, írja be az értéket a következő minta használatával: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Zendesk ügyfél-támogatási csoport](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png)

5. Ehhez a SAML helyességi feltételek vár egy meghatározott formátumban. Nem kötelező SAML attribútum, de opcionálisan hozzáadhat egy attribútumot **felhasználói attribútumok** következő szakasz az alábbi lépéseket: 

     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés addattb konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.
 
    > [!NOTE] 
    > A bővítményattribútumokat használatával ad hozzá az attribútumokat, amelyek nem az alapértelmezés szerint az Azure AD-ben. Kattintson a [állítható be SAML felhasználói attribútumok](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) teljes listáját a beolvasandó SAML attribútumok, amelyek **Zendesk** fogad el.  

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_general_400.png)

7. Az a **Zendesk konfigurációs** területén kattintson **konfigurálása Zendesk** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Zendesk-konfiguráció](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a Zendesk vállalati webhely rendszergazdaként.

9. Kattintson a **Admin**.

10. A bal oldali navigációs ablaktáblán kattintson **beállítások**, és kattintson a **biztonsági**.

11. Az a **biztonsági** lapon, a következő lépésekkel: 
   
     ![Biztonsági](./media/active-directory-saas-zendesk-tutorial/ic773089.png "biztonsági")

    ![Egyszeri bejelentkezés](./media/active-directory-saas-zendesk-tutorial/ic773090.png "egyszeri bejelentkezés")

     a. Kattintson a **Admin & ügynökök** fülre.

     b. Válassza ki **egyszeri bejelentkezés (SSO) és a SAML**, majd válassza ki **SAML**.

     c. A **SAML SSO URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta. 

     d. A **távoli kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
        
     e. A **tanúsítvány-ujjlenyomat** szövegmező, illessze be a **ujjlenyomat** érték tanúsítvány, amely az Azure-portálon másolta.
     
     f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-zendesk-test-user"></a>Zendesk tesztfelhasználó létrehozása

Ahhoz, hogy be tudjon jelentkezni az Azure AD-felhasználók **Zendesk**, akkor ki kell építenie a **Zendesk**.  
Attól függően, hogy az alkalmazások szerepkörrel a normális működés:

 1. **Végfelhasználói** fiókok bejelentkezéskor automatikusan törlődnek.
 2. **Ügynök** és **Admin** fiókokat kell manuálisan építhető **Zendesk** bejelentkezés előtt.
 
**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Zendesk** bérlő.

2. Válassza ki a **Ügyféllista** fülre.

3. Válassza ki a **felhasználói** fülre, majd **Hozzáadás**.
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-zendesk-tutorial/ic773632.png "felhasználó hozzáadása")
4. Típus a **neve** és **E-mail** rendelkezés szeretne, és kattintson a meglévő Azure AD fiók **mentése**.
   
    ![Új felhasználó](./media/active-directory-saas-zendesk-tutorial/ic773633.png "új felhasználó")

> [!NOTE]
> Bármely más Zendesk felhasználói fiók létrehozása eszközök, vagy rendelkezés AAD felhasználói fiókokhoz Zendesk által nyújtott API-k.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zendesk Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Zendesk Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Zendesk**.

    ![Az alkalmazások listáját a Zendesk-hivatkozás](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Zendesk csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Zendesk-alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png

