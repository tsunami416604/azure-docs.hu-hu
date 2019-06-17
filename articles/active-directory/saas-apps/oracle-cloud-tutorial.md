---
title: 'Oktatóanyag: Az Oracle Felhőbeli infrastruktúra-konzol az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Oracle Felhőbeli infrastruktúra-konzol közötti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a034e7dc06defd701b58ec283ee161f5557c075d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095449"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Oktatóanyag: Oracle Felhőbeli infrastruktúra konzolon integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Oracle Felhőbeli infrastruktúra konzolon integrálása az Azure Active Directory (Azure AD) lesz. Oracle Felhőbeli infrastruktúra-konzol és az Azure AD integrálása, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá az Oracle Felhőbeli infrastruktúra-konzol Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett Oracle Felhőbeli infrastruktúra-konzol az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Oracle Felhőbeli infrastruktúra konzolon egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Oracle Felhőbeli infrastruktúra konzolon **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Oracle Felhőbeli infrastruktúra konzolon hozzáadása a katalógusból

Az Azure AD integrálása a Oracle Felhőbeli infrastruktúra-konzol konfigurálása, hozzá kell Oracle Felhőbeli infrastruktúra-konzol a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Oracle Felhőbeli infrastruktúra konzolon** kifejezést a keresőmezőbe.
1. Válassza ki **Oracle Felhőbeli infrastruktúra konzolon** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO-Oracle Felhőbeli infrastruktúra konzol segítségével egy teszt nevű felhasználó **b Simon**. Az SSO működjön kell egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az Oracle Felhőbeli infrastruktúra-konzolon.

Az Azure AD SSO-Oracle Felhőbeli infrastruktúra-konzol tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Oracle Felhőbeli infrastruktúra-konzol konfigurálása az](#configure-oracle-cloud-infrastructure-console)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Hozzon létre Oracle Felhőbeli infrastruktúra konzolon tesztfelhasználót](#create-oracle-cloud-infrastructure-console-test-user)**  szeretné, hogy egy megfelelője a b Simon Oracle Felhőbeli infrastruktúra-konzolon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Oracle Felhőbeli infrastruktúra konzolon** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyetlen bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

   > [!NOTE]
   > A szolgáltató származó metaadatfájl kap a **konfigurálása Oracle Felhőbeli infrastruktúra konzolon egyszeri bejelentkezés** az oktatóanyag szakaszában.
    
   1. Kattintson a **metaadatfájl feltöltése**.

   1. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

   1. A metaadatfájl sikeres feltöltését követően a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** szakasz szövegmezőbe.
    
      > [!NOTE]
      > Ha a **azonosító** és **válasz URL-cím** értékek nem automatikus polulated lekérése, majd adja meg az értékeket manuálisan a követelmény alapján.

      Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: ` https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Oracle Felhőbeli infrastruktúra-konzol ügyfél-támogatási csapatának](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **összevonási metaadatainak XML** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Oracle Felhőbeli infrastruktúra konzolalkalmazást a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

   ![image](common/edit-attribute.png)

1. Emellett a fent Oracle Felhőbeli infrastruktúra Konzolalkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. Az a **felhasználói attribútumok & jogcímek** szakaszában a **csoportjogcímek (előzetes verzió)**  párbeszédpanelen hajtsa végre az alábbi lépéseket:

   1. Kattintson a **toll** melletti **azonosító értékét nevet**.

   1. Válassza ki **állandó** , **válasszon azonosító formátuma**.
 
   1. Kattintson a **Save** (Mentés) gombra.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Kattintson a **toll** melletti **csoportok visszaküldött jogcímek**.

   1. Válassza ki **biztonsági csoportok** választógomb listájából.

   1. Válassza ki **forrásattribútumának Attribútumhierarchiája** , **csoportazonosító**.

   1. Ellenőrizze **testreszabása a csoportos jogcímet neve**.

   1. Az a **neve** szövegmezőben **groupName**.

   1. Az a **Namespace (nem kötelező)** szövegmezőben `https://auth.oraclecloud.com/saml/claims`.

   1. Kattintson a **Save** (Mentés) gombra.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Az a **Oracle Felhőbeli infrastruktúra konzolon beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Oracle Felhőbeli infrastruktúra-konzol konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Oracle Felhőbeli infrastruktúra-konzolt rendszergazdaként.

1. Kattintson a bal oldali menüben, majd kattintson a **identitás** majd keresse meg a **összevonási**.

   ![Konfiguráció](./media/oracle-cloud-tutorial/config01.png)

1. Mentse a **szolgáltató metaadatait tartalmazó fájl** kattintva a **töltse le a jelen dokumentum** hivatkozásra, és töltse fel azt a **alapszintű SAML-konfigurációja** szakaszában az Azure portal, majd Kattintson a **identitásszolgáltató hozzáadása**.

   ![Konfiguráció](./media/oracle-cloud-tutorial/config02.png)

1. Az a **identitásszolgáltató hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

   ![Konfiguráció](./media/oracle-cloud-tutorial/config03.png)

   1. Az a **neve** szöveg mezőben adja meg a nevet.

   1. Az a **leírás** szöveg mezőbe írja be a leírást.

   1. Válassza ki **MICROSOFT ACTIVE DIRECTORY ÖSSZEVONÁSI szolgáltatással (AD FS) vagy a SAML 2.0-s szabványnak megfelelő IDENTITÁSSZOLGÁLTATÓ** , **típus**.

   1. Kattintson a **Tallózás** feltöltése az Azure Portalról letöltött összevonási metaadatainak XML.

   1. Kattintson a **Folytatás** és az a **identitásszolgáltató szerkesztése** szakaszban a következő lépésekkel:

      ![Konfiguráció](./media/oracle-cloud-tutorial/config09.png)

   1. A **IDENTITY PROVIDER csoport** mezőknél adja meg a csoport nevének és a csoport azonosítója, amely az Azure Portal webhelyen állíthatja be. A csoport képezhető le a megfelelő csoporthoz kell **OCI csoport** mező.

   1. A beállításnak az Azure portal és a szervezet kell több csoportot leképezheti. Kattintson a **+ leképezés hozzáadása** igény szerint annyi csoportokat szeretne hozzáadni.

   1. Kattintson a **Submit** (Küldés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B. Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával Oracle Felhőbeli infrastruktúra-konzol Azure egyszeri bejelentkezés használatára b Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Oracle Felhőbeli infrastruktúra konzolon**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

   ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Oracle Felhőbeli infrastruktúra konzolon tesztfelhasználó létrehozása

 Oracle Felhőbeli infrastruktúra-konzol támogatja a just-in-time-kiépítés, amely alapértelmezés szerint ki van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó létrehozása nem hozzáférési és hozhat létre a felhasználó még nincs szükség tett kísérlet során.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Az Oracle Felhőbeli infrastruktúra konzolon csempe kiválasztásakor a hozzáférési panelen, a rendszer átirányítja az Oracle Felhőbeli infrastruktúra konzolon bejelentkezési oldal. Válassza ki a **IDENTITÁSSZOLGÁLTATÓ** a legördülő menüből, majd kattintson a **Folytatás** bejelentkezni az alább látható módon. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfiguráció](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
