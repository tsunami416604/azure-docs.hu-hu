---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Mitel Connecttel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Mitel Connect között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 26a761708f56ff7aba8daf86d2991579e60291cb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870192"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Oktatóanyag: Az Azure Active Directory integrációja a Mitel MiCloud Connecttel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Mitel MiCloud Connectet az Azure Active Directoryval (Azure AD). A MiCloud Connect integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a MiCloud Connect-alkalmazásokhoz a vállalati hitelesítő adataikkal.
* Engedélyezheti, hogy a fiók felhasználói automatikusan bejelentkezhessenek a MiCloud Connectbe (Single Sign-On) az Azure AD-fiókjukkal.


Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció micloud Connecttel való konfigurálásához a következő elemekre van szükség:

* Azure AD-előfizetés

  Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Mitel MiCloud Connect fiók

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést (SSO).

* A Mitel Connect támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-mitel-connect-from-the-gallery"></a>Mitel Connect hozzáadása a galériából

A Mitel Connect Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Mitel Connectet a katalógusból a felügyelt SaaS-alkalmazások listájához az Azure Portalon.

**Ha hozzá szeretné adni a Mitel Connectet a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** kattintson a bal oldali navigációs panelen az **Azure Active Directory**elemre.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Kattintson **a Vállalati alkalmazások,** majd **az Összes alkalmazás**elemre.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Kattintson **az Új alkalmazás gombra.**

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. Írja be a **Mitel Connect** kifejezést a keresőmezőbe, kattintson a **Mitel Csatlakozás** az eredmények panelről elemre, majd a **Hozzáadás**gombra.

     ![Mitel Connect az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a MiCloud Connecttel egy **Britta Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a MiCloud Connect közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a MiCloud Connect szolgáltatással a következő lépéseket kell végrehajtania:

1. **[Konfigurálja a MiCloud Connect for SSO szolgáltatást az Azure AD-vel](#configure-micloud-connect-for-sso-with-azure-ad)** – hogy a felhasználók használhassák ezt a funkciót, és konfigurálhassák az egyszeri szolgáltatás beállításait az alkalmazás oldalon.
2. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
3. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
4. **[Hozzon létre egy Mitel MiCloud Connect tesztfelhasználót](#create-a-mitel-micloud-connect-test-user)** – ha britta Simon megfelelőjét szeretné létrehozni a MiCloud Connect-fiókjában, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>A MiCloud Connect konfigurálása az SSO szolgáltatáshoz az Azure AD-vel

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezését a MiCloud Connecthez az Azure Portalon, és konfigurálja a MiCloud Connect-fiókját úgy, hogy az Azure AD használatával engedélyezze az Egyszeri bejelentkezés használatát.

Konfigurálása MiCloud Connect with SSO for Azure AD, a legegyszerűbb, hogy nyissa meg az Azure Portal és a Mitel-fiók portál egymás mellett. Néhány adatot át kell másolnia az Azure Portalról a Mitel-fiók portálra, másokat pedig a Mitel-fiók portálról az Azure Portalra.


1. A konfigurációs lap megnyitásához az [Azure Portalon](https://portal.azure.com/)tegye a következőket:

    a. A **Mitel Connect** alkalmazásintegrációs lapon kattintson **az Egyszeri bejelentkezés gombra.**

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

    b. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen kattintson az **SAML gombra.**

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)
    
    Megjelenik az SAML-alapú bejelentkezési lap.

2. A konfigurációs párbeszédpanel megnyitásához a Mitel-fiók portálon tegye a következőket:

    a. Kattintson a **Telefonrendszer** menü **Bővítményfunkciók parancsára.**

    b. Az egyszeri **bejelentkezés**jobb oldalán kattintson az Aktiválás vagy a **Beállítások gombra.** **Activate**
    
    Megjelenik az Egyszeri bejelentkezés beállításainak összekapcsolása párbeszédpanel.
    
3. Jelölje be az **Egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.
    ![kép](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Az Azure Portalon kattintson a **Szerkesztés** ikonra az **alapszintű SAML konfigurációs** szakaszban.
    ![kép](common/edit-urls.png)

    Megjelenik az Alapvető SAML konfiguráció párbeszédpanel.

5.  Másolja az URL-címet a **Mitel-fiók portál Mitel-azonosító (entitásazonosító)** mezőjéből, és illessze be az Azure Portal **azonosító (entitásazonosítója)** mezőjébe.

6. Másolja az URL-címet a Mitel-fiók portál **Válasz URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőjéből, és illessze be az Azure Portal **Válasz URL-címe (helyességi fogyasztói szolgáltatás URL)** mezőjébe.  
   ![kép](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. A **Bejelentkezés az URL-címmezőbe** írja be az alábbi URL-címek egyikét:

    * **https://portal.shoretelsky.com**- a Mitel Account portál alapértelmezett Mitel alkalmazásként való használata
    * **https://teamwork.shoretel.com**- a Teamwork használata alapértelmezett Mitel alkalmazásként

    **Megjegyzés:** Az alapértelmezett Mitel alkalmazás az alkalmazás elérhető, amikor a felhasználó rákattint a Mitel Connect csempe a hozzáférési panelen. Ez is az alkalmazás érhető el, ha az Azure AD-ből tesztbeállítást végez.

8. Kattintson a **Mentés** gombra az Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén.

9. Az Azure Portal **SAML-alapú bejelentkezési** lapjának **SAML-aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **Tanúsítvány (Base64)** mellett az **aláíró tanúsítvány** letöltéséhez és a számítógépre mentéséhez.
    ![kép](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Nyissa meg az Aláíró tanúsítvány fájlt egy szövegszerkesztőben, másolja a fájl összes adatát, majd illessze be az adatokat a Mitel-fiók portál **Aláíró tanúsítvány** mezőjébe. 
    ![kép](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Az Azure **Portal SAML-alapú bejelentkezési** lapjának **Beállítási Mitel Connect** szakaszában tegye a következőket:

    a. Másolja az URL-címet a **Bejelentkezési URL-cím** mezőből, és illessze be a **Mitel-fiók portál Bejelentkezési URL-mezőjébe.**

    b. Másolja az URL-címet az **Azure AD-azonosító** mezőből, és illessze be a Mitel-fiók portál **entitásazonosító** mezőjébe.
    ![kép](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Kattintson a **Mentés** gombra a Mitel-fiók portál **On Single Sign-On Settings (Egyszeri bejelentkezési beállítások csatlakoztatása)** párbeszédpanelen.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy Britta Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portalon a bal oldali ablaktáblában kattintson az **Azure Active Directory**elemre, kattintson a **Felhasználók**elemre, majd a Minden **felhasználó parancsra.**

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Kattintson az **Új felhasználó** elemre a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságai párbeszédpanelen tegye a következő lépéseket:

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**nevet.
  
    b. A **Felhasználónév** mezőbe írja\<be\>brittasimon@ cégtartományt. \<kiterjesztés.\>  
Például: BrittaSimon@contoso.com.

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Mitel Connect hez való hozzáférés biztosításával.

1. Az Azure Portalon kattintson a **Vállalati alkalmazások**elemre, majd az **Összes alkalmazás**elemre.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában kattintson a **Mitel Connect gombra.**

    ![A Mitel Connect hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben kattintson a **Felhasználók és csoportok**parancsra.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson **a Felhasználó hozzáadása**gombra, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** kattintson a Felhasználók és csoportok parancsra.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a **Felhasználók** listában, majd kattintson a képernyő alján a **Kijelölés** elemre.

6. Ha az SAML-feltételben szerepkörértéket vár, válassza ki a felhasználó megfelelő szerepkörét a **Szerepkör kiválasztása** párbeszédpanel listájából, majd kattintson a képernyő alján a **Kijelölés** gombra.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés gombra.**

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel MiCloud Connect tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a MiCloud Connect-fiókjában. A felhasználókat az egyszeri bejelentkezés előtt létre kell hozni és aktiválni kell.

A felhasználók mitel-fiókportálon való hozzáadásáról a Mitel Tudásbázis [Felhasználói hozzáadása](https://oneview.mitel.com/s/article/Adding-a-User-092815) című cikkben olvashat részletesen.

Hozzon létre egy felhasználót MiCloud Connect fiókjában a következő adatokkal:

  * **Név:** Britta Simon

* **Üzleti e-mail cím:**`brittasimon@<yourcompanydomain>.<extension>`   
(Példa: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Felhasználónév:**`brittasimon@<yourcompanydomain>.<extension>`  
(Példa: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); a felhasználó felhasználóneve általában megegyezik a felhasználó üzleti e-mail címével)

**MEGJEGYZÉS:** A felhasználó MiCloud Connect felhasználónevének meg kell egyeznie a felhasználó Azure-beli e-mail-címével.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen fogja tesztelni.

Ha a Hozzáférési panelen a Mitel Connect csempére kattint, a rendszer automatikusan átirányítja, hogy jelentkezzen be az alapértelmezettként konfigurált MiCloud Connect alkalmazásba a **Bejelentkezési URL-cím** mezőben. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
