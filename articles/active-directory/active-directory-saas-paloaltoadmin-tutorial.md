---
title: 'Oktatóanyag: Azure Active Directory integrálása Palo Alto hálózatok - rendszergazda felhasználói felület |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Palo Alto hálózatok - rendszergazda felhasználói felület közötti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aa3366810a40b004fe510cb2909f8da0f3513ddb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350338"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Az Azure Active Directory integrálása Palo Alto hálózatok - rendszergazda felhasználói felület

Ebben az oktatóanyagban elsajátíthatja Azure Active Directory (Azure AD) integrálása Palo Alto hálózatok - rendszergazda felhasználói felületén.

Integráló Azure ad Palo Alto hálózatokkal - rendszergazda felhasználói felületén, töltse le a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Palo Alto hálózatok - rendszergazda felhasználói felület szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy az beszerzése az automatikus bejelentkezést Palo Alto hálózatokhoz - rendszergazda felhasználói felület (egyszeri bejelentkezés, vagy az SSO) az Azure AD-fiókok.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Az Azure AD SaaS alkalmazásintegráció, lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Palo Alto hálózatokkal - rendszergazda felhasználói felületén, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Palo Alto hálózatok új generációs tűzfal vagy Panorámaképe (a tűzfalak központi felügyeleti rendszerhez)

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, azt javasoljuk, hogy végezzen *nem* használja az éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. 

Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

