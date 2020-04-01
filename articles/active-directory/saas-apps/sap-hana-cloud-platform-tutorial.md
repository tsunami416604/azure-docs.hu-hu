---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAP Cloud Platformmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Cloud Platform között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160084"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAP Cloud Platformmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Cloud Platformot az Azure Active Directoryval (Azure AD).
Az SAP Cloud Platform integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az SAP Cloud Platformhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az SAP Cloud Platformra (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció sap cloud platformmal való konfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* AZ SAP Cloud Platform egyszeri bejelentkezéses előfizetése

Az oktatóanyag befejezése után az SAP Cloud Platformhoz rendelt Azure AD-felhasználók egyetlen bejelentkezést érhetnek el az alkalmazásba [a Hozzáférési panel bevezetés e felületén.](../user-help/active-directory-saas-access-panel-introduction.md)

>[!IMPORTANT]
>Telepítenie kell a saját alkalmazását, vagy elő kell fizetnie egy alkalmazásra az SAP Cloud Platform-fiókjában az egyszeri bejelentkezés teszteléséhez. Ebben az oktatóanyagban egy alkalmazás van telepítve a fiókban.
> 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az SAP Cloud Platform támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP Cloud Platform hozzáadása a galériából

Az SAP Cloud Platform Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Cloud Platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az SAP Cloud Platformot a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAP Cloud Platform**kifejezést, válassza az SAP Cloud **Platform** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![AZ SAP Cloud Platform az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az SAP Cloud Platformmal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó az SAP Cloud Platform on-val kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az SAP Cloud Platformmal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAP Cloud Platform single sign-on konfigurálásához](#configure-sap-cloud-platform-single-sign-on)** az egyszeri bejelentkezési beállításokat az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SAP Cloud Platform tesztfelhasználót](#create-sap-cloud-platform-test-user)** – ha britta Simon megfelelője az SAP Cloud Platformon, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SAP Cloud Platformmal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Cloud Platform** alkalmazásintegrációs lapján válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Az SAP Cloud Platform domainje és URL-címei egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írja be a felhasználók által az **SAP Cloud Platform** alkalmazásba való bejelentkezéshez használt URL-címet. Ez egy védett erőforrás fiókspecifikus URL-címe az SAP Cloud Platform-alkalmazásban. Az URL-cím a következő mintán alapul:`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Ez az SAP Cloud Platform alkalmazás URL-címe, amely a felhasználóhitelesítést igényel.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Az **Azonosító** szövegmezőben az SAP Cloud Platform típusát az alábbi minták egyikének használatával adja meg: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [az SAP Cloud Platform ügyféltámogatási csapatával](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) a bejelentkezési URL-cím és azonosító leéséhez. Válasz URL-t kaphat a megbízhatóság kezelése szakasz, amely később az oktatóanyag ban ismertet.
    > 
4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Az SAP Cloud Platform egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be az SAP `https://account.<landscape host>.ondemand.com/cockpit`Cloud Platform https://account.hanatrial.ondemand.com/cockpit)Cockpit-be (például: .

2. Kattintson a **Megbízhatóság** fülre.
   
    ![Bizalmi kapcsolat](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Bizalmi kapcsolat")

3. A Megbízhatóság kezelés csoport **Helyi szolgáltató**csoportjában hajtsa végre az alábbi lépéseket:

    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Megbízhatóság kezelése")
   
    a. Kattintson a **Szerkesztés** gombra.

    b. **Konfigurációs típusként**válassza az **Egyéni**lehetőséget.

    c. **Helyi szolgáltató névként**hagyja meg az alapértelmezett értéket. Másolja ezt az értéket, és illessze be az **azonosító** mezőbe az SAP Cloud Platform Azure AD-konfigurációjában.

    d. **Aláírási kulcs** és **aláírótanúsítvány-kulcspár** létrehozásához kattintson **a Kulcspár létrehozása gombra.**

    e. Fő **propagálásként**válassza **a Letiltva**lehetőséget.

    f. **A Kényszerhitelesítés mezőben**válassza **a Letiltva**lehetőséget.

    g. Kattintson a **Mentés** gombra.

4. A **Helyi szolgáltató** beállításainak mentése után hajtsa végre az alábbi műveleteket a Válasz URL-címének beszerzéséhez:
   
    ![Metaadatok bekése](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Metaadatok bekése")

    a. Töltse le az SAP Cloud Platform metaadatfájlját a **Metaadatok bekapása gombra**kattintva.

    b. Nyissa meg a letöltött SAP Cloud Platform metaadat-XML-fájlt, és keresse meg az **ns3:AssertionConsumerService** címkét.
 
    c. Másolja a **Hely** attribútum értékét, majd illessze be a **Válasz URL-mezőjébe** az SAP Cloud Platform Azure AD-konfigurációjában.

5. Kattintson a **Megbízható identitásszolgáltató** fülre, majd a **Megbízható azonosítószolgáltató hozzáadása gombra.**
   
    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Megbízhatóság kezelése")
   
    >[!NOTE]
    >A megbízható identitásszolgáltatók listájának kezeléséhez a Helyi szolgáltatás szolgáltatója szakaszegyéni konfigurációtípusát kell választania. Az alapértelmezett konfigurációs típus esetén nem szerkeszthető és implicit megbízhatósággal rendelkezik az SAP-azonosító szolgáltatással szemben. A Nincs beállításnál nincsenek megbízhatósági beállítások.
    > 
    > 

6. Kattintson az **Általános** fülre, majd a **Tallózás** gombra a letöltött metaadatfájl feltöltéséhez.
    
    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Megbízhatóság kezelése")
    
    >[!NOTE]
    >A metaadatfájl feltöltése után az **egyszeri bejelentkezésurl-címe,** az **egységes kijelentkezési URL**és az aláíró **tanúsítvány** értékei automatikusan feltöltődnek.
    > 
     
7. Kattintson az **Attribútumok** fülre.

8. Az **Attribútumok** lapon hajtsa végre a következő lépést:
    
    ![Attribútumok](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attribútumok") 

    a. Kattintson **a Helyességalapú attribútum hozzáadása gombra,** majd adja hozzá a következő helyességalapú attribútumokat:
       
    | Helyességifeltétel attribútuma | Fő attribútum |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Utónév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Vezetéknév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Az attribútumok konfigurációja attól függ, hogy az SCP-n lévő alkalmazás(ok) hogyan fejlődnek, azaz hogy milyen attribútumot várnak az SAML-válaszban, és milyen név alatt (Egyszerű attribútum) férnek hozzá ehhez az attribútumhoz a kódban.
     > 
    
    b. A képernyőkép **alapértelmezett attribútuma** csak illusztrációs célokat szolgál. Nem szükséges, hogy a forgatókönyv működik.  
 
    c. A képernyőképen látható **Principal Attribútum** nevei és értékei az alkalmazás fejlesztésének módjától függenek. Lehetséges, hogy az alkalmazás különböző leképezéseket igényel.

### <a name="assertion-based-groups"></a>Kiállításokon alapuló csoportok

Opcionális lépésként beállíthatja a helyességalapú csoportokat az Azure Active Directory-identitásszolgáltatóhoz.

Csoportok használata az SAP Cloud Platform lehetővé teszi, hogy dinamikusan hozzáegy vagy több felhasználó egy vagy több szerepkör az SAP Cloud Platform-alkalmazások, az SAML 2.0 alapértékeinek értékei által meghatározott. 

Ha például a kijelentés a "*contract=temporary*" attribútumot tartalmazza, akkor az összes érintett felhasználót hozzá szeretné adni az "*IDEIGLENES*" csoporthoz. Az *"IDEIGLENES"* csoport egy vagy több szerepkört tartalmazhat egy vagy több, az SAP Cloud Platform-fiókban telepített alkalmazásból.
 
Akkor használjon helyességalapú csoportokat, ha egyszerre több felhasználót szeretne hozzárendelni az SAP Cloud Platform-fiókjában lévő alkalmazások egy vagy több szerepköréhez. Ha csak egy vagy kis számú felhasználót szeretne hozzárendelni adott szerepkörökhöz, javasoljuk, hogy közvetlenül rendelje hozzá őket az SAP Cloud Platform vezérlőpultjának "**Engedélyezés**" lapján.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést az SAP Cloud Platformhoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az SAP **Cloud Platform**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **SAP Cloud Platform**lehetőséget.

    ![Az SAP Cloud Platform hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sap-cloud-platform-test-user"></a>Sap Cloud Platform tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az SAP Cloud Platformra, hozzá kell rendelnie az SAP Cloud Platformszerepköröket.

**Ha szerepkört szeretne hozzárendelni egy felhasználóhoz, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az **SAP Cloud Platform** pilótafülkéjébe.

2. Hajtsa végre a következőket:
   
    ![Engedélyek](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Engedélyek")
   
    a. Kattintson **az Engedélyezés gombra.**

    b. Kattintson a **Felhasználók** fülre.

    c. A **Felhasználó** mezőbe írja be a felhasználó e-mail címét.

    d. A **Hozzárendelés gombra** kattintva rendelje hozzá a felhasználót egy szerepkörhöz.

    e. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor az SAP Cloud Platform csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az SAP Cloud Platformba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

