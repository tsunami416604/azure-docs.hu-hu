---
title: 'Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks - GlobalProtect |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto Networks - GlobalProtect között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: d3fdf52d07faa4242a0267ebc929946bbc95418a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427618"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks - GlobalProtect

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Palo Alto Networks - GlobalProtect az Azure Active Directoryval (Azure AD).

Futtató Palo Alto Networks - GlobalProtect az Azure AD integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Palo Alto Networks - GlobalProtect Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Palo Alto Networks - GlobalProtect (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Palo Alto Networks - GlobalProtect, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Palo Alto Networks - GlobalProtect egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadása a Palo Alto Networks - GlobalProtect a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Hozzáadása a Palo Alto Networks - GlobalProtect a katalógusból
A Palo Alto Networks - GlobalProtect be az Azure AD-integráció konfigurálása kell hozzáadnia a Palo Alto Networks - GlobalProtect a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Palo Alto Networks - GlobalProtect katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Palo Alto Networks - GlobalProtect**, jelölje be **Palo Alto Networks - GlobalProtect** eredmény panelen kattintson a **Hozzáadás** az alkalmazás hozzáadására szolgáló gomb .

    ![Futtató Palo Alto Networks - GlobalProtect a találatok listájában](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés a Palo Alto Networks tesztelése és konfigurálása,-GlobalProtect "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Palo Alto Networks - GlobalProtect tartozó felhasználó Mi az a felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Palo Alto Networks - hivatkozás kapcsolatának GlobalProtect kell létrehozni.

Palo Alto Networks - GlobalProtect, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Palo Alto Networks - GlobalProtect, szüksége a következő építőelemeit végrehajtásához:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy futtató Palo Alto Networks - GlobalProtect tesztfelhasználó](#create-a-palo-alto-networks---globalprotect-test-user)**  – van egy Britta Simon megfelelője a Palo Alto Networks - GlobalProtect, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Palo Alto Networks - GlobalProtect alkalmazás egyszeri bejelentkezés konfigurálása.

**A Palo Alto Networks - GlobalProtect, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Palo Alto Networks - GlobalProtect** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

1. Az a **Palo Alto Networks - GlobalProtect tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Palo Alto Networks - GlobalProtect tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<Customer Firewall URL>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Palo Alto Networks - GlobalProtect ügyfél-támogatási csapatának](https://support.paloaltonetworks.com/support) beolvasni ezeket az értékeket. 
 
1. Palo Alto Networks - GlobalProtect alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumnak. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | felhasználónév | User.userPrincipalName |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket. Azt az értéket példaként user.userprincipalname leképezett, de leképezheti a megfelelő értékkel. 
    
    d. Kattintson a **Ok**


1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban rendszergazdaként nyissa meg a Palo Alto hálózatok tűzfal felügyeleti felhasználói Felületéhez.

1. Kattintson a **eszköz**.

    ![Rendszert futtató Palo Alto egyszeri bejelentkezés konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Válassza ki **identitásszolgáltató SAML** a bal oldali navigációs sávot, majd kattintson a "Importálása" a metaadatait tartalmazó fájl importálása.

    ![Rendszert futtató Palo Alto egyszeri bejelentkezés konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Hajtsa végre a következő műveleteket az importálási ablakot a

    ![Rendszert futtató Palo Alto egyszeri bejelentkezés konfigurálása](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Az a **profilnév** szövegmezőben adjon meg egy nevet például az Azure AD GlobalProtect.
    
    b. A **Identity Provider metaadatok**, kattintson a **Tallózás** , és válassza ki a metadata.xml fájlt, amely az Azure Portalról letöltött
    
    c. Kattintson az **OK** gombra

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/paloaltoglobalprotect-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/paloaltoglobalprotect-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/paloaltoglobalprotect-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>A Palo Alto Networks - GlobalProtect tesztfelhasználó létrehozása

Futtató Palo Alto Networks - GlobalProtect Just-in-time támogatja a felhasználók átadásának így a felhasználók automatikusan létrejön a rendszer a sikeres hitelesítés után ha már nem létezik. Nem kell itt bármely művelet elvégzésére. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto Networks - GlobalProtect Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése a Palo Alto Networks - GlobalProtect, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Palo Alto Networks - GlobalProtect**.

    ![A futtató Palo Alto Networks - alkalmazásainak listájában GlobalProtect hivatkozás](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Palo Alto Networks - GlobalProtect csempe a hozzáférési panelen kattintva, kell lekérése automatikusan bejelentkezett, a Palo Alto Networks - GlobalProtect alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_203.png

