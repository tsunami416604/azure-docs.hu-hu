---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Cloud platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az SAP Cloud platform között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: c2738e1a6168440adee79ebaa599a313600153a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546763"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Oktatóanyag: Azure Active Directory integráció az SAP Cloud platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Cloud platformot Azure Active Directory (Azure AD) használatával.
Az SAP Cloud platform és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az SAP Cloud platformhoz hozzáférő Azure AD-t is vezérelheti.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAP Cloud platformba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SAP Cloud platformmal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAP Cloud platform egyszeri bejelentkezésre alkalmas előfizetés

Az oktatóanyag elvégzése után az SAP Cloud platformhoz rendelt Azure AD-felhasználók egyszeri bejelentkezést végezhetnek az alkalmazásba a [hozzáférési panel bevezetésének](../user-help/active-directory-saas-access-panel-introduction.md)használatával.

>[!IMPORTANT]
>Az egyszeri bejelentkezés teszteléséhez telepítenie kell egy alkalmazást, vagy elő kell fizetnie egy alkalmazásra az SAP Cloud platform-fiókjában. Ebben az oktatóanyagban egy alkalmazást helyezünk üzembe a fiókban.
> 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az SAP Cloud platform támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP Cloud platform hozzáadása a katalógusból

Az SAP Cloud platform Azure AD-integrációjának konfigurálásához hozzá kell adnia az SAP Cloud platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha SAP Cloud platformot szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAP Cloud platform**kifejezést, válassza az **SAP Cloud platform** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAP Cloud platform az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SAP Cloud platformon konfigurálhatja és tesztelheti egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az SAP Cloud platformon található kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés SAP Cloud platformmal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[SAP Cloud platform egyszeri bejelentkezésének konfigurálása](#configure-sap-cloud-platform-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[SAP Cloud platform tesztelési felhasználó létrehozása](#create-sap-cloud-platform-test-user)** – a Britta Simon-nek az SAP Cloud platformon, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés SAP Cloud platformon történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)az **SAP Cloud platform** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![SAP Cloud platform tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be a felhasználók által a **SAP Cloud platform** alkalmazásba való bejelentkezéshez használt URL-címet. Ez a védett erőforrás fiókra vonatkozó URL-címe az SAP Cloud platform alkalmazásban. Az URL-cím a következő mintán alapul: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Ez az az SAP Cloud platform-alkalmazás URL-címe, amelyhez a felhasználónak hitelesítenie kell magát.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Az **azonosító** szövegmezőben adja meg az SAP Cloud platformhoz tartozó URL-címet az alábbi minták egyikének használatával: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot az [SAP Cloud platform ügyfél-támogatási csapatával](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) Sign-On URL-cím és azonosító beszerzéséhez. A válasz URL-címe a megbízhatósági kezelés szakaszból szerezhető be, amelyet az oktatóanyag későbbi részében ismertetünk.
    > 
4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Az SAP Cloud platform egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az SAP Cloud platform pilótafülkére `https://account.<landscape host>.ondemand.com/cockpit` (például: https://account.hanatrial.ondemand.com/cockpit) ).

2. Kattintson a **megbízhatóság** fülre.
   
    ![Bizalom](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Bizalom")

3. A megbízhatósági kezelés szakaszban a **helyi szolgáltató**területen hajtsa végre a következő lépéseket:

    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Megbízhatóság kezelése")
   
    a. Kattintson a **Szerkesztés** gombra.

    b. A **konfiguráció típusa**mezőben válassza az **Egyéni**lehetőséget.

    c. A **helyi szolgáltató neve**beállításnál hagyja meg az alapértelmezett értéket. Másolja ezt az értéket, és illessze be az SAP Cloud platformhoz készült Azure AD-konfiguráció **azonosító** mezőjébe.

    d. Az **aláíró kulcs** és az **aláíró tanúsítvány** kulcspár létrehozásához kattintson a kulcspár **létrehozása**lehetőségre.

    e. **Elsődleges propagálásként**válassza a **Letiltva**lehetőséget.

    f. **Kényszerített hitelesítésként**válassza a **Letiltva**lehetőséget.

    : Kattintson a **Mentés** gombra.

4. A **helyi szolgáltató** beállításainak mentése után hajtsa végre a következőt a válasz URL-címének beszerzéséhez:
   
    ![Metaadatok beolvasása](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Metaadatok beolvasása")

    a. Töltse le az SAP Cloud platform metaadat-fájlját a **metaadatok lekérése**elemre kattintva.

    b. Nyissa meg a letöltött SAP Cloud platform metadata XML-fájlt, és keresse meg a **ns3: AssertionConsumerService** címkét.
 
    c. Másolja a **Location** attribútum értékét, majd illessze be az SAP Cloud platformhoz készült Azure ad **-konfiguráció válasz URL** mezőjébe.

5. Kattintson a **megbízható identitás-szolgáltató** fülre, majd a **megbízható identitás-szolgáltató hozzáadása**lehetőségre.
   
    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Megbízhatóság kezelése")
   
    >[!NOTE]
    >A megbízható identitás-szolgáltatók listájának kezeléséhez a helyi szolgáltató szakaszban kell megadnia az egyéni konfiguráció típusát. Az alapértelmezett konfigurációs típus esetében az SAP ID szolgáltatás nem szerkeszthető és implicit megbízhatósági kapcsolattal rendelkezik. Nincs, nem rendelkezik megbízhatósági beállításokkal.
    > 
    > 

6. Kattintson az **általános** fülre, majd a **Tallózás** gombra a letöltött metaadat-fájl feltöltéséhez.
    
    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Megbízhatóság kezelése")
    
    >[!NOTE]
    >A metaadat-fájl feltöltése után a rendszer automatikusan kitölti az **egyszeri bejelentkezési URL-cím**, az **egyszeri KIJELENTKEZÉSI URL-cím**és az **aláíró tanúsítvány** értékét.
    > 
     
7. Kattintson az **Attribútumok** fülre.

8. Az **attribútumok** lapon hajtsa végre a következő lépést:
    
    ![Attribútumok](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attribútumok") 

    a. Kattintson a **Assertion-Based attribútum hozzáadása**lehetőségre, majd adja hozzá a következő kijelentési attribútumokat:
       
    | Érvényesítési attribútum | Elsődleges attribútum |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |LastName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Az attribútumok konfigurációja attól függ, hogy az adott alkalmazás (ok) hogyan lett kifejlesztve, azaz hogy mely attribútum (ok) vár az SAML-válaszban, és milyen névvel (a résztvevő attribútummal) férhetnek hozzá ehhez az attribútumhoz a kódban.
     > 
    
    b. A képernyőkép **alapértelmezett attribútuma** csak illusztrációs célokat szolgál. A forgatókönyv működéséhez nem szükséges.  
 
    c. Az alkalmazás fejlesztésének módjától függ, hogy a **fő attribútum** neve és értékei milyen módon jelennek meg a képernyőképen. Lehetséges, hogy az alkalmazásnak eltérő leképezéseket kell használnia.

### <a name="assertion-based-groups"></a>Kijelentési alapú csoportok

Választható lépésként konfigurálhatja az Azure Active Directory identitás-szolgáltatóhoz tartozó érvényesítő csoportokat is.

A csoportok használata az SAP Cloud platformon lehetővé teszi, hogy dinamikusan rendeljen hozzá egy vagy több felhasználót egy vagy több szerepkörhöz az SAP Cloud platform-alkalmazásokban, az SAML 2,0-utasítás attribútumainak értékei alapján. 

Ha például a "*Szerződés = ideiglenes*" attribútumot tartalmazza, az összes érintett felhasználót fel kell venni az "*ideiglenes*" csoportba. Az "*ideiglenes*" csoport egy vagy több olyan szerepkört tartalmazhat, amelyek egy vagy több, az SAP Cloud platform-fiókban üzembe helyezett alkalmazásból származnak.
 
Ha egyszerre több felhasználót szeretne hozzárendelni egy vagy több szerepkörhöz az SAP Cloud platform-fiókban, használja az állítási alapú csoportokat. Ha csak egy vagy több felhasználót szeretne hozzárendelni bizonyos szerepkörökhöz, azt javasoljuk, hogy közvetlenül az SAP Cloud platform cockpit "**engedélyek**" lapján Rendeljen hozzájuk.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az SAP Cloud platform elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **SAP Cloud platform**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza ki az **SAP Cloud platform**elemet.

    ![Az SAP Cloud platform hivatkozása az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sap-cloud-platform-test-user"></a>SAP Cloud platform tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az SAP Cloud platformba, hozzá kell rendelnie a szerepköröket az SAP Cloud platformon.

**Ha szerepkört szeretne hozzárendelni egy felhasználóhoz, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **SAP Cloud platform** cockpitbe.

2. Hajtsa végre a következőket:
   
    ![Engedélyek](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Engedélyek")
   
    a. Kattintson az **Engedélyezés**elemre.

    b. Kattintson a **felhasználók** fülre.

    c. A **felhasználó** szövegmezőbe írja be a felhasználó e-mail-címét.

    d. Kattintson a **hozzárendelés** elemre a felhasználó szerepkörhöz rendeléséhez.

    e. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SAP Cloud platform csempére kattint, automatikusan be kell jelentkeznie az SAP Cloud platformra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

