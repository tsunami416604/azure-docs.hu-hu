---
title: 'Oktatóanyag: Azure Active Directory-integráció SCC életciklusával |} Microsoft Docs'
description: Útmutató SCC életciklusa az Azure Active Directoryval az egyszeri bejelentkezés, automatikus kiépítésének, és több!
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Oktatóanyag: Azure Active Directoryval integrált SCC életciklusa
Ez az oktatóanyag célja az Azure és SCC életciklus integrálása megjelenítése.  

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* Egy SCC életciklusa az egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés

Ez az oktatóanyag befejezése után az Azure AD-felhasználók SCC életciklus rendelt fog tudni egyetlen jelentkezzen be az alkalmazás a SCC életciklus vállalati hely (a szolgáltatás a szolgáltató által kezdeményezett bejelentkezési), vagy használja a [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

1. Az alkalmazás SCC életciklus-integráció engedélyezése
2. Egyszeri bejelentkezés (SSO) konfigurálása
3. Felhasználók átadására
4. Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "forgatókönyv")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Az alkalmazás SCC életciklus-integráció engedélyezése
Ez a szakasz célja felvázoló SCC életciklusa az alkalmazás-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás-integráció SCC életciklusát, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
    ![Az Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "alkalmazások")
4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazás hozzáadása](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "alkalmazás hozzáadása")
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")
6. Az a **keresőmezőbe**, típus **SCC életciklus**.
   
    ![Alkalmazáskatalógusában](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Alkalmazáskatalógusában")
7. Az eredmények ablaktáblájában válassza **SCC életciklus**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![SCC életciklus](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC életciklusa")
   
## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez SCC életciklus fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **SCC életciklus** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a ** konfigurálása egyszeri bejelentkezés ** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "egyszeri bejelentkezés konfigurálása")
2. A a **hová bejelentkezni SCC életciklus felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "egyszeri bejelentkezés konfigurálása")
3. A a **alkalmazás URL-cím konfigurálása** lap a **URL-cím bejelentkezési** szövegmező, írja be az URL-cím segítségével a felhasználók jelentkezzen be a SCC életciklus-alkalmazás a következő minta használatával "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*", és kattintson a **következő**.
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "alkalmazás URL-CÍMEK konfigurálása")
4. A a **konfigurálhatja az egyszeri bejelentkezés SCC életciklus** lapján kattintson **metaadatok letöltése**, és mentse helyileg a számítógépen metaadatait tartalmazó fájl.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "egyszeri bejelentkezés konfigurálása")
5. Továbbítsa a SCC életciklus támogatási csoportnak adott metaadatait tartalmazó fájl.
   
   >[!NOTE]
   >Egyszeri bejelentkezés ki engedélyezni lehessen az SCC életciklus támogatási csapatával.
   > 
   > 

6. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "egyszeri bejelentkezés konfigurálása")
   
## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása

Ahhoz, hogy az Azure AD-felhasználók SCC életciklus bejelentkezni, akkor ki kell építenie SCC életciklus be. Nincs művelet elem felhasználói kiépítés SCC életciklus konfigurálhatók.

Ha egy hozzárendelt felhasználó próbál bejelentkezni az SCC életciklusát, SCC életciklus fiók automatikusan létrejön, szükség esetén.

>[!NOTE]
>Bármely más SCC életciklus felhasználói fiók létrehozása eszközök, vagy API-k biztosítja SCC életciklus rendelkezés AAD felhasználói fiókokhoz.
> 
> 

## <a name="assign-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése SCC életciklusát, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a ** SCC életciklus ** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
    ![Felhasználók hozzárendelése](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "felhasználók hozzárendelése")
3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
    ![Igen](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Igen")

Az SSO-beállítások tesztelésére, nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

