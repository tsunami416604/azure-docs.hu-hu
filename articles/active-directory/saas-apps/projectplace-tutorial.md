---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Projectplace |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Projectplace között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aae6de49a3df3f1e648b99aa9936d6af85fc020f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497290"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Oktatóanyag: Projectplace integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Projectplace integrálása az Azure Active Directory (Azure AD) lesz. Projectplace integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a Projectplace Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett a Projectplace az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.
* Felhasználókat automatikusan Projectplace kell létrehozni.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Projectplace egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Projectplace **SP és IDP** által kezdeményezett egyszeri Bejelentkezést, és támogatja a **igény szerinti** felhasználóátadást készíthet elő.

## <a name="adding-projectplace-from-the-gallery"></a>Projectplace hozzáadása a katalógusból

Az Azure AD integrálása a Projectplace konfigurálásához hozzá kell Projectplace a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Projectplace** kifejezést a keresőmezőbe.
1. Válassza ki **Projectplace** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Projectplace **b Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a Projectplace szüksége.

Az Azure AD SSO Projectplace tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Projectplace](#configure-projectplace)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Projectplace tesztfelhasználó létrehozása](#create-projectplace-test-user)**  , amely kapcsolódik az Azure AD felhasználói ábrázolása Projectplace-megfelelője a b Simon van.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Projectplace** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett módot, az alkalmazás előre konfigurálva, és a szükséges URL-címek vannak már előre fel van töltve az Azure-ral . A felhasználónak szüksége van a konfiguráció mentéséhez kattintson a **mentése** gombra.

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://service.projectplace.com`

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás **ikon** másolása a **alkalmazás összevonási metaadatainak URL-címe** , a követelmény alapján, és mentse a Jegyzettömbben.

   ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

1. Az a **állítsa be a Projectplace** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Configure Projectplace

Az egyszeri bejelentkezés konfigurálásához a **Projectplace** oldalon kell küldenie a másolt **alkalmazás összevonási metaadatainak URL-címe** , az Azure Portalról a [Projectplace támogatási csapatának](https://success.planview.com/Projectplace/Support) . Ez a csapat biztosítja, hogy a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

>[!NOTE]
>Az egyszeri bejelentkezés beállításainak által végrehajtandó rendelkezik a [Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support). Értesítést fog kapni, amint a konfigurálás befejeződött. 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban b Simon projectplace tartalomcsomagjához a hozzáférés biztosításával Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Projectplace**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-projectplace-test-user"></a>Projectplace tesztfelhasználó létrehozása

>[!NOTE]
>Ezt a lépést kihagyhatja, ha engedélyezve van a Projectplace-kiépítés rendelkezik. Kérje meg a [Projectplace-ügyfélszolgálathoz](https://success.planview.com/Projectplace/Support) engedélyezéséhez Jelentésközpont, miután a felhasználók kész az első bejelentkezés során létrejön a Projectplace.

Ahhoz, hogy az Azure AD-felhasználók a Projectplace bejelentkezni, hozzá kell őket a Projectplace. Adja hozzá manuálisan kell.

**Hozzon létre egy felhasználói fiókot, az alábbi lépéseket kell végrehajtani:**

1. Jelentkezzen be a **Projectplace** vállalati hely rendszergazdaként.

2. Lépjen a **személyek**, majd válassza ki **tagok**:
   
    ![Ugrás a személyek, és kattintson a tagok](./media/projectplace-tutorial/ic790228.png "személyek")

3. Válassza ki **tag hozzáadása**:
   
    ![Válassza ki a tag hozzáadása](./media/projectplace-tutorial/ic790232.png "tagok hozzáadása")

4. Az a **tag hozzáadása** területén az alábbi lépéseket.
   
    ![Tag szakasszal](./media/projectplace-tutorial/ic790233.png "új tagok")
   
    1. Az a **új tagok** mezőbe írja be az e-mail-címe érvényes Azure AD-fiókot, amely a hozzáadni kívánt.
   
    1. Kattintson a **Küldés** gombra.

   Mielőtt aktívvá válik, győződjön meg arról, hogy a fiók mutató hivatkozást tartalmazó e-mail érkezik az Azure ad-ben fióktulajdonos.

>[!NOTE]
>Bármely más felhasználóifiók-létrehozási eszköz is használhatja, vagy Projectplace adhat hozzá felhasználói fiókokat az Azure AD által biztosított API-t.


### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Projectplace-csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Projectplace, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)