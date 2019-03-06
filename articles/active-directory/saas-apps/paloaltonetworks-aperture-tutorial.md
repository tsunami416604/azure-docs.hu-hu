---
title: 'Oktatóanyag: A Palo Alto Networks - rekesze és az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto Networks - rekesze és között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41c514706a671f755eb5b0efd5e3eefa5052c6e2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450022"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Oktatóanyag: A Palo Alto Networks - rekesze és az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Palo Alto Networks - nyílás, az Azure Active Directoryval (Azure AD).

Futtató Palo Alto Networks - nyílás, az Azure AD integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Palo Alto Networks - rekesze és az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Palo Alto Networks - (egyszeri bejelentkezés) rekesze és az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

A Palo Alto Networks - nyílás, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Azure AD-előfizetés
- A Palo Alto Networks - rekesze és egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadása a Palo Alto Networks - rekesze és a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Hozzáadása a Palo Alto Networks - rekesze és a katalógusból
A Palo Alto Networks - rekesze és az Azure AD-integráció konfigurálása kell hozzáadnia a Palo Alto Networks - rekesze és a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Palo Alto Networks - katalógusból, rekesze és hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Palo Alto Networks - rekesze és**, jelölje be **Palo Alto Networks - rekesze és** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Palo Alto Networks - rekesze és az eredmények listájában](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés a Palo Alto Networks tesztelése és konfigurálása,-rekesze és a egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a partner felhasználó Palo Alto Networks - rekesze és a egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Palo Alto Networks - hivatkozás kapcsolatának rekesze és kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Palo Alto Networks - nyílás, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy futtató Palo Alto Networks - rekesze és tesztfelhasználó](#create-a-palo-alto-networks---aperture-test-user)**  – van egy Britta Simon megfelelője a Palo Alto Networks - nyílás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Palo Alto Networks - rekesze és alkalmazás az egyszeri bejelentkezés konfigurálása.

**A Palo Alto Networks - nyílás, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Palo Alto Networks - rekesze és** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

1. Az a **Palo Alto Networks - rekesze és tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Futtató Palo Alto Networks - rekesze és tartomány és URL-címek egyszeri bejelentkezési adatait](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Futtató Palo Alto Networks - rekesze és tartomány és URL-címek egyszeri bejelentkezési adatait](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Palo Alto Networks - rekesze és ügyfél-támogatási csapatának](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


1. Az a **Palo Alto Networks - rekesze és konfigurációs** területén kattintson **konfigurálása Palo Alto Networks - rekesze és** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![A konfigurálás hivatkozás](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

1. Egy másik böngészőablakban, jelentkezzen be a Palo Alto Networks - rekesze és rendszergazdaként.

1. A felső menüsávon kattintson **beállítások**.

    ![A beállítások lapon](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

1. Navigáljon a **alkalmazás** szakaszban kattintson **hitelesítési** alkotnak a bal oldali menüben.

    ![A hitelesítés lap](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
1. Az a **hitelesítési** oldalon tegye a következőket:
    
    ![A hitelesítési lapon](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Ellenőrizze a **engedélyezése egyetlen bejelentkezési-On(Supported SSP Providers are Okta, One login)** a **egyszeri bejelentkezés** mező.

    b. Az a **identitás Szolgáltatóazonosító** szövegmezőbe, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt.

    c. Kattintson a **fájl kiválasztása** töltse fel a letöltött az Azure AD-ből a **szolgáltató Identitástanúsítványt** mező.

    d. Az a **Identity Provider egyszeri bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    e. Tekintse át a adatainak identitásszolgáltatót **rekesze és Info** szakaszt, és töltse le a tanúsítványt a **rekesze és kulcs** mező.

    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>A Palo Alto Networks - rekesze és tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Palo Alto Networks - rekesze és a felhasználó hoz létre. Együttműködve [Palo Alto Networks - rekesze és ügyfél-támogatási csapatának](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) felhasználót is hozzáadhat a a Palo Alto Networks - rekesze és platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto Networks - rekesze és Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése a Palo Alto Networks - nyílás, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Palo Alto Networks - rekesze és**.

    ![A futtató Palo Alto Networks - alkalmazásainak listájában rekesze és hivatkozás](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Palo Alto Networks - rekesze és csempe a hozzáférési panelen kattintva, kell lekérése automatikusan bejelentkezett, a Palo Alto Networks - rekesze és alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

