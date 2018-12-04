---
title: 'Oktatóanyag: Azure Active Directory-integráció a G Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a G Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2018
ms.author: jeedes
ms.openlocfilehash: bcea7848c7331ecd326f0ccb6ab9f543ce972205
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834689"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Oktatóanyag: Azure Active Directory-integráció a G Suite-tal

Ebben az oktatóanyagban elsajátíthatja a G Suite integrálása az Azure Active Directory (Azure AD).

A G Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a G Suite, az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a G Suite-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a G Suite, a következőkre van szükség:

- Azure AD-előfizetés
- A G Suite egyszeri bejelentkezés engedélyezve van az előfizetés
- A Google Apps előfizetést vagy a Google Cloud Platform-előfizetés.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja. Ez a dokumentum létrehozása az új felhasználói élmény Single-Sign-on. Ha továbbra is használja a régit, a telepítő diferent fog kinézni. Az új felhasználói felületre, az egyszeri bejelentkezés beállításai G-Suite alkalmazás is engedélyezheti. Lépjen a **Azure AD-ben a vállalati alkalmazások**válassza **G Suite**, jelölje be **egyszeri bejelentkezés** majd kattintson a **próbálja ki az új funkciót**.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. **K: Ez a Google Cloud Platform SSO-integráció az Azure AD-integrációs támogatás?**

    V: Igen. Google Cloud Platform és a Google Apps ossza meg ugyanazt a hitelesítési platformot. Így a GCP integrálni kell a Google Apps az egyszeri bejelentkezés konfigurálásához.

