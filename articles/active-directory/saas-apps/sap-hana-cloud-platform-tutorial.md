---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Cloud platformon |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP-Felhőplatformmal között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a7fa71f2cb9d2c5326eea75af40e1ab6b986eb9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864872"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Cloud platformon

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP-Felhőplatformmal integrálása az Azure Active Directory (Azure AD).
SAP-Felhőplatformmal integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAP Cloud Platform az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a SAP Cloud platformon (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SAP Cloud platformon, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az SAP Cloud Platform egyszeri bejelentkezés engedélyezve van az előfizetés

Ebben az oktatóanyagban elvégezte az SAP-Felhőplatformmal rendelt Azure AD-felhasználók tudják egyetlen jelentkezzen be az alkalmazás használatával az [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Szeretne saját-alkalmazás üzembe helyezése, illetve fizethetnek elő a SAP Cloud Platform-fiók alkalmazás egyszeri bejelentkezési vizsgálatához. Ebben az oktatóanyagban egy alkalmazást helyezünk üzembe a fiókban.
> 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Az SAP-Felhőplatform támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP-Felhőplatformmal hozzáadása a katalógusból

Az Azure AD-be SAP-Felhőplatformmal integrációjának konfigurálása, hozzá kell SAP-Felhőplatformmal a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SAP-Felhőplatformmal hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAP-Felhőplatformmal**, jelölje be **SAP-Felhőplatformmal** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában SAP-Felhőplatformmal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Cloud platformon alapuló nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó, az SAP-Felhőplatformmal hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SAP Cloud platformon tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az SAP Cloud platformon egyszeri bejelentkezés konfigurálása](#configure-sap-cloud-platform-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[SAP-Felhőplatformmal tesztfelhasználó létrehozása](#create-sap-cloud-platform-test-user)**  – egy megfelelője a Britta Simon rendelkeznie a SAP Cloud platformon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Cloud platformon, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAP-Felhőplatformmal** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az SAP Cloud platformon tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** beviteli mező, írja be az URL-címet a felhasználók által használt, jelentkezzen be a **SAP-Felhőplatformmal** alkalmazás. Ez a fiók-specifikus URL-címét az SAP-Felhőplatformmal alkalmazásban védett erőforrásokhoz. Az URL-cím alapján a következő mintának: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Ez az az URL-cím, az SAP-Felhőplatformmal alkalmazásba, a felhasználói hitelesítést igényel.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Az a **azonosító** szövegmezőben, hogy Ön biztosítja az SAP Cloud platformon, írjon be egy URL-címet a következő minták segítségével: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:

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
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-azonosító és válasz URL-cím. Kapcsolattartó [SAP Cloud platformon ügyfél-támogatási csapatának](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) bejelentkezési URL- és azonosító beolvasásához. Válasz URL-cím el az oktatóanyag későbbi részében ismertetett megbízhatósági kezeléssel foglalkozó szakaszban.
    > 
4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Az SAP Cloud platformon egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be, az SAP Cloud Platform vezérlőpultot `https://account.<landscape host>.ondemand.com/cockpit`(például: https://account.hanatrial.ondemand.com/cockpit).

2. Kattintson a **megbízható** fülre.
   
    ![Megbízható](./media/sap-hana-cloud-platform-tutorial/ic790800.png "megbízhatóság")

3. A megbízható felügyeleti területen alatt **helyi szolgáltató**, hajtsa végre az alábbi lépéseket:

    ![Megbízható felügyeleti](./media/sap-hana-cloud-platform-tutorial/ic793931.png "megbízható felügyeleti")
   
    a. Kattintson a **Szerkesztés** gombra.

    b. Mint **konfigurációtípus**válassza **egyéni**.

    c. Mint **helyi szolgáltatónevet**, hagyja változatlanul az alapértelmezett értéket. Másolja ezt az értéket, és illessze be azt a **azonosító** SAP Cloud platformon az Azure AD-konfigurációjának mezőbe.

    d. Létrehozni egy **aláíró kulcs** és a egy **aláíró tanúsítvány** kulcspárt, kattintson a **kulcspár létrehozása**.

    e. Mint **egyszerű propagálás**válassza **letiltott**.

    f. Mint **kényszerített hitelesítési**válassza **letiltott**.

    g. Kattintson a **Save** (Mentés) gombra.

4. A mentés után a **helyi szolgáltató** beállításait, hajtsa végre a következő, a válasz URL-cím:
   
    ![Metaadatok beolvasása](./media/sap-hana-cloud-platform-tutorial/ic793930.png "metaadatainak beolvasása")

    a. Az SAP-Felhőplatformmal metaadatait tartalmazó fájl letöltéséhez kattintson **metaadatok beolvasása**.

    b. Nyissa meg a letöltött SAP-Felhőplatformmal metaadatainak XML-fájlt, és keresse meg a **ns3:AssertionConsumerService** címke.
 
    c. Másolja az értéket, a **hely** attribútumot, és illessze be azt a **válasz URL-cím** SAP Cloud platformon az Azure AD-konfigurációjának mezőbe.

5. Kattintson a **identitásszolgáltató megbízható** fülre, majd **hozzáadása megbízható identitásszolgáltató**.
   
    ![Megbízható felügyeleti](./media/sap-hana-cloud-platform-tutorial/ic790802.png "megbízható felügyeleti")
   
    >[!NOTE]
    >Megbízható Identitásszolgáltatók listájának kezelése, egyéni konfiguráció írja be a helyi szolgáltató szakaszban kiválasztott kell. Alapértelmezett konfiguráció típusa van egy nem szerkeszthető és implicit megbízhatóságot az SAP-azonosító szolgáltatáshoz. Nincs, a megbízhatósági beállítások nem rendelkezik.
    > 
    > 

6. Kattintson a **általános** fülre, majd **Tallózás** a letöltött metaadatfájl feltöltése.
    
    ![Megbízható felügyeleti](./media/sap-hana-cloud-platform-tutorial/ic793932.png "megbízható felügyeleti")
    
    >[!NOTE]
    >A tartozó értékeket a metaadatfájl feltöltése után **egyszeri bejelentkezési URL-cím**, **egyszeri kijelentkezési URL-cím**, és **aláíró tanúsítvány** a rendszer automatikusan kitölti.
    > 
     
7. Kattintson az **Attribútumok** fülre.

8. Az a **attribútumok** fülre, hajtsa végre a következő lépést:
    
    ![Attribútumok](./media/sap-hana-cloud-platform-tutorial/ic790804.png "attribútumok") 

    a. Kattintson a **Add Assertion-Based attribútum**, majd adja hozzá a következő állítás tartományalapú attribútumok:
       
    | Helyességi feltétel attribútum | Rendszerbiztonsági tag attribútum |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Keresztnév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Vezetéknév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >A konfiguráció az attribútumok attól függ, hogyan a szolgáltatáskapcsolódási pont (ok) lettek kifejlesztve, azt jelenti, mely attribútumokkal, a SAML-válasz többek között elvárják, és mely néven (rendszerbiztonsági tag attribútum) férnek hozzá ezt az attribútumot a kódot.
     > 
    
    b. A **attribútum alapértelmezett** a képernyőképen látható a folyamat csak illusztrációs célokat szolgálnak. Nem szükséges, hogy a forgatókönyv működjön.  
 
    c. A neveket és értékeket **rendszerbiztonsági tag attribútum** a képernyőképen látható függ, hogy az alkalmazás fejlesztése. Akkor lehet, hogy az alkalmazás futtatásához szükséges más leképezések.

### <a name="assertion-based-groups"></a>Előfeltétel-alapú csoportok

Választható lépésként az Azure Active Directory identitásszolgáltató csoportok előfeltétel-alapú konfigurálható.

Csoportok használata a SAP Cloud Platform lehetővé teszi dinamikus hozzárendelése egy vagy több felhasználó, az SAP-Felhőplatformmal alkalmazásokban, a SAML 2.0-előfeltétel az attribútum értéke által meghatározott egy vagy több szerepkört. 

Például, ha az előfeltétel az attribútumot tartalmaz "*szerződés = ideiglenes*", érdemes lehet hozzáadni a csoporthoz, az összes érintett felhasználók"*ideiglenes*". A csoport "*ideiglenes*" az üzembe helyezett SAP-Felhőplatform-fiókjában található egy vagy több alkalmazás egy vagy több szerepkört is tartalmazhat.
 
Előfeltétel-alapú csoportokat használja, ha egyszerre sok felhasználók hozzárendelése egy vagy több szerepkör az SAP Cloud Platform-fiókban alkalmazásokat szeretne. Ha azt szeretné, csak egyetlen vagy kis számú felhasználók hozzárendelése az egyes szerepkörök, javasoljuk, hogy hozzá kell rendelni őket közvetlenül a "**engedélyek**" az SAP-Felhőplatformmal vezérlőpultot lapján.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az SAP-Felhőplatformmal Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAP-Felhőplatformmal**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **SAP-Felhőplatformmal**.

    ![Az alkalmazások listáját az SAP-Felhőplatformmal hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-cloud-platform-test-user"></a>SAP-Felhőplatformmal tesztfelhasználó létrehozása

Ahhoz, hogy az SAP-Felhőplatformmal jelentkezzen be az Azure AD-felhasználók, szerepkörök az SAP Cloud platformon kell rendelnie őket.

**Szerepkör hozzárendelése egy felhasználóhoz, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SAP-Felhőplatformmal** vezérlőpultot.

2. Hajtsa végre a következőket:
   
    ![Engedélyek](./media/sap-hana-cloud-platform-tutorial/ic790805.png "engedélyei")
   
    a. Kattintson a **engedélyezési**.

    b. Kattintson a **felhasználók** fülre.

    c. Az a **felhasználói** szövegmezőbe írja be a felhasználó e-mail címét.

    d. Kattintson a **hozzárendelése** hozzárendelése a felhasználói szerepkörhöz.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SAP Cloud platformon csempére kattint, akkor kell automatikusan megtörténik a a SAP felhőalapú platform, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

