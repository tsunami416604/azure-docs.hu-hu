---
title: 'Oktatóanyag: Azure Active Directoryval integrált TOPdesk - biztonságos |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és TOPdesk - biztonságos között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 132803377554ecb4494d8b4ca6db7aa2f5f3aff1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225228"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Azure Active Directoryval integrált TOPdesk - biztonságos

Ebben az oktatóanyagban elsajátíthatja TOPdesk - integrálása az Azure Active Directoryval (Azure AD) biztonságos.

TOPdesk - biztonságos integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér TOPdesk - biztonságos szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett TOPdesk - biztonságos (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a TOPdesk - biztonságos, kell a következő elemek:

- Az Azure AD szolgáltatásra
- A TOPdesk - biztonságos egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Hozzáadás TOPdesk - biztonságos a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-topdesk---secure-from-the-gallery"></a>Hozzáadás TOPdesk - biztonságos a gyűjteményből
Biztonságos TOPdesk - integrációjának konfigurálása az Azure AD-be kell TOPdesk hozzáadása – biztonságos a gyűjteményből a felügyelt SaaS listájának alkalmazásokat.

**TOPdesk - hozzáadása a gyűjteményből biztonságos, hajtsa végre a következő lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **TOPdesk - biztonságos**, jelölje be **TOPdesk - biztonságos** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![TOPdesk - az eredménylistában biztonságos](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a TOPdesk tesztelése és konfigurálása – a "Britta Simon" nevű tesztfelhasználó biztonságos alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a TOPdesk - biztonságos tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a TOPdesk - biztonságos hivatkozás kapcsolatának kell létrehozni.

A TOPdesk - biztonságos, az értéket a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a TOPdesk - tesztelése és konfigurálása biztonságos, kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy TOPdesk - biztonságos tesztfelhasználó](#create-a-topdesk---secure-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a TOPdesk - biztonságos, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a TOPdesk - biztonságos alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD az egyszeri bejelentkezés konfigurálása TOPdesk - biztonságos, hajtsa végre a következő lépéseket:**

1. Az Azure portálon a a **TOPdesk - biztonságos** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. Az a **TOPdesk - biztonságos tartománya és URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk TOPdesk - biztonságos tartomány és az URL-címek](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.topdesk.net`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Válasz URL-CÍMEN kifejtett oktatóanyag későbbi részében. Ügyfél [TOPdesk - biztonságos ügyfél-támogatási csoport](http://www.topdesk.com/us/support) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/topdesk-secure-tutorial/tutorial_general_400.png)

6. A a **TOPdesk - konfiguráció biztonságos** kattintson **konfigurálása TOPdesk - biztonságos** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![TOPdesk - biztonságos konfigurációt](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Jelentkezzen be a **TOPdesk - biztonságos** vállalati hely rendszergazdaként.

8. Az a **TOPdesk** menüben kattintson a **beállítások**.

    ![Beállítások](./media/topdesk-secure-tutorial/ic790598.png "beállítások")

9. Kattintson a **bejelentkezési beállítások**.

    ![Bejelentkezési beállítások](./media/topdesk-secure-tutorial/ic790599.png "bejelentkezési beállítások")

10. Bontsa ki a **bejelentkezési beállítások** menüben, majd kattintson **általános**.

    ![Általános](./media/topdesk-secure-tutorial/ic790600.png "általános")

11. Az a **biztonságos** szakasza a **SAML bejelentkezési** konfigurációs szakaszban, hajtsa végre a következő lépéseket:

    ![Műszaki beállítások](./media/topdesk-secure-tutorial/ic790855.png "műszaki beállításai")
   
    a. Kattintson a **letöltése** a nyilvános metaadatait tartalmazó fájl letöltéséhez, és mentse helyileg a számítógépen.
   
    b. Nyissa meg a metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópont.
    
    ![Helyességi feltétel fogyasztói szolgáltatás](./media/topdesk-secure-tutorial/ic790856.png "helyességi feltétel fogyasztói szolgáltatás")
   
    c. Másolás a **AssertionConsumerService** értékét, illessze be ezt az értéket a válasz URL-CÍMEN textbox **TOPdesk - biztonságos tartománya és URL-címek** szakasz.

12. A tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:
    
    ![Tanúsítvány](./media/topdesk-secure-tutorial/ic790606.png "tanúsítvány")
    
    a. Nyissa meg a letöltött metaadatfájl Azure-portálon.

    b. Bontsa ki a **RoleDescriptor** csomópont egy **xsi: type** a **táplált: ApplicationServiceType**.

    c. Másolja a értékének a **x.509** csomópont.

    d. Mentse a másolt **x.509** érték helyileg a fájlt a számítógépen.

13. Az a **nyilvános** kattintson **Hozzáadás**.
    
    ![Adja hozzá](./media/topdesk-secure-tutorial/ic790607.png "hozzáadása")

14. Az a **SAML-alapú konfigurációs Segéd** párbeszédpanel lapon, a következő lépésekkel:
    
    ![SAML-alapú konfigurációs Segéd](./media/topdesk-secure-tutorial/ic790608.png "SAML-alapú konfigurációs Segéd")
    
    a. Töltse fel az Azure-portálon, a letöltött metaadatfájl **összevonási metaadatok**, kattintson a **Tallózás**.

    b. Töltse fel a tanúsítványfájlt, a **tanúsítvány (RSA)**, kattintson a **Tallózás**.

    c. Fel kell töltenie az embléma fájlt során kapott azonosítóértékeket TOPdesk terméktámogatással a **embléma ikon**, kattintson a **Tallózás**.

    d. Az a **felhasználói név attribútum** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Az a **megjelenített név** szövegmező, adja meg a konfiguráció nevét.

    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/topdesk-secure-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/topdesk-secure-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/topdesk-secure-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/topdesk-secure-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-topdesk---secure-test-user"></a>Hozzon létre egy TOPdesk - biztonságos tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók TOPdesk - be tudjon jelentkezni biztonságos, akkor ki kell építenie TOPdesk - biztonságos be.  
TOPdesk - esetén biztonságos, egy kézi tevékenység.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **TOPdesk - biztonságos** vállalati hely rendszergazdaként.
2. Kattintson a felső menüben **TOPdesk \> új \> támogatófájljait \> operátor**.
   
    ![Operátor](./media/topdesk-secure-tutorial/ic790610.png "operátor")

3. Az a **új operátor** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új operátor](./media/topdesk-secure-tutorial/ic790611.png "új operátor")
   
    a. Kattintson a **általános** fülre.
   
    b. Az a **vezetékneve** szövegmezőhöz, például a felhasználó vezetékneve típusú **Simon**.
   
    c. Válassza ki a **hely** a fiók a **hely** szakasz.
   
    d. Az a **bejelentkezési név** a szövegmező a **TOPdesk bejelentkezési** területen írja be a felhasználó bejelentkezési nevét.
   
    e. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más TOPdesk - biztonságos felhasználói fiók létrehozása eszközök vagy TOPdesk - felhasználói fiókok AAD kiépítése biztonságos által nyújtott API-kat is használhatja.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon használandó Azure egyszeri bejelentkezés által biztosított TOPdesk - biztonságos hozzáférés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése TOPdesk - biztonságos, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **TOPdesk - biztonságos**.

    ![A TOPdesk – az alkalmazások listáját a biztonságos kapcsolat](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

TOPdesk - biztonságos csempe a hozzáférési panelen kattintva meg kell beolvasása automatikusan bejelentkezett a TOPdesk - biztonságos alkalmazás számára.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-secure-tutorial/tutorial_general_203.png

