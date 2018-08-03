---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAP Cloud platformon |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP-Felhőplatformmal között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 07b3c32601d90fdeed1c335c0f36a5ccbdbe4f1d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446714"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Oktatóanyag: Azure Active Directory-integráció az SAP Cloud platformon

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP-Felhőplatformmal integrálása az Azure Active Directory (Azure AD).

SAP-Felhőplatformmal integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az SAP Cloud Platform az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az SAP Cloud platformon (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SAP Cloud platformon, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy SAP-Felhőplatformmal egyszeri bejelentkezés engedélyezve van az előfizetés

Ebben az oktatóanyagban elvégezte az SAP-Felhőplatformmal rendelt Azure AD-felhasználók tudják egyetlen jelentkezzen be az alkalmazás használatával az [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Szeretne saját-alkalmazás üzembe helyezése, illetve fizethetnek elő a SAP Cloud Platform-fiók alkalmazás egyszeri bejelentkezési vizsgálatához. Ebben az oktatóanyagban egy alkalmazást helyezünk üzembe a fiókban.
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SAP-Felhőplatformmal hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP-Felhőplatformmal hozzáadása a katalógusból
Az Azure AD-be SAP-Felhőplatformmal integrációjának konfigurálása, hozzá kell SAP-Felhőplatformmal a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SAP-Felhőplatformmal hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SAP-Felhőplatformmal**, jelölje be **SAP-Felhőplatformmal** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SAP-Felhőplatformmal](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Cloud platformon a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a SAP Cloud platformon tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó, az SAP-Felhőplatformmal hivatkozás kapcsolata kell létrehozni.

SAP-Felhőplatformmal, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SAP Cloud platformon tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy SAP-Felhőplatformmal tesztfelhasználót](#create-a-sap-cloud-platform-test-user)**  – egy megfelelője a Britta Simon rendelkeznie a SAP Cloud platformon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezést az SAP-Felhőplatformmal alkalmazásban konfigurálja.

**Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Cloud platformon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SAP-Felhőplatformmal** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

1. Az a **SAP Cloud platformon tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az SAP Cloud platformon tartomány és URL-címek egyszeri bejelentkezési adatait](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

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
     
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban, jelentkezzen be, az SAP Cloud Platform vezérlőpultot `https://account.<landscape host>.ondemand.com/cockpit`(például: https://account.hanatrial.ondemand.com/cockpit).

1. Kattintson a **megbízható** fülre.
   
    ![Megbízható](./media/sap-hana-cloud-platform-tutorial/ic790800.png "megbízhatóság")

1. A megbízható felügyeleti területen alatt **helyi szolgáltató**, hajtsa végre az alábbi lépéseket:

    ![Megbízható felügyeleti](./media/sap-hana-cloud-platform-tutorial/ic793931.png "megbízható felügyeleti")
   
    a. Kattintson a **Szerkesztés** gombra.

    b. Mint **konfigurációtípus**válassza **egyéni**.

    c. Mint **helyi szolgáltatónevet**, hagyja változatlanul az alapértelmezett értéket. Másolja ezt az értéket, és illessze be azt a **azonosító** SAP Cloud platformon az Azure AD-konfigurációjának mezőbe.

    d. Létrehozni egy **aláíró kulcs** és a egy **aláíró tanúsítvány** kulcspárt, kattintson a **kulcspár létrehozása**.

    e. Mint **egyszerű propagálás**válassza **letiltott**.

    f. Mint **kényszerített hitelesítési**válassza **letiltott**.

    g. Kattintson a **Save** (Mentés) gombra.

1. A mentés után a **helyi szolgáltató** beállításait, hajtsa végre a következő, a válasz URL-cím:
   
    ![Metaadatok beolvasása](./media/sap-hana-cloud-platform-tutorial/ic793930.png "metaadatainak beolvasása")

    a. Az SAP-Felhőplatformmal metaadatait tartalmazó fájl letöltéséhez kattintson **metaadatok beolvasása**.

    b. Nyissa meg a letöltött SAP-Felhőplatformmal metaadatainak XML-fájlt, és keresse meg a **ns3:AssertionConsumerService** címke.
 
    c. Másolja az értéket, a **hely** attribútumot, és illessze be azt a **válasz URL-cím** SAP Cloud platformon az Azure AD-konfigurációjának mezőbe.

1. Kattintson a **identitásszolgáltató megbízható** fülre, majd **hozzáadása megbízható identitásszolgáltató**.
   
    ![Megbízható felügyeleti](./media/sap-hana-cloud-platform-tutorial/ic790802.png "megbízható felügyeleti")
   
    >[!NOTE]
    >Megbízható Identitásszolgáltatók listájának kezelése, egyéni konfiguráció írja be a helyi szolgáltató szakaszban kiválasztott kell. Alapértelmezett konfiguráció típusa van egy nem szerkeszthető és implicit megbízhatóságot az SAP-azonosító szolgáltatáshoz. Nincs, a megbízhatósági beállítások nem rendelkezik.
    > 
    > 

1. Kattintson a **általános** fülre, majd **Tallózás** a letöltött metaadatfájl feltöltése.
    
    ![Megbízható felügyeleti](./media/sap-hana-cloud-platform-tutorial/ic793932.png "megbízható felügyeleti")
    
    >[!NOTE]
    >A tartozó értékeket a metaadatfájl feltöltése után **egyszeri bejelentkezési URL-cím**, **egyszeri kijelentkezési URL-cím**, és **aláíró tanúsítvány** a rendszer automatikusan kitölti.
    > 
     
1. Kattintson az **Attribútumok** fülre.

1. Az a **attribútumok** fülre, hajtsa végre a következő lépést:
    
    ![Attribútumok](./media/sap-hana-cloud-platform-tutorial/ic790804.png "attribútumok") 

    a. Kattintson a **Add Assertion-Based attribútum**, majd adja hozzá a következő állítás tartományalapú attribútumok:
       
    | Helyességi feltétel attribútum | Rendszerbiztonsági tag attribútum |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Keresztnév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Vezetéknév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mailben |
   
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

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>SAP-Felhőplatformmal tesztfelhasználó létrehozása

Ahhoz, hogy az SAP-Felhőplatformmal jelentkezzen be az Azure AD-felhasználók, szerepkörök az SAP Cloud platformon kell rendelnie őket.

**Szerepkör hozzárendelése egy felhasználóhoz, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SAP-Felhőplatformmal** vezérlőpultot.

1. Hajtsa végre a következőket:
   
    ![Engedélyek](./media/sap-hana-cloud-platform-tutorial/ic790805.png "engedélyei")
   
    a. Kattintson a **engedélyezési**.

    b. Kattintson a **felhasználók** fülre.

    c. Az a **felhasználói** szövegmezőbe írja be a felhasználó e-mail címét.

    d. Kattintson a **hozzárendelése** hozzárendelése a felhasználói szerepkörhöz.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az SAP-Felhőplatformmal Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Az SAP-Felhőplatformmal Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SAP-Felhőplatformmal**.

    ![Az alkalmazások listáját az SAP-Felhőplatformmal hivatkozásra](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SAP Cloud platformon csempére kattint, meg kell lekérése automatikusan bejelentkezett az SAP-Felhőplatformmal alkalmazáshoz.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_203.png

