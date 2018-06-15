---
title: 'Oktatóanyag: Azure Active Directory-integráció a IBM Kenexa felmérés vállalati |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az IBM Kenexa felmérés vállalati között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 88e0072adeeebaf6c9e964db28a45f6fe038fddf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342178"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció a IBM Kenexa felmérés vállalati

Ebben az oktatóanyagban elsajátíthatja IBM Kenexa felmérés vállalati integrálása az Azure Active Directory (Azure AD).

IBM Kenexa felmérés vállalati integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér az IBM Kenexa felmérés vállalati szabályozhatja.
- Engedélyezheti a felhasználók automatikusan jelentkezhetnek be IBM Kenexa felmérés vállalati az Azure AD-fiókok egyszeri bejelentkezés (SSO) használatával.
- A fiók egyetlen központi helyen kezelheti: az Azure-portálon.

Ha azt szeretné, az Azure AD egy szolgáltatott szoftverként (SaaS) alkalmazás integrációt, tudnia további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a IBM Kenexa felmérés vállalati, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az IBM Kenexa felmérés vállalati SSO-kompatibilis előfizetéssel

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, azt javasoljuk, hogy nem használ egy éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban a Azure AD SSO tesztkörnyezetben tesztelni. Az oktatóanyag ismertetett forgatókönyvben két fő építőelemeket áll:

* IBM Kenexa felmérés vállalati hozzáadása a gyűjteményből
* Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>IBM Kenexa felmérés vállalati hozzáadása a gyűjteményből
Az Azure AD integrálása a IBM Kenexa felmérés vállalati megadásához vegye fel IBM Kenexa felmérés vállalati a gyűjteményből a felügyelt SaaS-alkalmazásokhoz.