2. **K: kompatibilisek Chromebooks és egyéb Látványelem eszközök az Azure AD egyszeri bejelentkezés?**
  
    V: Igen, a felhasználók is jelentkezhetnek be az Azure AD hitelesítő adataikkal Chromebook eszközeiket. Ez [G Suite-támogatási cikk](https://support.google.com/chrome/a/answer/6060880) információ arról, hogy miért felhasználók lehet kérni a hitelesítő adatokat kétszer.

3. **K: Ha egyszeri bejelentkezés engedélyezése felhasználók tudják használata az Azure AD hitelesítő adataikkal jelentkezhetnek be minden olyan Google-termék, például a Google tanterem szolgáltatásbeli, GMail, Google drive-on, YouTube, és így tovább?**

    V: Igen, attól függően, [melyik G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) úgy dönt, hogy engedélyezi vagy letiltja a szervezet számára.

4. **K: engedélyezhető az egyszeri bejelentkezés csak a G Suite felhasználók egy részhalmaza számára?**

    V: nem, ne tudják bekapcsolni a egyszeri bejelentkezés azonnal minden felhasználótól megköveteli a a G Suite hitelesítéséhez az Azure AD-beli hitelesítő. A G Suite nem támogatja több identitásszolgáltató kellene, mert az identitásszolgáltató a G Suite környezetének lehet az Azure AD vagy a Google –, de nem mindkettőt egyszerre.

5. **K: Ha van bejelentkezett felhasználó Windows keresztül, azok automatikusan hitelesítéshez és a G Suite nélkül első kéri a jelszót?**

    V: két lehetőség van ez a forgatókönyv engedélyezésének. Első lépésként felhasználók sikerült bejelentkezni a Windows 10-es eszközök [Azure Active Directory Joinon](../device-management-introduction.md). Azt is megteheti, sikerült jelentkeznek be, amely tartományhoz van csatlakoztatva egy helyszíni Active Directoryban, amely engedélyezve van az egyszeri bejelentkezés az Azure AD-n keresztül történő Windows-eszközök egy [Active Directory összevonási szolgáltatások (AD FS)](../hybrid/plan-connect-user-signin.md) központi telepítés. Mindkét módszerhez szükség, hogy hajtsa végre a lépéseket a következő oktatóanyaggal, engedélyezze az egyszeri bejelentkezés az Azure AD között és a G Suite-tal.

6. **Hiba: Érvénytelen E-mail**

    A telepítő az e-mailek attribútum szükség a felhasználók tudják jelentkezik be. Ez az attribútum nem állítható be kézzel.

    Az e-mailek attribútum töltve egy érvényes Exchange-licenccel rendelkező bármelyik felhasználó számára. Levelezési a felhasználó nem áll, ha ez a hiba fog kapni, mint az alkalmazás meg kell kapnia a hozzáférést, hogy ez az attribútum.

    Nyissa meg egy rendszergazdai fiókkal portal.office.com, majd kattintson a felügyeleti központban, számlázási, előfizetési lehetőségekről, válassza ki az Office 365-előfizetéssel, és kattintson a hozzárendelheti a felhasználók számára, jelölje be az előfizetésüket, ellenőrizze, és a jobb oldali ablaktáblában kattintson a kívánt felhasználókat Szerkesztés a licenc.

    Miután az Office 365-licenc van hozzárendelve, a alkalmazni kell néhány percet igénybe vehet. Ezt követően a user.mail attribútum lesz töltve, és fel kell oldani a problémát.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A G Suite hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-g-suite-from-the-gallery"></a>A G Suite hozzáadása a katalógusból

Az Azure AD integrálása a G Suite konfigurálása, hozzá kell G Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**A G Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **G Suite**, jelölje be **G Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A G Suite, a találatok listájában](./media/google-apps-tutorial/tutorial_gsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a G Suite, a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a G Suite tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a G Suite hivatkozás kapcsolatának kell létrehozni.

Az Azure AD egyszeri bejelentkezés a G Suite tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[A G Suite tesztfelhasználó létrehozása](#creating-a-g-suite-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a G Suite, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a G Suite-alkalmazás az egyszeri bejelentkezés konfigurálása.

**A G Suite konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **G Suite** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A G Suite-tartomány és URL-címek egyszeri bejelentkezési adatait](./media/google-apps-tutorial/tutorial_gsuite_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [G Suite ügyfél-támogatási csapatának](https://www.google.com/contact/) beolvasni ezeket az értékeket.

5. A G Suite-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Az a **forrásattribútum** listájában, selelct az attribútum értéke.

    c. Kattintson a **Save** (Mentés) gombra.

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/google-apps-tutorial/tutorial_gsuite_certificate.png) 

6. Az a **állítsa be a G Suite** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![G Suite konfigurálása](common/configuresection.png)

9. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a [G Suite felügyeleti konzol](https://admin.google.com/) a rendszergazdai fiók használatával.

10. Kattintson a **biztonsági**. Ha a hivatkozás nem jelenik meg, akkor előfordulhat, hogy elrejtve a **további vezérlők** menü a képernyő alján.

    ![Kattintson a Security (Biztonság) elemre.][10]

11. Az a **biztonsági** kattintson **beállítani az egyszeri bejelentkezés (SSO).**

    ![Kattintson az egyszeri Bejelentkezést.][11]

12. Hajtsa végre az alábbi konfigurációmódosításokat:

    ![Egyszeri bejelentkezés konfigurálása][12]

    a. Válassza ki **külső identitásszolgáltató beállítása az SSO**.

    b. Az a **lap URL-címe be** G Suite mezőbe illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. Az a **kijelentkezési URL-címe** G Suite mezőbe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.

    d. Az a **jelszó URL-Címének módosítása** G Suite mezőbe illessze be az értéket a **jelszó URL-Címének módosítása** Azure Portalról másolt.

    e. A G Suite az a **ellenőrző tanúsítvány**, az Azure Portalról letöltött tanúsítvány feltöltése.

    f. Válassza ki **használata egy tartományi adott kiállító**.

    g. Kattintson a **módosítások mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-g-suite-test-user"></a>A G Suite tesztfelhasználó létrehozása

Ez a szakasz célja a G Suite szoftver Britta Simon nevű felhasználó létrehozásához. G Suite támogatja az automatikus kiépítést, amely alapértelmezésben engedélyezve van. Nem tartoznak, ez a szakasz a műveletek. Ha a felhasználó még nem létezik a G Suite szoftver, egy új létrejön, amikor megpróbálja elérni a G Suite szoftver.

> [!NOTE]
> Győződjön meg arról, hogy a felhasználó már létezik a G Suite, ha az Azure AD-kiépítés nem lett-e egyszeri bejelentkezés tesztelése előtt.

> [!NOTE]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Google támogatási csapatának](https://www.google.com/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon G Suite-hoz a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **G Suite**.

    ![Egyszeri bejelentkezés konfigurálása](./media/google-apps-tutorial/tutorial_gsuite_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a G Suite csempére kattint, akkor kell lekérése automatikusan bejelentkezett a G Suite-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png