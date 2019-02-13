---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az iQualify LMS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az iQualify LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25711bd09adf17fa82f9177f4badad723e590b12
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184193"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Oktatóanyag: Az Azure Active Directory integrációja az LMS iQualify

Ebben az oktatóanyagban elsajátíthatja, hogyan iQualify LMS integrálása az Azure Active Directory (Azure AD).

IQualify LMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá iQualify segítségével az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a iQualify (egyszeri bejelentkezés) segítségével az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az iQualify LMS, a következő elemek szükségesek:

- Azure AD-előfizetés
- Egy iQualify LMS egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. IQualify LMS hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-iqualify-lms-from-the-gallery"></a>IQualify LMS hozzáadása a katalógusból
Az Azure AD integrálása iQualify segítségével a konfigurálása, hozzá kell iQualify LMS a galériából a felügyelt SaaS-alkalmazások listájára.

**IQualify LMS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **iQualify LMS**, jelölje be **iQualify LMS** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában LMS iQualify](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az LMS alapján "Britta Simon." nevű tesztfelhasználó iQualify

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, milyen a partner felhasználó iQualify segítségével, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó iQualify hivatkozás kapcsolata segítségével kell létrehozni.

IQualify LMS, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az iQualify LMS tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy iQualify LMS tesztfelhasználót](#create-an-iqualify-lms-test-user)**  – egy megfelelője a Britta Simon iQualify segítségével, amely kapcsolódik az Azure AD felhasználói ábrázolása a rendelkeznie.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezése az Azure AD egyszeri bejelentkezés az Azure Portalon, és az LMS iQualify alkalmazásában egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iQualify LMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **iQualify LMS** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

1. Az a **iQualify LMS tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás Identitásszolgáltató által kezdeményezett módban konfigurálása:

    ![LMS tartomány és URL-címek egyszeri bejelentkezés iQualify információk](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: 
    | |
    |--|--|
    | Éles környezetben: `https://<yourorg>.iqualify.com/`|
    | Tesztelési környezetben: `https://<yourorg>.iqualify.io`|
    
    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: 
    | |
    |--|--|
    | Éles környezetben: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Tesztelési környezetben: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![LMS tartomány és URL-címek egyszeri bejelentkezés iQualify információk](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|--|
    | Éles környezetben: `https://<yourorg>.iqualify.com/login` |
    | Tesztelési környezetben: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [iQualify LMS ügyfél-támogatási csapatának](https://www.iqualify.com) beolvasni ezeket az értékeket. 

1. A iQualify LMS alkalmazás vár egy megadott formátumban megjeleníteni a Security Assertion Markup Language (SAML) helyességi feltételek. A jogcímek konfigurálása és kezelése az értékeket az attribútumok a **felhasználói attribútumok** iQualify application integration lap részében, az alábbi képernyőképen látható módon:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/iqualify-tutorial/atb.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanel az alábbi táblázatban szereplő minden egyes sorára hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "attribútum" | 

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/iqualify-tutorial/atb2.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/iqualify-tutorial/atb3.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**

    e. Ismételje meg a "a" és "d". a következő táblázat sorait. 

    > [!Note]
    > A lépések "a" és "d" ismétlődő a **person_id** attribútum **nem kötelező megadni**

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base-64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/iqualify-tutorial/tutorial_general_400.png)
    
1. Az a **iQualify LMS konfigurációs** területén kattintson **iQualify LMS konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![iQualify LMS-konfiguráció](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

1.  Nyisson meg egy új böngészőablakot, és a iQualify környezeti rendszergazdaként, majd jelentkezzen be.

1. Miután bejelentkezett, kattintson a jobb felső sarokban a profilképet, majd kattintson a **"Fiókbeállítások."**

    ![Fiókbeállítások](./media/iqualify-tutorial/setting1.png) 
1. A fiók beállítások területen, kattintson a menüszalagon a bal oldali menüben, és kattintson a **"INTEGRÁCIÓK."**
    
    ![INTEGRÁCIÓK](./media/iqualify-tutorial/setting2.png)

1. -INTEGRÁCIÓK alatt kattintson a a **SAML** ikonra.

    ![SAML-ikon](./media/iqualify-tutorial/setting3.png)

1. Az a **SAML-hitelesítési beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![SAML-hitelesítési beállítások](./media/iqualify-tutorial/setting4.png)

    a. Az a **SAML EGYSZERI bejelentkezés szolgáltatás URL-cím** mezőbe illessze be a **SAML egyszeri Sign‑On szolgáltatás URL-cím** az Azure AD alkalmazás-konfigurációs ablakából másolt érték.
    
    b. Az a **SAML KIJELENTKEZÉSI URL-címe** mezőbe illessze be a **Sign‑Out URL-cím** az Azure AD alkalmazás-konfigurációs ablakából másolt érték.
    
    c. Nyissa meg a letöltött tanúsítvány-fájlt a Jegyzettömbben, másolja a tartalmat és, majd illessze be azt a **nyilvános tanúsítvány** mezőbe.
    
    d. A **bejelentkezési GOMB FELIRATA** adja meg a gombot a bejelentkezési lapon megjelenő nevét.
    
    e. Kattintson a **SAVE** (Mentés) gombra.

    f. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/iqualify-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/iqualify-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/iqualify-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/iqualify-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-iqualify-lms-test-user"></a>Hozzon létre egy iQualify LMS tesztfelhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználó iQualify jön létre. iQualify LMS támogatja just‑in‑time felhasználók átadása, amely alapértelmezés szerint engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az iQualify, egy új jön létre, a hozzáférés iQualify LMS megkísérlésekor.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iQualify LMS való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel iQualify LMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **iQualify LMS**.

    ![Az alkalmazások listáját a iQualify LMS hivatkozás](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a iQualify LMS a hozzáférési panelen csempére kattint, az iQualify LMS alkalmazás bejelentkezési lapján szerezheti be. 

   ![bejelentkezési oldal](./media/iqualify-tutorial/login.png) 

Kattintson a **Azure AD bejelentkezési oldalára** gombra kattintva kell lekérése automatikusan bejelentkezett a iQualify LMS alkalmazásba.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png