A gyűjteményből IBM Kenexa felmérés vállalati hozzáadásához tegye a következőket:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Hozzáadhat egy alkalmazást, kattintson a **új alkalmazás** gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **IBM Kenexa felmérés vállalati**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Az eredmények listájában válassza **IBM Kenexa felmérés vállalati**, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![IBM Kenexa felmérés vállalati az eredménylistában](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban konfigurálása és tesztelése az Azure AD SSO IBM Kenexa felmérés vállalati "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működjön az Azure AD az Azure ad-ben az IBM Kenexa felmérés vállalati felhasználó megfelelőjére azonosítania kell. Ez azt jelenti az Azure AD kapcsolatot kell létesítenie egy hivatkozás egy Azure AD-felhasználó és a kapcsolódó felhasználó között IBM Kenexa felmérés vállalati.

A hivatkozás kapcsolatot létesíteni, rendelje az értékét a **felhasználónév** IBM Kenexa felmérés vállalati értékeként a **felhasználónév** az Azure ad-ben.

IBM Kenexa felmérés vállalati Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, végezze el az építőelemeket, a következő két szakaszokban.

### <a name="configure-azure-ad-sso"></a>Az Azure AD-egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri Bejelentkezést az Azure portálon, és egyszeri bejelentkezés konfigurálása az IBM Kenexa felmérés vállalati alkalmazás a következő módon:

1. Az Azure portálon a a **IBM Kenexa felmérés vállalati** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![IBM Kenexa felmérés vállalati konfigurálása egyszeri bejelentkezés hivatkozás][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanel a **mód** mezőben válassza **SAML-alapú bejelentkezés** SSO engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. Az a **IBM Kenexa felmérés vállalati tartomány és az URL-címek** területen tegye a következőket:

    ![IBM Kenexa felmérés vállalati tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. Az a **azonosító** szövegmezőhöz URL-címet adja meg a következő mintával: `https://surveys.kenexa.com/<companycode>`

    b. Az a **válasz URL-CÍMEN** szövegmezőhöz URL-címet adja meg a következő mintával: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Az előző értékei nem valódi. A tényleges azonosítójú frissítheti, illetve válasz URL-CÍMÉT. A tényleges értékek beszerzéséhez forduljon a [IBM Kenexa felmérés vállalati támogatási csoport](https://www.ibm.com/support/home/?lnk=fcw).

4. A **SAML-aláíró tanúsítványa**, kattintson a **tanúsítvány (Base64)**, és mentse a fájlt a számítógépre.

    ![A tanúsítvány (Base64) letöltési hivatkozását](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    Az IBM Kenexa felmérés vállalati alkalmazás vár a biztonsági helyességi feltételek Markup Language (SAML) helyességi feltételek fogadásához egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurálása. A válaszban a felhasználó-azonosító jogcím értékét meg kell egyeznie az egyszeri bejelentkezési azonosító a Kenexa rendszerben konfigurált. A megfelelő felhasználói azonosítót a szervezetében, egyszeri Bejelentkezéses Internet Datagram Protocol (IDP) megfeleltetéséhez dolgozni a [IBM Kenexa felmérés vállalati támogatási csoport](https://www.ibm.com/support/home/?lnk=fcw). 

    Alapértelmezés szerint az Azure AD a felhasználói azonosító a felhasználó egyszerű felhasználónév (UPN) értéket állítja be. Módosíthatja ezt az értéket a **attribútum** lapon, az alábbi képernyőfelvételen látható módon. Az integráció csak a leképezési befejezése után működik megfelelően.
    
    ![A felhasználói attribútumok párbeszédpanel](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Kattintson a **Save** (Mentés) gombra.

    ![A konfigurálása egyszeri bejelentkezéshez mentési gomb](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Megnyitásához a **bejelentkezés konfigurálása** ablakban, a **IBM Kenexa felmérés vállalati konfiguráció**, kattintson a **konfigurálása IBM Kenexa felmérés vállalati**. 
 
    ![A konfigurálása IBM Kenexa felmérés vállalati hivatkozás](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Másolás a **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** közötti értéket a **rövid összefoglaló** szakasz.

8. Az a **bejelentkezés konfigurálása** ablakban, a **rövid összefoglaló**, másolása a **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** értékeket.

9. Egyszeri bejelentkezés konfigurálása a **IBM Kenexa felmérés vállalati** oldalán, küldjön a letöltött **tanúsítvány (Base64)**, **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** értékeit a a [IBM Kenexa felmérés vállalati támogatási csoport](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Olvassa el ezeket az utasításokat a tömör verzióját a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Miután hozzáadta az alkalmazásból a **Active Directory** > **vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapot, és hozzáférhet a beágyazott dokumentáció keresztül a **konfigurációs** szakasz végén. A beágyazott dokumentáció szolgáltatással kapcsolatos további tudnivalókért lásd: [az Azure AD dokumentációjában beágyazott](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz az alábbi lépésekkel hoz létre tesztfelhasználó Britta Simon az Azure-portálon:

![Hozzon létre egy Azure AD-teszt felhasználó][100]

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Az IBM Kenexa felmérés vállalati tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon meghívta IBM Kenexa felmérés vállalati felhasználó létrehozása. 

A felhasználók létrehozásához az IBM Kenexa felmérés Enterprise rendszerben, és az egyszeri bejelentkezési azonosító hozzárendelését a számukra, használhatja a [IBM Kenexa felmérés vállalati támogatási csoport](https://www.ibm.com/support/home/?lnk=fcw). Ezt az egyszeri bejelentkezési azonosító értéket is kell rendelni a felhasználói azonosító értékét az Azure AD. Módosíthatja az alapértelmezett beállítás a **attribútum** fülre.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze felhasználói Britta Simon által biztosított hozzáférés IBM Kenexa felmérés vállalati Azure SSO használandó.

![A felhasználói szerepkör hozzárendelése][200] 

Felhasználó Britta Simon hozzárendelése IBM Kenexa felmérés vállalati, tegye a következőket:

1. Az Azure portálon, nyissa meg a **alkalmazások** nézet, keresse fel a **Directory** nézetben jelölje ki **vállalati alkalmazások**, és kattintson a **összes alkalmazás**.

    ![A "Vállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

2. Az a **alkalmazások** listáról válassza ki **IBM Kenexa felmérés vállalati**.

    ![Az alkalmazások listáját az IBM Kenexa felmérés vállalati hivatkozás](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. Kattintson a bal oldali ablaktáblában **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra, majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanel, kattintson a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési Panel segítségével tesztelheti.

Amikor rákattint az **IBM Kenexa felmérés vállalati** csempére a hozzáférési panelen, meg kell lennie automatikusan bejelentkeztetjük az IBM Kenexa felmérés vállalati alkalmazás.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
