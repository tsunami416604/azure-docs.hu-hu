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
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: 8001f2d38ac80bb6c67419faa54bf834531f0332
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439402"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Oktatóanyag: Azure Active Directory-integráció a G Suite-tal

Ebben az oktatóanyagban elsajátíthatja a G Suite integrálása az Azure Active Directory (Azure AD).

A G Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a G Suite, az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a G Suite-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a G Suite, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A G Suite egyszeri bejelentkezés engedélyezve van az előfizetés
- A Google Apps előfizetést vagy a Google Cloud Platform-előfizetés.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Gyakori kérdések
1.  **K: Ez a Google Cloud Platform SSO-integráció az Azure AD-integrációs támogatás?**
    
    V: Igen. Google Cloud Platform és a Google Apps ossza meg ugyanazt a hitelesítési platformot. Így a GCP integrálni kell a Google Apps az egyszeri bejelentkezés konfigurálásához.


1. **K: kompatibilisek Chromebooks és egyéb Látványelem eszközök az Azure AD egyszeri bejelentkezés?**
   
    V: Igen, a felhasználók is jelentkezhetnek be az Azure AD hitelesítő adataikkal Chromebook eszközeiket. Ez [G Suite-támogatási cikk](https://support.google.com/chrome/a/answer/6060880) információ arról, hogy miért felhasználók lehet kérni a hitelesítő adatokat kétszer.

1. **K: Ha egyszeri bejelentkezés engedélyezése felhasználók tudják használata az Azure AD hitelesítő adataikkal jelentkezhetnek be minden olyan Google-termék, például a Google tanterem szolgáltatásbeli, GMail, Google drive-on, YouTube, és így tovább?**
   
    V: Igen, attól függően, [melyik G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) úgy dönt, hogy engedélyezi vagy letiltja a szervezet számára.

1. **K: engedélyezhető az egyszeri bejelentkezés csak a G Suite felhasználók egy részhalmaza számára?**
   
    V: nem, ne tudják bekapcsolni a egyszeri bejelentkezés azonnal minden felhasználótól megköveteli a a G Suite hitelesítéséhez az Azure AD-beli hitelesítő. A G Suite nem támogatja több identitásszolgáltató kellene, mert az identitásszolgáltató a G Suite környezetének lehet az Azure AD vagy a Google –, de nem mindkettőt egyszerre.

1. **K: Ha van bejelentkezett felhasználó Windows keresztül, azok automatikusan hitelesítéshez és a G Suite nélkül első kéri a jelszót?**
   
    V: két lehetőség van ez a forgatókönyv engedélyezésének. Első lépésként felhasználók sikerült bejelentkezni a Windows 10-es eszközök [Azure Active Directory Joinon](../device-management-introduction.md). Azt is megteheti, sikerült jelentkeznek be, amely tartományhoz van csatlakoztatva egy helyszíni Active Directoryban, amely engedélyezve van az egyszeri bejelentkezés az Azure AD-n keresztül történő Windows-eszközök egy [Active Directory összevonási szolgáltatások (AD FS)](../connect/active-directory-aadconnect-user-signin.md) központi telepítés. Mindkét módszerhez szükség, hogy hajtsa végre a lépéseket a következő oktatóanyaggal, engedélyezze az egyszeri bejelentkezés az Azure AD között és a G Suite-tal.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A G Suite hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-g-suite-from-the-gallery"></a>A G Suite hozzáadása a katalógusból
Az Azure AD integrálása a G Suite konfigurálása, hozzá kell G Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**A G Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **G Suite**, jelölje be **G Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A G Suite, a találatok listájában](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a G Suite, a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a G Suite tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a G Suite hivatkozás kapcsolatának kell létrehozni.

A G Suite, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a G Suite tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A G Suite tesztfelhasználó létrehozása](#create-a-g-suite-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a G Suite, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a G Suite-alkalmazás az egyszeri bejelentkezés konfigurálása.

**A G Suite konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **G Suite** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

1. Az a **G Suite-tartomány és URL-címek** szakaszra, ha a konfigurálni kívánt a **Gmail** hajtsa végre az alábbi lépéseket:

    ![A G Suite-tartomány és URL-címek egyszeri bejelentkezési adatait](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [G Suite ügyfél-támogatási csapatának](https://www.google.com/contact/) beolvasni ezeket az értékeket.

1. Az a **G Suite-tartomány és URL-címek** szakaszra, ha a konfigurálni kívánt a **Google Cloud Platform** hajtsa végre az alábbi lépéseket:

    ![A G Suite-tartomány és URL-címek egyszeri bejelentkezési adatait](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [G Suite ügyfél-támogatási csapatának](https://www.google.com/contact/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/google-apps-tutorial/tutorial_general_400.png)

1. Az a **G Suite konfigurálása** területén kattintson **G Suite konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, a SAML egyszeri bejelentkezési szolgáltatás URL-cím és a módosítás jelszó URL-címe** származó a **gyors útmutató szakaszban.**

    ![G Suite konfigurálása](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a [G Suite felügyeleti konzol](http://admin.google.com/) a rendszergazdai fiók használatával.

1. Kattintson a **biztonsági**. Ha a hivatkozás nem jelenik meg, akkor előfordulhat, hogy elrejtve a **további vezérlők** menü a képernyő alján.
   
    ![Kattintson a Security (Biztonság) elemre.][10]

1. Az a **biztonsági** kattintson **beállítani az egyszeri bejelentkezés (SSO).**
   
    ![Kattintson az egyszeri Bejelentkezést.][11]

1. Hajtsa végre az alábbi konfigurációmódosításokat:
   
    ![Egyszeri bejelentkezés konfigurálása][12]
   
    a. Válassza ki **külső identitásszolgáltató beállítása az SSO**.

    b. Az a **lap URL-címe be** G Suite mezőbe illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    c. Az a **kijelentkezési URL-címe** G Suite mezőbe illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt. 

    d. Az a **jelszó URL-Címének módosítása** G Suite mezőbe illessze be az értéket a **jelszó URL-Címének módosítása** Azure Portalról másolt. 

    e. A G Suite az a **ellenőrző tanúsítvány**, az Azure Portalról letöltött tanúsítvány feltöltése.

    f. Válassza ki **használata egy tartományi adott kiállító**.

    g. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/google-apps-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/google-apps-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/google-apps-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/google-apps-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-g-suite-test-user"></a>A G Suite tesztfelhasználó létrehozása

Ez a szakasz célja a G Suite szoftver Britta Simon nevű felhasználó létrehozásához. G Suite támogatja az automatikus kiépítést, amely alapértelmezésben engedélyezve van. Nem tartoznak, ez a szakasz a műveletek. Ha a felhasználó még nem létezik a G Suite szoftver, egy új létrejön, amikor megpróbálja elérni a G Suite szoftver.

>[!NOTE] 
>Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Google támogatási csapatának](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon G Suite-hoz a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a G Suite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **G Suite**.

    ![A G Suite hivatkozásra az alkalmazások listáját](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a G Suite csempére kattint, akkor kell lekérése automatikusan bejelentkezett a G Suite-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png

