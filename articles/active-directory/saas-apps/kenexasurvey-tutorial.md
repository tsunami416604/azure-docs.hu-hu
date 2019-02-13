---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az IBM Kenexa felmérés Enterprise |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az IBM Kenexa felmérés Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67b24ca0008a03474b54a1bf226261c3f395fec
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183224"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Oktatóanyag: Az Azure Active Directory-integráció az IBM Kenexa felmérés Enterprise

Ebben az oktatóanyagban elsajátíthatja, hogyan IBM Kenexa felmérés vállalati integrálható az Azure Active Directory (Azure AD).

IBM Kenexa felmérés vállalati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az IBM Kenexa felmérés vállalati Azure AD-ben.
- Engedélyezheti a felhasználók automatikusan jelentkezhetnek be IBM Kenexa felmérés nagyvállalati és az Azure AD-fiókjukat az egyszeri bejelentkezés (SSO) használatával.
- A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

Ha szeretne többet is megtudni szoftverek az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja, [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az IBM Kenexa felmérés Enterprise, a következőkre van szükség:

- Azure AD-előfizetés
- Az IBM Kenexa felmérés vállalati SSO-kompatibilis előfizetéssel

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy nem használja éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban az Azure AD SSO-t egy tesztkörnyezetben teszteléséhez. Az oktatóanyagban ismertetett forgatókönyv két fő építőelemeket áll:

* IBM Kenexa felmérés vállalati hozzáadása a katalógusból
* Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>IBM Kenexa felmérés vállalati hozzáadása a katalógusból
Az Azure AD-be, IBM Kenexa felmérés vállalati integráció konfigurálásához, adja hozzá a katalógus IBM Kenexa felmérés vállalati a felügyelt SaaS-alkalmazások listájában.

IBM Kenexa felmérés vállalati hozzáadása a katalógusból, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra. 

    ![Az Azure Active Directory gomb][1]

1. Válassza ki **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Egy alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **IBM Kenexa felmérés vállalati**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

1. Az eredmények listájában válassza **IBM Kenexa felmérés vállalati**, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![IBM Kenexa felmérés vállalati a találatok listájában](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az IBM Kenexa felmérés Enterprise egy "Britta Simon." nevű tesztelési felhasználó alapján

Az SSO működjön az Azure AD meg kell állapítania az IBM Kenexa felmérés vállalati felhasználó megfelelője az Azure ad-ben. Más szóval az Azure AD kapcsolatot kell létesítenie egy hivatkozás egy Azure AD-felhasználót és a egy kapcsolódó felhasználó közötti IBM Kenexa felmérés vállalati.

A hivatkozás kapcsolatot hozhat létre, rendelje hozzá az értékét a **felhasználónév** IBM Kenexa felmérés vállalati értékeként a **felhasználónév** az Azure ad-ben.

Az Azure AD SSO IBM Kenexa felmérés vállalati tesztelése és konfigurálása, hajtsa végre a következő két szakasz az építőelemeket.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon, és egyszeri bejelentkezés konfigurálása az IBM Kenexa felmérés vállalati alkalmazásban a következő módon:

1. Az Azure Portalon az a **IBM Kenexa felmérés vállalati** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![IBM Kenexa felmérés vállalati konfigurálása egyszeri bejelentkezési hivatkozás][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanel a **mód** jelölje ki **SAML-alapú bejelentkezés** engedélyezhető az SSO.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

1. Az a **IBM Kenexa felmérés vállalati tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![IBM Kenexa felmérés vállalati tartomány és URL-címek egyszeri bejelentkezési adatait](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. Az a **azonosító** szövegmezőbe a URL-címet írja be a következő mintával: `https://surveys.kenexa.com/<companycode>`

    b. Az a **válasz URL-cím** szövegmezőbe a URL-címet írja be a következő mintával: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Az előző értékek nem valódi. Frissítse azokat a tényleges azonosítóval, és a válasz URL-címe. A tényleges értékek beszerzéséhez forduljon a [IBM Kenexa felmérés vállalati támogatási csapatának](https://www.ibm.com/support/home/?lnk=fcw).

1. A **SAML-aláíró tanúsítvány**, kattintson a **tanúsítvány (Base64)**, majd mentse a számítógépre a tanúsítványfájlt.

    ![A tanúsítvány (Base64) letöltési hivatkozás](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    Az IBM Kenexa felmérés vállalati alkalmazás vár egy megadott formátumban, és megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja a biztonsági helyességi feltételek Markup Language (SAML) helyességi feltételek fogadásához. A válaszban a felhasználó-azonosító jogcím értékét meg kell egyeznie a Kenexa rendszerben konfigurált egyszeri bejelentkezési azonosítója. A szervezetben, SSO Internet Datagram Protocol (IDP) megfelelő felhasználói azonosítót hozzárendelni dolgozni a [IBM Kenexa felmérés vállalati támogatási csapatának](https://www.ibm.com/support/home/?lnk=fcw). 

    Alapértelmezés szerint az Azure AD a felhasználói azonosító a felhasználó egyszerű felhasználónév (UPN) értéket állítja be. Módosíthatja ezt az értéket a **attribútum** lap az alábbi képernyőképen látható módon. Az integráció csak a leképezési befejezését követően működik megfelelően.
    
    ![A felhasználói attribútumok párbeszédpanel](./media/kenexasurvey-tutorial/tutorial_attribute.png) 

1. Kattintson a **Save** (Mentés) gombra.

    ![A konfigurálás egyszeri Mentés gomb](./media/kenexasurvey-tutorial/tutorial_general_400.png)

1. Megnyitásához a **bejelentkezés konfigurálása** ablakban, a **IBM Kenexa felmérés vállalati konfiguráció**, kattintson a **konfigurálása IBM Kenexa felmérés vállalati**. 
 
    ![Az IBM Kenexa felmérés vállalati konfigurálása hivatkozás](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

1. Másolás a **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** értékeket a **rövid összefoglaló** szakaszban.

1. Az a **bejelentkezés konfigurálása** ablak alatt **rövid összefoglaló**, másolása a **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-** értékeket.

1. Egyszeri bejelentkezés konfigurálása az **IBM Kenexa felmérés vállalati** oldalán, a letöltött küldése **tanúsítvány (Base64)**, **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** értékeket kell a [IBM Kenexa felmérés vállalati támogatási csapatának](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Tekintse meg ezeket az utasításokat a tömör verzióját a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazást. Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezési** lapra, és hozzáférhet a a beágyazott dokumentáció a **konfigurációs** szakasz végén. A beágyazott dokumentáció szolgáltatással kapcsolatos további tudnivalókért lásd: [Azure ad-ben a beágyazott dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ebben a szakaszban tesztfelhasználó Britta Simon az Azure Portalon létrehozhat az alábbiak szerint:

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/kenexasurvey-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/kenexasurvey-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.
 
    ![A Hozzáadás gombra.](./media/kenexasurvey-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![A felhasználó párbeszédpanel](./media/kenexasurvey-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Egy IBM Kenexa felmérés vállalati tesztfelhasználó létrehozása

Ebben a szakaszban egy IBM Kenexa felmérés vállalati Britta Simon nevű felhasználó létrehozásához. 

A felhasználók létrehozásához az IBM Kenexa felmérés Enterprise rendszerben, és az egyszeri bejelentkezési azonosító leképezése a számukra, használhatja a [IBM Kenexa felmérés vállalati támogatási csapatának](https://www.ibm.com/support/home/?lnk=fcw). Ezt az egyszeri bejelentkezési azonosító értéket is kell rendelni a felhasználói azonosító értékét az Azure ad-ből. Az ezt az alapértelmezett beállítást módosíthatja a **attribútum** fülre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure SSO használatára a hozzáférés biztosításával az IBM Kenexa felmérés vállalati felhasználó.

![A felhasználói szerepkör hozzárendelése][200] 

Britta Simon felhasználó hozzárendelése IBM Kenexa felmérés Enterprise, tegye a következőket:

1. Az Azure Portalon nyissa meg a **alkalmazások** nézet, nyissa meg a **Directory** nézetben válassza **vállalati alkalmazások**, és kattintson a **minden alkalmazás** .

    ![A "Nagyvállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

1. Az a **alkalmazások** listáról válassza ki **IBM Kenexa felmérés vállalati**.

    ![Az alkalmazások listáját az IBM Kenexa felmérés vállalati hivatkozásra](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

1. A bal oldali ablaktáblán kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

1. Kattintson a **Hozzáadás** gombra, majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

1. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurálása a hozzáférési Panel használatával tesztelheti.

Amikor rákattint a **IBM Kenexa felmérés vállalati** csempéje a hozzáférési panelen, meg kell hogy automatikusan jelentkezzenek be az IBM Kenexa felmérés vállalati alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/kenexasurvey-tutorial/tutorial_general_203.png

 
