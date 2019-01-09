---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a G Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a G Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 31dc45dbe8d7e3ed9b701f8623fd4e1425320316
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107463"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Oktatóanyag: Az Azure Active Directory-integráció, a G Suite-tal

Ebben az oktatóanyagban elsajátíthatja a G Suite integrálása az Azure Active Directory (Azure AD).
A G Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a G Suite, az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a G Suite-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

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

1. **K: Ez az integráció támogatja a Google Cloud Platform SSO-integráció az Azure ad-vel?**

    V: Igen. Google Cloud Platform és a Google Apps ossza meg ugyanazt a hitelesítési platformot. Így a GCP integrálni kell a Google Apps az egyszeri bejelentkezés konfigurálásához.

2. **K: Kompatibilisek Chromebooks és egyéb Látványelem eszközök az Azure AD egyszeri bejelentkezés?**
  
    V: Igen, a felhasználók is jelentkezhetnek be az Azure AD hitelesítő adataikkal Chromebook eszközeiket. Ez [G Suite-támogatási cikk](https://support.google.com/chrome/a/answer/6060880) információ arról, hogy miért felhasználók lehet kérni a hitelesítő adatokat kétszer.

3. **K: Ha egyszeri bejelentkezés engedélyezése felhasználók tudják használata az Azure AD hitelesítő adataikkal jelentkezhetnek be minden olyan Google-termék, például a Google tanterem szolgáltatásbeli, GMail, Google drive-on, YouTube, és így tovább?**

    V: Igen, attól függően, [melyik G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) úgy dönt, hogy engedélyezi vagy letiltja a szervezet számára.

4. **K: Is engedélyezhető az egyszeri bejelentkezés csak a G Suite felhasználók egy részhalmaza számára?**

    V: Nem, ne tudják bekapcsolni a egyszeri bejelentkezés azonnal minden felhasználótól megköveteli a a G Suite hitelesítéséhez az Azure AD-beli hitelesítő. A G Suite nem támogatja több identitásszolgáltató kellene, mert az identitásszolgáltató a G Suite környezetének lehet az Azure AD vagy a Google –, de nem mindkettőt egyszerre.

5. **K: Ha van bejelentkezett felhasználó Windows keresztül, azok automatikusan hitelesítéshez és a G Suite nélkül első kéri a jelszót?**

    V: Ez a forgatókönyv engedélyezésének két lehetőség van. Első lépésként felhasználók sikerült bejelentkezni a Windows 10-es eszközök [Azure Active Directory Joinon](../device-management-introduction.md). Azt is megteheti, sikerült jelentkeznek be, amely tartományhoz van csatlakoztatva egy helyszíni Active Directoryban, amely engedélyezve van az egyszeri bejelentkezés az Azure AD-n keresztül történő Windows-eszközök egy [Active Directory összevonási szolgáltatások (AD FS)](../hybrid/plan-connect-user-signin.md) központi telepítés. Mindkét módszerhez szükség, hogy hajtsa végre a lépéseket a következő oktatóanyaggal, engedélyezze az egyszeri bejelentkezés az Azure AD között és a G Suite-tal.

6. **K: Mit tegyek, ha egy "érvénytelen e-mail" hibaüzenet?**

    V: A telepítő az e-mailek attribútum szükség a felhasználók tudják jelentkezik be. Ez az attribútum nem állítható be kézzel.

    Az e-mailek attribútum töltve egy érvényes Exchange-licenccel rendelkező bármelyik felhasználó számára. Levelezési a felhasználó nem áll, ha ez a hiba fog kapni, mint az alkalmazás meg kell kapnia a hozzáférést, hogy ez az attribútum.

    Nyissa meg egy rendszergazdai fiókkal portal.office.com, majd kattintson a felügyeleti központban, számlázási, előfizetési lehetőségekről, válassza ki az Office 365-előfizetéssel, és kattintson a hozzárendelheti a felhasználók számára, jelölje be az előfizetésüket, ellenőrizze, és a jobb oldali ablaktáblában kattintson a kívánt felhasználókat Szerkesztés a licenc.

    Miután az Office 365-licenc van hozzárendelve, a alkalmazni kell néhány percet igénybe vehet. Ezt követően a user.mail attribútum lesz töltve, és fel kell oldani a problémát.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a G Suite **SP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a G Suite  **[felhasználók automatikus átadása](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>A G Suite hozzáadása a katalógusból

Az Azure AD integrálása a G Suite konfigurálása, hozzá kell G Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**A G Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **G Suite**, jelölje be **G Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![A G Suite, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés nevű tesztfelhasználó alapján G Suite-tal **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a G Suite hivatkozás kapcsolatának kell létrehozni.

Az Azure AD egyszeri bejelentkezés a G Suite tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A G Suite egyszeri bejelentkezés konfigurálása](#configure-g-suite-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[A G Suite tesztfelhasználó létrehozása](#create-g-suite-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a G Suite, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A G Suite konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **G Suite** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A G Suite-tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [G Suite ügyfél-támogatási csapatának](https://www.google.com/contact/) beolvasni ezeket az értékeket.

5. A G Suite-alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **egyedi felhasználói azonosító** van **user.userprincipalname** , de a G Suite vár ezt a képezhető le a felhasználó e-mail-címmel. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) | Adatforrás-attribútum |
    | ---------------| --------------- |
    | Egyedi felhasználói azonosító | User.mail |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **állítsa be a G Suite** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Az Azure Ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-g-suite-single-sign-on"></a>A G Suite egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a [G Suite felügyeleti konzol](https://admin.google.com/) a rendszergazdai fiók használatával.

2. Kattintson a **biztonsági**. Ha a hivatkozás nem jelenik meg, akkor előfordulhat, hogy elrejtve a **további vezérlők** menü a képernyő alján.

    ![Kattintson a Security (Biztonság) elemre.][10]

3. Az a **biztonsági** kattintson **beállítani az egyszeri bejelentkezés (SSO).**

    ![Kattintson az egyszeri Bejelentkezést.][11]

4. Hajtsa végre az alábbi konfigurációmódosításokat:

    ![Egyszeri bejelentkezés konfigurálása][12]

    a. Válassza ki **külső identitásszolgáltató beállítása az SSO**.

    b. Az a **lap URL-címe be** G Suite mezőbe illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. Az a **kijelentkezési URL-címe** G Suite mezőbe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.

    d. Az a **jelszó URL-Címének módosítása** G Suite mezőbe illessze be az értéket a **jelszó URL-Címének módosítása** Azure Portalról másolt.

    e. A G Suite az a **ellenőrző tanúsítvány**, az Azure Portalról letöltött tanúsítvány feltöltése.

    f. Válassza ki **használata egy tartományi adott kiállító**.

    g. Kattintson a **módosítások mentése**.

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

Ebben a szakaszban engedélyezze Britta Simon G Suite-hoz a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **G Suite**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **G Suite**.

    ![A G Suite hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-g-suite-test-user"></a>A G Suite tesztfelhasználó létrehozása

Ez a szakasz célja a G Suite szoftver Britta Simon nevű felhasználó létrehozásához. G Suite támogatja az automatikus kiépítést, amely alapértelmezésben engedélyezve van. Nem tartoznak, ez a szakasz a műveletek. Ha a felhasználó még nem létezik a G Suite szoftver, egy új létrejön, amikor megpróbálja elérni a G Suite szoftver.

> [!NOTE]
> Győződjön meg arról, hogy a felhasználó már létezik a G Suite, ha az Azure AD-kiépítés nem lett-e egyszeri bejelentkezés tesztelése előtt.

> [!NOTE]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Google támogatási csapatának](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a G Suite csempére kattint, akkor kell automatikusan megtörténik a a G Suite-hoz, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Felhasználók átadásának konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png