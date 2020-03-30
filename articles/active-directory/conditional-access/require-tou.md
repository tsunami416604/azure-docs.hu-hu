---
title: A feltételes hozzáférés használatához használati feltételeket igényelnek – Azure Active Directory
description: Ebben a rövid útmutatóban megtudhatja, hogyan követelheti meg, hogy a használati feltételeket elfogadja, mielőtt az Azure Active Directory feltételes hozzáférése hozzáférést biztosítana a kiválasztott felhőalkalmazásokhoz.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74380105"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Rövid útmutató: A felhőalapú alkalmazások elérése előtt el kell fogadni a használati feltételeket

Mielőtt bizonyos felhőalkalmazásokhoz a környezetében elérne, érdemes lehet a felhasználók beleegyezését kérni a használati feltételek (ToU) elfogadásához. Az Azure Active Directory (Azure AD) feltételes hozzáférése a következőket biztosítja:

- Egy egyszerű módszer a felhasználói felület konfigurálására
- A használati feltételek feltételes hozzáférési házirenden keresztüli elfogadásának megkövetelése  

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy [Azure AD feltételes hozzáférési szabályzatot,](../active-directory-conditional-access-azure-portal.md) amely megköveteli, hogy a felhasználói címet el kell fogadni a kiválasztott felhőalapú alkalmazás a környezetben.

