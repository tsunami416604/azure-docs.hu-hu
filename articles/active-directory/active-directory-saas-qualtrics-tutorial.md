---
title: "Oktatóanyag: Azure Active Directoryval integrált Qualtrics |} Microsoft Docs"
description: "Megtudhatja, hogyan Qualtrics használata az Azure Active Directoryval az egyszeri bejelentkezés, automatizált üzembe helyezést és további engedélyezéséhez!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2fcde595a40dafda7549f5bccb582b57585b314e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Oktatóanyag: Azure Active Directoryval integrált Qualtrics
Ez az oktatóanyag célja az Azure és Qualtrics integrálását megjelenítése.  

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* Egy Qualtrics egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés

Ez az oktatóanyag befejezése után az Azure AD-felhasználók Qualtrics rendelt fog tudni egyetlen jelentkezzen be az alkalmazás a Qualtrics vállalati hely (a szolgáltatás a szolgáltató által kezdeményezett bejelentkezési), vagy használja a [a hozzáférési Panelbemutatása](active-directory-saas-access-panel-introduction.md).

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

1. Az alkalmazás Qualtrics-integráció engedélyezése
2. Egyszeri bejelentkezés (SSO) konfigurálása
3. Felhasználók átadására
4. Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "forgatókönyv")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Az alkalmazás Qualtrics-integráció engedélyezése
Ez a szakasz célja felvázoló Qualtrics az alkalmazás-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás-integráció Qualtrics, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
   ![Az Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
   ![Alkalmazások](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "alkalmazások")
4. Kattintson a **Hozzáadás** az oldal alján.
   
   ![Alkalmazás hozzáadása](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "alkalmazás hozzáadása")
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
   ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")
6. Az a **keresőmezőbe**, típus **Qualtrics**.
   
   ![Alkalmazáskatalógusában](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Alkalmazáskatalógusában")
7. Az eredmények ablaktáblájában válassza **Qualtrics**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez Qualtrics fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **Qualtrics** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "egyszeri bejelentkezés konfigurálása")
2. Az a **hová bejelentkezni Qualtrics felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "egyszeri bejelentkezés konfigurálása")
3. A a **alkalmazás URL-cím konfigurálása** lap a **Qualtrics bejelentkezési URL-cím** szövegmező, írja be az URL-cím (pl.: "*https://ssotest2ut1.qualtrics.com*"), és kattintson a **Következő**.
   
   ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "alkalmazás URL-CÍMEK konfigurálása")
4. A a **konfigurálhatja az egyszeri bejelentkezés Qualtrics** kattintson **metaadatok letöltése**, és mentse a fájlt a számítógépen a metaadat.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "egyszeri bejelentkezés konfigurálása")
5. A metaadatfájl küldeni a Qualtrics támogatási csapatával.
   
   >[!NOTE]
   >Az egyszeri bejelentkezés konfigurációs ki a Qualtrics támogatási csoport végzi el. Értesítést kap, amint a konfigurálása befejeződött.
   > 
   > 
6. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "egyszeri bejelentkezés konfigurálása")
   
## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása

Nincs művelet elem ahhoz, hogy a felhasználó Qualtrics történő konfigurálásához. Ha egy hozzárendelt felhasználó megpróbál bejelentkezni, a hozzáférési panelen Qualtrics, Qualtrics ellenőrzi, hogy a felhasználó létezik-e.  

Nincs még nincs felhasználói fiók érhető el, ha a Qualtrics automatikusan létrehozni.

## <a name="assign-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése Qualtrics, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a **Qualtrics** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
   ![Felhasználók hozzárendelése](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "felhasználók hozzárendelése")
3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
   ![Igen](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Igen")

Az SSO-beállítások tesztelésére, nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

