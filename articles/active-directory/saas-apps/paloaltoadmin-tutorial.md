---
title: 'Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks - felügyeleti felhasználói Felületéhez |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto Networks - felügyeleti felhasználói Felületéhez között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: fed368c0df265495d9fee764f86825957fae8bab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447424"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Az Azure Active Directory integrálásának Palo Alto Networks - felügyeleti felhasználói Felületéhez

Ebben az oktatóanyagban elsajátíthatja az Azure Active Directory (Azure AD) integrálása a Palo Alto Networks - felügyeleti felhasználói Felületéhez.

A Palo Alto Networks - felügyeleti felhasználói Felületéhez, integráló Azure AD által a következő előnyöket kap:

- Szabályozhatja, ki férhet hozzá a Palo Alto Networks - rendszergazdai felhasználói felület az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy az első bejelentkezett automatikusan a Palo Alto Networks - rendszergazdai felhasználói felület (egyszeri bejelentkezés, vagy az SSO) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Palo Alto Networks - rendszergazdai felhasználói felület, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Palo Alto hálózatok új generációs tűzfal vagy Panorámaképe (központi felügyeleti rendszer pedig a tűzfalakat)

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy *nem* éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. 

Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

* Palo Alto Networks - hozzáadása a katalógusból felügyeleti felhasználói Felületéhez
* Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Adja hozzá a Palo Alto Networks - felügyeleti felhasználói Felületéhez a katalógusból
Az Azure AD-integráció konfigurálásához a Palo Alto Networks - felügyeleti felhasználói Felületéhez, adja hozzá Palo Alto Networks - felügyeleti felhasználói Felületéhez a katalógusból az alábbiak szerint felügyelt SaaS-alkalmazások listájához:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A "Nagyvállalati alkalmazások" ablak][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra az ablak tetején.

    ![Az "új alkalmazás" gombra][3]

1. A Keresés mezőbe írja be a **Palo Alto Networks - felügyeleti felhasználói Felületéhez**válassza **Palo Alto Networks - felügyeleti felhasználói Felületéhez** a találatok listájában, és válassza ki a **Hozzáadás**.

    ![Futtató Palo Alto Networks - felügyeleti felhasználói Felületéhez a találatok listájában](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján a Palo Alto Networks - felügyeleti felhasználói Felületéhez, az Azure AD egyszeri bejelentkezés tesztelése

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell állapítania a Palo Alto Networks - felügyeleti felhasználói Felületéhez felhasználó és a megfelelő Azure AD-ben. Más szóval az Azure AD-felhasználó, és ugyanahhoz a felhasználóhoz a Palo Alto Networks - rendszergazdai felhasználói felület közötti hivatkozás kapcsolatot kell létrehozni.

A hivatkozás kapcsolatot hozhat létre, a hozzárendelés másként a Palo Alto Networks - felügyeleti felhasználói Felületéhez *felhasználónév* értékét a *felhasználónév* az Azure ad-ben.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Palo Alto Networks - felügyeleti felhasználói Felületéhez, hajtsa végre a következő öt szakaszokban építőelemeket.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Palo Alto Networks - rendszergazdai felhasználói felület alkalmazás az alábbiak szerint az egyszeri bejelentkezés konfigurálása:

1. Az Azure Portalon az a **Palo Alto Networks - felügyeleti felhasználói Felületéhez** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Az "Egyszeri bejelentkezés" hivatkozásra][4]

1. Az a **egyszeri bejelentkezési** ablakban, a a **egyszeri bejelentkezési mód** jelölje ki **SAML-alapú bejelentkezés**.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

1. A **Palo Alto Networks - rendszergazdai felhasználói felület tartomány és URL-címek**, tegye a következőket:

    !["Palo Alto Networks - rendszergazdai felhasználói felület tartomány és URL-címek" egyszeri bejelentkezési adatait](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-címet a következő formátumban: *https://\<ügyfél tűzfal teljesen minősített Tartományneve > /php/login.php*.

    b. Az a **azonosító** mezőbe írja be egy URL-címet a következő formátumban: *https://\<ügyfél tűzfal teljesen minősített Tartományneve >: 443/SAML20/SP*.
    
    c. Az a **válasz URL-cím** mezőbe írja be a helyességi feltétel fogyasztói Service (ACS) URL-cím a következő formátumban: *https://\<ügyfél tűzfal teljesen minősített Tartományneve >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Az előző értékek nem valódi. Frissítse azokat a tényleges bejelentkezési URL-cím és azonosító. Az értékek beszerzéséhez forduljon [Palo Alto Networks - rendszergazdai felhasználói felület ügyfél-támogatási csapatának](https://support.paloaltonetworks.com/support). 
 
1. A Palo Alto Networks - rendszergazdai felhasználói felület alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, mert konfigurálja a jogcímek, az alábbi képen látható módon. Az attribútumértékek kezelése a **felhasználói attribútumok** szakaszában a **alkalmazásintegráció** lap az alábbiak szerint:
    
    ![Az SAML-jogkivonat attribútumai listája](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Az attribútum értékei csak példaként szolgálnak, mivel képezze le a megfelelő értékeket *felhasználónév* és *adminrole*. Nem kötelező attribútum egy másik, *accessdomain*, amely az adott virtuális rendszereken a tűzfalon való rendszergazdai hozzáférés korlátozása szolgál.
   >
        
    | Attribútum neve | Hodnota atributu |
    | --- | --- |    
    | felhasználónév | User.userPrincipalName |
    | adminrole | customadmin |

    a. Válassza ki **attribútum hozzáadása**.  
    
    ![A "Attribútum hozzáadása" gomb](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    A **attribútum hozzáadása** ablak nyílik meg.

    ![A "Attribútum hozzáadása" ablak](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** mezőbe írja be azon attribútum nevét, amely a sorhoz látható.
    
    c. Az a **érték** mezőbe írja be az attribútum értéke, amely a sorhoz látható.
    
    d. Kattintson az **OK** gombra.

    > [!NOTE]
    > Az attribútumokkal kapcsolatos további információkért tekintse meg a következő cikkeket:
    > * [Rendszergazdai felhasználói felület (adminrole) rendszergazdai szerepkör profilja](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Eszköz hozzáférési tartományi rendszergazdai felhasználói felület (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

1. Alatt **SAML-aláíró tanúsítvány**válassza **metaadatainak XML**, majd válassza ki **mentése**.

    ![A metaadatok XML letöltési hivatkozás](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![A Mentés gombra](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

1. Egy új ablakban rendszergazdaként nyissa meg a Palo Alto hálózatok tűzfal felügyeleti felhasználói Felületéhez.

1. Válassza ki a **eszköz** fülre.

    ![Az eszközök lapon](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

1. A bal oldali panelen válassza ki a **identitásszolgáltató SAML**, majd válassza ki **importálása** a metaadatait tartalmazó fájl importálása.

    ![Az importálás metaadatok szolgáló gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Az a **SAML azonosítása szolgáltató kiszolgáló profilok importálása** ablakban tegye a következőket:

    ![Az "SAML azonosítása szolgáltató kiszolgáló profil importálása" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Az a **profilnév** mezőben adjon meg egy nevet (például **Azure ad felügyeleti felhasználói Felületéhez**).
    
    b. Alatt **Identity Provider metaadatok**, jelölje be **Tallózás**, és válassza ki a korábban az Azure Portalról letöltött metadata.xml fájlt.
    
    c. Törölje a **identitás társzolgáltató tanúsítványának ellenőrzése** jelölőnégyzetet.
    
    d. Kattintson az **OK** gombra.
    
    e. A konfigurációk a tűzfalon véglegesítéséhez kattintson **véglegesítési**.

1. A bal oldali panelen válassza ki a **identitásszolgáltató SAML**, majd válassza ki a SAML-alapú identitás-szolgáltató profilban (például **Azure ad felügyeleti felhasználói Felületéhez**), amely az előző lépésben létrehozott. 

    ![Az SAML-alapú identitás szolgáltató profilban](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

1. Az a **SAML-alapú identitás szolgáltató kiszolgáló profilban** ablakban tegye a következőket:

    ![Az "SAML-alapú identitás szolgáltató kiszolgáló profilban" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Az a **Identity Provider SLO URL-cím** mezőben cserélje le a korábban importált SLO URL-cím a következő URL-cím: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Kattintson az **OK** gombra.

1. Válassza ki a Palo Alto hálózatok tűzfal felügyeleti felhasználói Felületéhez, **eszköz**, majd válassza ki **rendszergazdai szerepkörök**.

1. Válassza ki a **Hozzáadás** gombra. 

1. Az a **rendszergazdai szerepkör profil** ablakban, a a **neve** adja meg a rendszergazda szerepkör nevét (például **fwadmin**).  
    A rendszergazda szerepkör nevének egyeznie kell az SAML-rendszergazdai szerepkör attribútum nevét, amely az identitásszolgáltató által küldött. A rendszergazda szerepkör nevét és értékét a 4. lépésben létrehozott.

    ![Rendszert futtató Palo Alto hálózatok rendszergazdai szerepkör konfigurálása](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
1. Válassza ki a tűzfalat felügyeleti felhasználói Felületéhez, **eszköz**, majd válassza ki **hitelesítési profilt**.

1. Válassza ki a **Hozzáadás** gombra. 

1. Az a **hitelesítési profilt** ablakban tegye a következőket: 

    ![A "Hitelesítés profil" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Az a **neve** mezőben adjon meg egy nevet (például **AzureSAML_Admin_AuthProfile**).
    
    b. Az a **típus** legördülő listában válassza **SAML**. 
   
    c. Az a **IdP-Server-profil** legördülő listára, válassza ki a megfelelő identitás-szolgáltató kiszolgáló SAML-profilt (például **Azure ad felügyeleti felhasználói Felületéhez**).
   
    c. Válassza ki a **egyszeri kijelentkezési engedélyezése** jelölőnégyzetet.
    
    d. Az a **rendszergazdai szerepkör attribútum** mezőben megadhatja azon attribútum nevét (például **adminrole**). 
    
    e. Válassza ki a **speciális** fülre, majd a **engedélyezési lista**, jelölje be **Hozzáadás**. 
    
    ![A Hozzáadás gombra a Speciális lapon](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Válassza ki a **összes** jelölje be a jelölőnégyzetet, vagy válassza ki a felhasználókat és csoportokat, amelyek ezzel a profillal hitelesítheti.  
    Amikor egy felhasználó hitelesíti magát, a tűzfal megegyezik a társított felhasználónév vagy a csoport ebben a listában lévő bejegyzéseket ellen. Ha nem ad hozzá bejegyzéseket, nem a felhasználók hitelesíthetők.

    g. Kattintson az **OK** gombra.

1. Válassza ki ahhoz, hogy a rendszergazdák számára, hogy az Azure SAML SSO, **eszköz** > **telepítő**. Az a **telepítő** panelen válassza a **felügyeleti** lapon majd **hitelesítési beállítások**, jelölje be a **beállítások** ("fogaskerék") gomb . 

 ![A beállítások gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

1. Válassza ki a SAML-hitelesítés profilt, amely a 17. lépésben létrehozott (például **AzureSAML_Admin_AuthProfile**).

 ![A hitelesítési profilt mező](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

1. Kattintson az **OK** gombra.

1. A konfiguráció véglegesítéséhez kattintson **véglegesítési**.


> [!TIP]
> Az alkalmazás beállítása, ahogy az előző utasítások a tömör verziója olvashat a [az Azure portal](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és hozzáférhet a dokumentáció az Embedded a **konfigurációs** alul található szakaszában. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban tesztfelhasználó Britta Simon az Azure Portalon létrehozhat az alábbiak szerint:

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Az aktuális felhasználó megjelenítéséhez válassza **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Felső részén a **minden felhasználó** ablakban válassza **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A **felhasználói** ablak nyílik meg.

1. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>A Palo Alto Networks - felügyeleti felhasználói Felületéhez tesztfelhasználó létrehozása

Palo Alto Networks - rendszergazdai felhasználói felület támogatja a just-in-time-felhasználók létrehozásának. Ha a felhasználó még nem létezik, automatikusan létrejön a rendszer a sikeres hitelesítés után. Semmit nem kell az, hogy a felhasználó létrehozásához.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze felhasználói Britta Simon által biztosított hozzáférés Palo Alto Networks - rendszergazdai felhasználói felület Azure egyszeri bejelentkezés használatára. Ehhez tegye a következőket:

![A felhasználói szerepkör hozzárendelése][200] 

1. Az Azure Portalon nyissa meg a **alkalmazások** nézet, nyissa meg a **Directory** megtekintheti, és válassza ki **vállalati alkalmazások** > **összes alkalmazások**.

    ![A "Nagyvállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

1. Az a **alkalmazások** listáról válassza ki **Palo Alto Networks - felügyeleti felhasználói Felületéhez**.

    ![A futtató Palo Alto Networks - hivatkozás felügyeleti felhasználói Felületéhez](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

1. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki **Hozzáadás** , majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

1. Válassza ki a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** ablakban válassza **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

A Palo Alto Networks - a hozzáférési panelen felügyeleti felhasználói Felületéhez csempe kiválasztásakor be kell jelentkeznie automatikusan, a Palo Alto Networks - alkalmazás felügyeleti felhasználói Felületéhez.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