![Szabályzat létrehozása](./media/require-tou/5555.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató forgatókönyvének végrehajtásához a következőkre van szükség:

- **Hozzáférés egy Azure AD Premium-kiadáshoz** – az Azure AD feltételes hozzáférés e-csomag Azure AD Premium-funkció.
- **Isabella Simonsen nevű tesztfiók** – Ha nem tudja, hogyan hozhat létre tesztfiókot, olvassa el a Felhőalapú felhasználók hozzáadása című [témakört.](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

A lépés célja, hogy a feltételes hozzáférési szabályzat nélküli bejelentkezési élmény megjelenítése.

**A bejelentkezés tesztelése:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) Isabella Simonsen néven.
1. Jelentkezzen ki.

## <a name="create-your-terms-of-use"></a>A használati feltételek létrehozása

Ez a szakasz a mintafelhasználói felület létrehozásának lépéseit tartalmazza. Amikor létrehoz egy főértéket, a **Feltételes hozzáférés házirendsablonokkal való kényszerítés**értékét választja ki. **Az Egyéni házirend** lehetőséget választva megnyithatja a párbeszédpanelt, hogy a feladatszolgáltató létrehozása után hozzon létre egy új feltételes hozzáférési házirendet.

**A használati feltételek létrehozása:**

1. A Microsoft Wordben hozzon létre egy új dokumentumot.
1. Írja be **a Saját használati feltételeket**, majd mentse a dokumentumot a számítógépre **mytou.pdf**néven.
1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Az Azure Portalon a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Az **Azure Active Directory (Azure Active Directory)** lap **Biztonsági** szakaszában kattintson a **Feltételes hozzáférés gombra.**

   ![Feltételes hozzáférés](./media/require-tou/03.png)

1. A **Kezelés** csoportban kattintson **a Használati feltételek gombra.**

   ![Használati feltételek](./media/require-tou/04.png)

1. A felső menüben kattintson az **Új kifejezések parancsra.**

   ![Használati feltételek](./media/require-tou/05.png)

1. Az **Új használati feltételek** lapon:

   ![Használati feltételek](./media/require-tou/112.png)

   1. A **Név** mezőbe írja be a **Saját készletkészlet beírását.**
   1. A **Megjelenítendő név** mezőbe írja be a **Saját készletkészlet beírását.**
   1. Töltse fel a használati feltételeket PDF-fájlt.
   1. **Nyelvként**válassza az **Angol**lehetőséget.
   1. A felhasználók **nak a használati feltételek kibontására lehetőséget,** válassza a **Be**lehetőséget.
   1. Kényszerítés **feltételes hozzáférésű házirendsablonokkal,** válassza az **Egyéni házirend**lehetőséget.
   1. Kattintson **a Létrehozás gombra.**

## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési házirend létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre a szükséges feltételes hozzáférési szabályzatot. A rövid útmutató forgatókönyve a következőket használja:

- Az Azure Portal helyőrzője egy felhőalapú alkalmazás, amely megköveteli a felhasználói felület kell elfogadni. 
- A mintafelhasználó a feltételes hozzáférési szabályzat teszteléséhez.  

A házirendben állítsa be a következőket:

| Beállítás | Érték |
| --- | --- |
| Felhasználók és csoportok | Simonsen Izabella |
| Felhőalapú alkalmazások | Microsoft Azure Management |
| Hozzáférés biztosítása | Saját TOU |

![Szabályzat létrehozása](./media/require-tou/1234.png)

**A feltételes hozzáférési házirend konfigurálása:**

1. Az **Új** lap **Név** mezőjébe írja be az **Isabella szükséges kezdőnév mezőjébe.**

   ![Név](./media/require-tou/71.png)

1. A **Hozzárendelés** csoportban kattintson a **Felhasználók és csoportok**elemre.

   ![Felhasználók és csoportok](./media/require-tou/06.png)

1. A **Felhasználók és csoportok** lapon:

   ![Felhasználók és csoportok](./media/require-tou/24.png)

   1. Kattintson **a Felhasználók és csoportok kijelölése**gombra, majd válassza a Felhasználók és **csoportok**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **Kijelölés** lapon válassza **az Isabella Simonsen**elemet, majd kattintson a **Kijelölés gombra.**
   1. A **Felhasználók és csoportok** lapon kattintson a **Kész gombra.**
1. Kattintson **a Felhőalapú alkalmazások**elemre.

   ![Felhőalapú alkalmazások](./media/require-tou/08.png)

1. A **Felhőalkalmazások** lapon:

   ![Felhőalapú alkalmazások kiválasztása](./media/require-tou/26.png)

   1. Kattintson **az Alkalmazások kiválasztása gombra.**
   1. Kattintson a **Kiválasztás** gombra.
   1. A **Kijelölés** lapon válassza a **Microsoft Azure Management**lehetőséget, majd kattintson a Kijelölés **gombra.**
   1. A **Felhőalapú alkalmazások** lapon kattintson a **Kész**gombra.
1. A **Hozzáférés vezérlői** csoportban kattintson a **Támogatás**gombra.

   ![Hozzáférés-vezérlés](./media/require-tou/10.png)

1. A **Grant** oldalon:

   ![Engedély](./media/require-tou/111.png)

   1. Válassza **a Hozzáférés megadása**lehetőséget.
   1. Válassza **a Saját tou lehetőséget**.
   1. Kattintson a **Kiválasztás** gombra.
1. A **Házirend engedélyezése** csoportban kattintson a **Be gombra.**

   ![Szabályzat engedélyezése](./media/require-tou/18.png)

1. Kattintson **a Létrehozás gombra.**

## <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, valószínűleg tudni szeretné, hogy a várt módon működik-e. Első lépésként használja a feltételes hozzáférés, mi van, ha a házirend eszköz szimulálja a bejelentkezést a teszt felhasználó. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

A Mi **a teendő, ha** házirend-értékelési eszköz inicializálásához állítsa be a következőket:

- **Isabella Simonsen** mint felhasználó
- **Microsoft Azure Management** felhőalapú alkalmazásként

A **Mi ha** gombra kattintva létrehoz egy szimulációs jelentést, amely a következőket jeleníti meg:

- **A tou megkövetelése az Isabella számára** **az alkalmazandó irányelvek** szerint
- **Saját TOU,** mint **Grant Controls**.

![Mi a teendő, ha a házirendeszköz](./media/require-tou/79.png)

**A feltételes hozzáférési házirend kiértékelése:**

1. A [Feltételes hozzáférés - Házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lap tetején kattintson a **Mi lenne, ha**a lehetőségre.  

   ![What If](./media/require-tou/14.png)

1. Kattintson **a Felhasználók**menü **Isabella Simonsen parancsára,** majd a **Kijelölés gombra.**

   ![Felhasználó](./media/require-tou/15.png)

1. Felhőalapú alkalmazás kiválasztása:

   ![Felhőalapú alkalmazások](./media/require-tou/16.png)

   1. Kattintson **a Felhőalapú alkalmazások**elemre.
   1. A **Felhőalapú alkalmazások lapon**kattintson az **Alkalmazások kiválasztása**gombra.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **Kijelölés** lapon válassza a **Microsoft Azure Management**lehetőséget, majd kattintson a Kijelölés **gombra.**
   1. A felhőalapú alkalmazások lapon kattintson a **Kész**gombra.
1. Kattintson **a Mi lenne, ha**a gombra.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulta, hogyan értékelheti ki a szimulált bejelentkezést. A szimuláció mellett tesztelje a feltételes hozzáférési szabályzatot is, hogy megbizonyosodjon arról, hogy az a várt módon működik.

A szabályzat teszteléséhez próbáljon meg bejelentkezni az [Azure Portalra](https://portal.azure.com) **az Isabella Simonsen** tesztfiókjával. Meg kell jelennie egy párbeszédablaknak, amely megköveteli, hogy elfogadja a használati feltételeket.

![Használati feltételek](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználót és a feltételes hozzáférési házirendet:

- Ha nem tudja, hogyan kell törölni egy Azure AD-felhasználót, olvassa el [a Felhasználók törlése az Azure AD-ből című](../fundamentals/add-users-azure-active-directory.md#delete-a-user)témakört.
- A házirend törléséhez jelölje ki a házirendet, majd kattintson a **Gyorselérési** eszköztár Törlés gombjára.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- A használati feltételek törléséhez jelölje ki azt, majd kattintson a kifejezések **törlése** gombra a tetején lévő eszköztáron.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többkori szolgáltatás megkövetelése bizonyos alkalmazásokhoz](app-based-mfa.md)
> [A hozzáférés blokkolása munkamenetkockázat észlelésekor](app-sign-in-risk.md)