* Palo Alto hálózatok - hozzáadása a felügyeleti felhasználói felület a gyűjteményből
* És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Adja hozzá a Palo Alto hálózatok - felügyeleti felhasználói felület a gyűjteményből
Palo Alto hálózatok - rendszergazda felhasználói felületén, az integráció az Azure AD konfigurálása hozzáadása Palo Alto hálózatok - rendszergazda felhasználói Felületet a gyűjteményből a a következőképpen felügyelt SaaS-alkalmazások listája:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![A "Vállalati alkalmazások" ablak][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra az ablak tetején.

    ![Az "új alkalmazás" gomb][3]

4. Írja be a keresőmezőbe, **Palo Alto hálózatok - rendszergazda felhasználói felület**, jelölje be **Palo Alto hálózatok - rendszergazda felhasználói felület** az eredmények listájában, és válassza a **Hozzáadás**.

    ![Palo Alto hálózatok - felügyeleti felhasználói felület az eredménylistában](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálhatja, és "Britta Simon." nevű tesztfelhasználó alapján Palo Alto hálózatokkal - rendszergazda felhasználói felületén, az Azure AD az egyszeri bejelentkezés tesztelése

Az egyszeri bejelentkezés működéséhez az Azure AD a Palo Alto hálózatok - rendszergazda felhasználói felület felhasználói és az Azure ad-ben a megfelelő azonosítania kell. Más szóval Palo Alto hálózatok - rendszergazda felhasználói felület azonos felhasználói, valamint az Azure AD-felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

A hivatkozás kapcsolat létrehozására, rendelje hozzá a Palo Alto hálózatokként - rendszergazda felhasználói felület *felhasználónév* értékének a *felhasználónév* az Azure ad-ben.

Az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal - rendszergazda felhasználói felületén, tesztelése és konfigurálása végezze el a következő öt szakaszokban építőelemeit.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez a Palo Alto hálózatokon - rendszergazda felhasználói felület alkalmazás a következő módon:

1. Az Azure portálon a a **Palo Alto hálózatok - rendszergazda felhasználói felület** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Az "Egyszeri bejelentkezés" hivatkozásra][4]

2. Az a **egyszeri bejelentkezés** ablakban, a a **egyszeri bejelentkezés mód** mezőben válassza **SAML-alapú bejelentkezés**.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. A **Palo Alto hálózatok - rendszergazda felhasználói felület tartomány és az URL-címek**, tegye a következőket:

    ![Az egyszeri bejelentkezés információkat "Palo Alto hálózatokat - rendszergazda felhasználói felület tartomány és az URL-címek"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Az a **bejelentkezési URL-cím** mezőbe, írja be egy URL-címet a következő formátumban: *https://\<ügyfél tűzfal teljesen minősített Tartományneve > /php/login.php*.

    b. Az a **azonosító** mezőbe, írja be egy URL-címet a következő formátumban: *https://\<ügyfél tűzfal teljesen minősített Tartományneve >: 443/SAML20/SP*.
    
    c. Az a **válasz URL-CÍMEN** mezőbe írja be a helyességi feltétel fogyasztói Service (ACS) URL-cím a következő formátumban: *https://\<ügyfél tűzfal teljesen minősített Tartományneve >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Az előző értékei nem valódi. Frissítse azokat a tényleges bejelentkezési URL-cím és azonosítója. Az értékek beszerzéséhez forduljon [Palo Alto hálózatok - rendszergazda felhasználói felület ügyfél-támogatási csoport](https://support.paloaltonetworks.com/support). 
 
4. Palo Alto hálózatok - rendszergazda felhasználói felület alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban, mert konfigurálása a jogcímek, az alábbi ábrán látható módon. Attribútum értékének kezelése a **felhasználói attribútumok** szakasza a **alkalmazásintegráció** lap a következő tevékenységek végrehajtásával:
    
    ![A SAML-jogkivonat attribútumok listája](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Az attribútum értékei csak példák, mert a megfelelő értékeket leképezése *felhasználónév* és *adminrole*. Nincs más nem kötelező attribútum, *accessdomain*, amellyel rendszergazdai hozzáférés korlátozása adott virtuális rendszereken a tűzfalon.
   >
        
    | Attribútum neve | Attribútum-érték |
    | --- | --- |    
    | felhasználónév | User.userPrincipalName |
    | adminrole | customadmin |

    a. Válassza ki **Hozzáadás attribútum**.  
    
    ![A "Hozzáadás attribútum" gombra](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    A **attribútum hozzáadása** ablak nyílik meg.

    ![A "Hozzáadás attribútum" ablak](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** mezőbe írja be az adott sorhoz feltüntetett attribútumot nevet.
    
    c. Az a **érték** mezőbe írja be az adott sorhoz feltüntetett attribútumot érték.
    
    d. Kattintson az **OK** gombra.

    > [!NOTE]
    > Kapcsolatos további információkért tekintse meg a következő cikkeket:
    > * [Rendszergazdai szerepkör profilja felügyeleti felhasználói felület (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Eszköz hozzáférési tartományi rendszergazda felhasználói felülete (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. A **SAML-aláíró tanúsítványa**, jelölje be **metaadatainak XML-kódja**, majd válassza ki **mentése**.

    ![A metaadatok XML-kód letöltése hivatkozás](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![A Mentés gombra](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

6. Nyissa meg a Palo Alto hálózatok tűzfal felügyeleti felhasználói felület rendszergazdaként egy új ablakban.

7. Válassza ki a **eszköz** fülre.

    ![Az eszközök lapon](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. A bal oldali panelen válassza ki a **SAML-Identitásszolgáltatóként**, majd válassza ki **importálása** a metaadatait tartalmazó fájl importálása.

    ![Az importálás metaadatok fájl gombra](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Az a **SAML azonosítása szolgáltató kiszolgáló profilok importálása** ablakban tegye a következőket:

    ![A "SAML azonosítása szolgáltató kiszolgáló profil importálása" ablak](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Az a **profilnév** adja meg a nevét (például **AzureAD felügyeleti felhasználói felület**).
    
    b. A **Identity Provider metaadatok**, jelölje be **Tallózás**, és válassza ki az Azure-portálról korábban letöltött metadata.xml fájlt.
    
    c. Törölje a jelet a **érvényesítése Identitástanúsítvány szolgáltató** jelölőnégyzetet.
    
    d. Kattintson az **OK** gombra.
    
    e. Véglegesíti a konfigurációk a tűzfalon, válassza ki a **véglegesítési**.

10. A bal oldali panelen válassza ki a **SAML-Identitásszolgáltatóként**, majd válassza ki a SAML-alapú identitás szolgáltató profilt (például **AzureAD felügyeleti felhasználói felület**), amelyet az előző lépésben hozott létre. 

    ![A SAML-alapú identitás szolgáltató profil](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. Az a **SAML-alapú identitás szolgáltató kiszolgáló profil** ablakban tegye a következőket:

    ![A "Profil SAML Identity Provider" ablak](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Az a **Identity Provider SLO URL-cím** mezőbe cserélje le a korábban importált SLO URL-cím a következő URL-cím: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Kattintson az **OK** gombra.

12. Válassza ki a Palo Alto hálózatok tűzfal felügyeleti felhasználói felület **eszköz**, majd válassza ki **rendszergazdai szerepkörök**.

13. Válassza ki a **Hozzáadás** gombra. 

14. Az a **rendszergazdai szerepkör profil** ablakban, a a **neve** adja meg a rendszergazdai szerepkör nevét (például **fwadmin**).  
    A rendszergazda szerepkör nevét meg kell felelnie az identitásszolgáltató által küldött SAML-rendszergazdai szerepkör attribútum nevét. A 4. lépésben létrehozott rendszergazdai szerepkör nevét és értékét.

    ![Palo Alto hálózatok rendszergazdai szerepkör konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Jelölje be a tűzfal felügyeleti felhasználói felület **eszköz**, majd válassza ki **hitelesítési profil**.

16. Válassza ki a **Hozzáadás** gombra. 

17. Az a **hitelesítési profil** ablakban tegye a következőket: 

    ![A "Hitelesítés profil" ablak](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Az a **neve** adja meg a nevét (például **AzureSAML_Admin_AuthProfile**).
    
    b. Az a **típus** legördülő listában válassza **SAML**. 
   
    c. Az a **IdP Server-profil** legördülő listára, válassza ki a megfelelő szolgáltató Identitáskiszolgálók SAML-profilt (például **AzureAD felügyeleti felhasználói felület**).
   
    c. Válassza ki a **engedélyezése egyetlen kijelentkezési** jelölőnégyzetet.
    
    d. Az a **rendszergazdai szerepkör attribútum** mezőbe írja be az attribútumnak a nevét (például **adminrole**). 
    
    e. Válassza ki a **speciális** fülre, majd a **engedélyezése lista**, jelölje be **hozzáadása**. 
    
    ![A Hozzáadás gombra a Speciális lapon](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Válassza ki a **összes** jelölőnégyzet jelölését, vagy válassza ki a felhasználókat és csoportokat, amely képes hitelesíteni az ezzel a profillal.  
    Amikor egy felhasználó hitelesíti magát, a tűzfal felel meg a társított felhasználónév vagy a csoport ebben a listában szereplő bejegyzések alapján. Ha nem adja hozzá bejegyzéseket, egy felhasználó sem képes hitelesíteni.

    g. Kattintson az **OK** gombra.

18. Ahhoz, hogy a rendszergazdák számára, hogy SAML SSO Azure használatával, jelölje ki a **eszköz** > **telepítő**. A a **telepítő** ablaktáblán válassza előbb a **felügyeleti** fülre, majd a **hitelesítési beállítások**, jelölje be a **beállítások** ("áttételi") gombra . 

 ![A beállítások gombra](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Válassza ki a 17. lépésben létrehozott SAML-alapú hitelesítés profilt (például **AzureSAML_Admin_AuthProfile**).

 ![A hitelesítési profil mező](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Kattintson az **OK** gombra.

21. Véglegesítse a konfigurációs, jelölje be **véglegesítési**.


> [!TIP]
> Hoz létre az alkalmazást, mert egy előző utasításait tömör verziója elolvashatja a [Azure-portálon](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a a beágyazott dokumentációjában találhatók a **konfigurációs** szakasz alján. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz az alábbi lépésekkel hoz létre tesztfelhasználó Britta Simon az Azure-portálon:

![Hozzon létre egy Azure AD-teszt felhasználó][100]

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Az aktuális felhasználó listáját jeleníti meg, jelölje be **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Felső részén a **minden felhasználó** ablakban válassza ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A **felhasználói** ablak nyílik meg.

4. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Palo Alto hálózatok - rendszergazda felhasználói felület tesztfelhasználó létrehozása

Palo Alto hálózatok - rendszergazda felhasználói felület támogatja a létesítést just-in-time felhasználó. Ha a felhasználó nem létezik, automatikusan létrejön a rendszer a sikeres hitelesítés után. Önnek a felhasználó létrehozásához kell semmilyen műveletet.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze felhasználói Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Palo Alto hálózatok - rendszergazda felhasználói felület használata. Ehhez tegye a következőket:

![A felhasználói szerepkör hozzárendelése][200] 

1. Az Azure portálon, nyissa meg a **alkalmazások** nézet, keresse fel a **Directory** megtekintéséhez, majd válassza ki **vállalati alkalmazások** > **összes alkalmazások**.

    ![A "Vállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

2. Az a **alkalmazások** listáról válassza ki **Palo Alto hálózatok - rendszergazda felhasználói felület**.

    ![Palo Alto hálózatok - rendszergazda felhasználói felület hivatkozás](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki **Hozzáadás** , majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

6. Válassza ki a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** ablakban válassza ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Palo Alto hálózatok - rendszergazda felhasználói felület csempe a hozzáférési panelen kiválasztásakor be kell jelentkeznie automatikusan a Palo Alto hálózatokhoz - rendszergazda felhasználói felület alkalmazás.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

