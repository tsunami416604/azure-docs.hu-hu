---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a FreshDeskszolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a FreshDesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227614"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Az Azure Active Directory integrációja a FreshDesk szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a FreshDesket az Azure Active Directoryval (Azure AD).
A FreshDesk integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a FreshDeskhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve freshdesk (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció freshdesk-del való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* FreshDesk egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* FreshDesk támogatja **SP** kezdeményezett SSO

## <a name="adding-freshdesk-from-the-gallery"></a>FreshDesk hozzáadása a galériából

A FreshDesk azure AD-be való integrálásának konfigurálásához hozzá kell adnia a FreshDesk-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A FreshDesk hozzáadása a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **FreshDesk**, válassza **a FreshDesk** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![FreshDesk az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a FreshDesk szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a FreshDesk közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a FreshDesk szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a FreshDesk Single Sign-On --hoz](#configure-freshdesk-single-sign-on)** configure a Single Sign-On beállítások at alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre FreshDesk teszt felhasználó](#create-freshdesk-test-user)** - egy megfelelője Britta Simon freshdesk, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének a FreshDesk szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **FreshDesk** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![FreshDesk tartomány és URL egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-címmezőbe** írja be az `https://<tenant-name>.freshdesk.com` URL-címet a következő minta használatával: vagy bármely más, a Freshdesk által javasolt érték.

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<tenant-name>.freshdesk.com` vagy bármely más, a Freshdesk által javasolt érték használatával.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a FreshDesk ügyféltámogatási csapatával,](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. A FreshDesk alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. A következő képernyőkép egy példát mutat erre. Az egyedi **felhasználói azonosító** alapértelmezett értéke a **user.userprincipalname,** de a FreshDesk azt várja, hogy ez le legyen képezve a felhasználó e-mail címével. Ehhez használhatja **a listából származó user.mail** attribútumot, vagy használhatja a megfelelő attribútumértéket a szervezet konfigurációja alapján. 

    ![image](common/edit-attribute.png)

6. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában a **Szerkesztés ikon** használatával szerkesztse a jogcímeket, vagy adja hozzá a jogcímeket az **Új jogcím hozzáadása** paragrafussal az SAML token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:
    
    | Név | Forrás attribútuma |
    | ---------------| --------------- |
    | Egyedi felhasználói azonosító | user.mail |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. Nyissa meg **a parancssort,** és futtassa a következő parancsokat:

    a. Írja `certutil.exe -dump FreshDesk.cer` be az értéket a parancssorba.

    > [!NOTE]
    > Itt **FreshDesk.cer** a tanúsítvány, amely az Azure Portalról letöltött.

    b. Másolja a **tanúsítvány kivonatoló (sha256)** értékét, és illessze be a jegyzettömbbe. 

9. A **FreshDesk beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-freshdesk-single-sign-on"></a>FreshDesk egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Freshdesk vállalati webhelyére rendszergazdaként.

2. Válassza a **Beállítások ikont,** és a **Biztonság** csoportban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/freshdesk-tutorial/IC776770.png "Egyszeri bejelentkezés")
  
    a. Az **Egyszeri bejelentkezés (SSO)** területen válassza **a Be**lehetőséget.

    b. Válassza **az SAML SSO**lehetőséget.

    c. Az **SAML bejelentkezési URL-cím** szövegdobozába illessze be **a bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    d. A **Kijelentkezés URL-cím** mezőjébe illessze be **a kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    e. A **Biztonsági tanúsítvány ujjlenyomat szövegdobozában** illessze be a **Cert Hash(sha256)** korábban beszerzett értékét.
  
    f. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a FreshDesk hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **FreshDesk**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **FreshDesk**lehetőséget.

    ![A FreshDesk hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-freshdesk-test-user"></a>FreshDesk tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a FreshDeskszolgáltatásba, ki kell építeni őket a FreshDesk-be.  
FreshDesk esetén kiépítése a manuális feladat.

**Felhasználói fiókok létesítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be **freshdesk-bérlőjéhez.**

2. A felső menüben kattintson a **Rendszergazda gombra.**

    ![Felügyelet](./media/freshdesk-tutorial/IC776772.png "Rendszergazda")

3. Az **Általános beállítások** lapon kattintson az **Ügyintézők gombra.**
  
    ![Ügynökök](./media/freshdesk-tutorial/IC776773.png "Ügynökök")

4. Kattintson **az Új ügynök gombra.**

    ![Új ügynök](./media/freshdesk-tutorial/IC776774.png "Új ügynök")

5. Az Ügynök adatai párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Ügynök adatai](./media/freshdesk-tutorial/IC776775.png "Ügynök adatai")

    a. Az **E-mail** szövegmezőbe írja be a kiépíteni kívánt Azure AD-fiók Azure AD-e-mail címét.

    b. A **Teljes név** mezőbe írja be a kiépíteni kívánt Azure AD-fiók nevét.

    c. A **Cím** mezőbe írja be a kiépíteni kívánt Azure AD-fiók címét.

    d. Kattintson a **Mentés** gombra.

    >[!NOTE]
    >Az Azure AD-fiók tulajdonosa kap egy e-mailt, amely tartalmaz egy hivatkozást, hogy erősítse meg a fiókot, mielőtt aktiválni.
    >
    >[!NOTE]
    >A Freshdesk-fiók létrehozása bármely más, a Freshdesk által biztosított api-k at használhat az Azure AD felhasználói fiókok FreshDesk beépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel FreshDesk csempéjére kattint, automatikusan be kell jelentkeznie arra a FreshDesk-be, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

