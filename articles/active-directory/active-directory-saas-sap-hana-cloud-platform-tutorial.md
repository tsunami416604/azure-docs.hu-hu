---
title: 'Oktatóanyag: Azure Active Directoryval integrált SAP Cloud Platform |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP Cloud Platform között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 4d39c8437cbed497e2a2a7e64caa05f8e3d04869
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352463"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Oktatóanyag: Azure Active Directoryval integrált SAP Cloud Platform

Ebben az oktatóanyagban elsajátíthatja SAP Cloud Platform integrálása az Azure Active Directory (Azure AD).

SAP Cloud Platform integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér SAP Cloud Platform szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett SAP Cloud platform (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs SAP Cloud Platform, az alábbi elemek szükségesek:

- Az Azure AD szolgáltatásra
- A SAP Cloud Platform egyszeri bejelentkezés engedélyezve van az előfizetés

Ez az oktatóanyag befejezése után az Azure AD felhasználók SAP Cloud Platform rendelt tudják az alkalmazás használatával történő egyszeri bejelentkezéshez a [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>A saját-alkalmazás központi telepítése, illetve fizethetnek elő az egyszeri bejelentkezés tesztelése SAP Cloud Platform fiókja kérelmet kell. Ebben az oktatóanyagban egy alkalmazás lett telepítve a fiókban.
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAP Cloud Platform hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP Cloud Platform hozzáadása a gyűjteményből
Az Azure AD integrálása a SAP Cloud Platform konfigurálásához kell hozzáadnia SAP Cloud Platform a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SAP Cloud Platform hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAP Cloud Platform**, jelölje be **SAP Cloud Platform** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAP Cloud Platform az eredménylistában](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés SAP Cloud Platform "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó SAP felhő platform Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SAP Cloud Platform közötti kapcsolat kapcsolatot kell létrehozni.

SAP Cloud Platform, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az SAP Cloud Platform tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SAP Cloud Platform tesztfelhasználó létrehozása](#create-a-sap-cloud-platform-test-user)**  - való egy megfelelője a Britta Simon SAP Cloud Platform, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az SAP Cloud Platform alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az SAP Cloud Platform konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SAP Cloud Platform** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Az a **SAP felhő Platform tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információkat SAP felhő Platform tartomány és az URL-címek](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-cím segítségével a felhasználók jelentkezzen be a **SAP Cloud Platform** alkalmazás. Ez az a fiók-specifikus az SAP Cloud Platform alkalmazásban védett erőforrás URL-CÍMÉT. Az URL-cím a következő mintát alapul: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Ez az URL-CÍMÉT, amelyhez a felhasználó hitelesítésére az SAP Cloud Platform alkalmazásban.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Az a **azonosító** szövegmezőben adja meg a SAP Cloud Platform, írja be egy URL-CÍMÉT a következő minta egyike: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:

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
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosítóját és a válasz URL-CÍMEN. Ügyfél [SAP felhő Platform ügyfél-támogatási csoport](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) bejelentkezési URL-cím és azonosítója. Válasz URL-CÍMEN kaphat, az oktatóanyag későbbi részében ismertetett megbízhatósági felügyeleti szakaszából.
    > 
     
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban, jelentkezzen be az SAP felhő Platform vezérlőpultot: `https://account.<landscape host>.ondemand.com/cockpit`(például: https://account.hanatrial.ondemand.com/cockpit).

7. Kattintson a **megbízható** fülre.
   
    ![Megbízható](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "megbízhatóság")

8. A megbízható felügyeleti csoportban a **helyi szolgáltató**, hajtsa végre a következő lépéseket:

    ![Megbízható felügyeleti](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "felügyeleti megbízhatóság")
   
    a. Kattintson a **Szerkesztés** gombra.

    b. Mint **konfigurációtípus**, jelölje be **egyéni**.

    c. Mint **helyi szolgáltatónevet**, hagyja meg az alapértelmezett értéket. Ez az érték másolja és illessze be azt a **azonosító** mező az Azure AD beállításai SAP Cloud platform mellett.

    d. Létrehozásához egy **aláírási kulcs** és egy **aláíró tanúsítvány** pár billentyűt, kattintson a **kulcspár létrehozása**.

    e. Mint **egyszerű propagálás**, jelölje be **letiltott**.

    f. Mint **kényszerített hitelesítési**, jelölje be **letiltott**.

    g. Kattintson a **Save** (Mentés) gombra.

9. A mentés után a **helyi szolgáltató** beállításait, hajtsa végre a következő a válasz URL-cím beszerzése:
   
    ![Metaadatok beolvasása](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "metaadatot beszerezni")

    a. Töltse le a SAP Cloud Platform metaadatfájl **metaadatok beolvasása**.

    b. Nyissa meg a letöltött SAP Cloud Platform metaadatok XML-fájlt, és keresse meg a **ns3:AssertionConsumerService** címke.
 
    c. Másolja a értékének a **hely** attribútumot, és illessze be azt a **válasz URL-CÍMEN** mező mellett az Azure AD beállításai SAP Cloud platform.

10. Kattintson a **megbízható identitásszolgáltató** fülre, majd **megbízható identitásszolgáltató hozzáadása**.
   
    ![Megbízható felügyeleti](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "felügyeleti megbízhatóság")
   
    >[!NOTE]
    >A megbízható identitás-szolgáltatóktól kezelését, az egyéni konfigurációs típus választotta, a helyi szolgáltató szakasz kell. Az alapértelmezett típus hogy egy nem szerkeszthető és implicit megbízhatóságot az SAP-azonosító szolgáltatáshoz. Sem a megbízhatóság beállításokat nem rendelkezik.
    > 
    > 

11. Kattintson a **általános** fülre, majd **Tallózás** feltölteni a fájlt a letöltött metaadat.
    
    ![Megbízható felügyeleti](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "felügyeleti megbízhatóság")
    
    >[!NOTE]
    >A metaadatfájl, értékeit feltöltése után **egyszeri bejelentkezési URL-cím**, **egyetlen kijelentkezési URL-címet**, és **aláíró tanúsítvány** automatikusan fel van töltve.
    > 
     
12. Kattintson az **Attribútumok** fülre.

13. Az a **attribútumok** lapra, hajtsa végre a következő lépést:
    
    ![Attribútumok](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "attribútumok") 

    a. Kattintson a **Add Assertion-Based attribútum**, majd adja hozzá a következő állítás alapú attribútumok:
       
    | Helyességi feltétel attribútum | Egyszerű attribútum |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Utónév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Vezetéknév |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mailben |
   
     >[!NOTE]
     >A konfiguráció az attribútumok attól függ, hogyan a szolgáltatáskapcsolódási pont alkalmazás(ok) fejlesztett, ez azt jelenti, hogy mely attribútum(ok) a SAML-válasz várható, és milyen néven (egyszerű attribútum) hozzáféréskor ezt az attribútumot a kódot.
     > 
    
    b. A **alapértelmezett attribútum** ezen a képernyőfelvételen a folyamat csak illusztrációs célokat szolgálnak. Nem működik a forgatókönyv végrehajtásához szükséges.  
 
    c. A nevek és értékek **egyszerű attribútum** a képernyőfelvételen látható módon az alkalmazás fejlesztése hogyan függ. Akkor lehet, hogy az alkalmazás által igényelt különböző leképezéseket.

### <a name="assertion-based-groups"></a>Csoportok helyességi feltétel alapján

Egy nem kötelező lépés konfigurálható az Azure Active Directory identitásszolgáltató csoportok helyességi feltétel alapján.

Csoportok használata a SAP Cloud Platform lehetővé teszi egy vagy több felhasználó dinamikusan hozzárendelése egy vagy több szerepkör az SAP Cloud Platform alkalmazásokban, határozza meg a SAML 2.0 helyességi feltétel attribútumok értékek. 

Például, ha a helyességi feltételt tartalmaz az attribútum "*szerződés = ideiglenes*", érdemes lehet a csoporthoz lehet hozzáadni az összes érintett felhasználók"*ideiglenes*". A csoport "*ideiglenes*" tartalmazhat egy vagy több szerepkör az SAP Felhőplatform-fiókban telepített egy vagy több alkalmazásokból.
 
Csoportok helyességi feltétel alapú használható, ha egyszerre sok felhasználó hozzárendelése egy vagy több szerepkör az alkalmazások az SAP Felhőplatform-fiókban. Ha azt szeretné, csak egyetlen vagy kis számú felhasználók hozzárendelése adott szerepkörök, ajánlott közvetlenül a hozzárendelése a "**engedélyek**" az SAP Cloud Platform vezérlőpultot lapján.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>SAP Cloud Platform tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezni az SAP Cloud Platform, a SAP felhő platform szerepköröket kell rendelnie a számukra.

**A szerepkör hozzárendelése felhasználóhoz, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SAP Cloud Platform** vezérlőpultot.

2. Hajtsa végre a következő:
   
    ![Engedélyek](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "engedélyek")
   
    a. Kattintson a **engedélyezési**.

    b. Kattintson a **felhasználók** fülre.

    c. Az a **felhasználói** szövegmező, írja be a felhasználó e-mail címét.

    d. Kattintson a **hozzárendelése** a felhasználó hozzárendelése egy szerepkörhöz.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP Cloud Platform Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Az SAP Cloud Platform Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP Cloud Platform**.

    ![Az alkalmazások listáját a SAP Cloud Platform hivatkozás](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési Panel SAP Cloud Platform mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett az SAP Cloud Platform alkalmazáshoz.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

