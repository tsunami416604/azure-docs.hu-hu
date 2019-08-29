---
title: Gyors útmutató – hozzáférés tiltása, ha a rendszer Azure Active Directory feltételes hozzáféréssel rendelkező munkamenet-kockázatot észlel | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhat egy Azure Active Directory (Azure AD) feltételes hozzáférési szabályzatot a bejelentkezések munkamenet-kockázatok alapján történő blokkolásához.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 928900c526ec0e77f84c621f630ac5894cdb2d23
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125662"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Gyors útmutató: Hozzáférés letiltása a Azure Active Directory feltételes hozzáféréssel rendelkező munkamenet-kockázat észlelésekor  

A környezet védelme érdekében érdemes lehet letiltani a gyanús felhasználókat a bejelentkezéskor. [Azure Active Directory (Azure ad) Identity Protection](../active-directory-identityprotection.md) elemzi az egyes bejelentkezéseket, és kiszámítja annak a valószínűségét, hogy a bejelentkezési kísérletet nem a felhasználói fiók jogos tulajdonosa végezte el. A valószínűség (alacsony, közepes, magas) a [bejelentkezési kockázati szintek](conditions.md#sign-in-risk)nevű számított érték formájában van megadva. A bejelentkezési kockázati feltétel beállításával feltételes hozzáférési szabályzatot állíthat be, hogy válaszoljon a megadott bejelentkezési kockázati szintekre.

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy [feltételes hozzáférési szabályzatot](../active-directory-conditional-access-azure-portal.md) , amely letiltja a bejelentkezést a beállított bejelentkezési kockázati szint észlelésekor.

![Szabályzat létrehozása](./media/app-sign-in-risk/1000.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- **Hozzáférés egy prémium szintű Azure ad P2 kiadáshoz** – a feltételes hozzáférés egy prémium szintű Azure ad P1-képességgel rendelkezik, ezért a P2 kiadásra van szükség, mivel az ebben a rövid útmutatóban szereplő forgatókönyv az identitások védelmét igényli.
- **Identitáskezelés** – az ebben a rövid útmutatóban szereplő forgatókönyvben engedélyezni kell az Identity Protection használatát. Ha nem tudja, hogyan engedélyezheti az Identitáskezelés védelmét, tekintse meg a [Azure Active Directory Identity Protection engedélyezése](../identity-protection/enable.md)című témakört.
- **Tor böngésző** – a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) úgy lett kialakítva, hogy segítsen megőrizni az adatvédelmet online. Az Identity Protection egy közepes kockázati szinttel rendelkező névtelen IP-címekről érkező bejelentkezéseket észlel egy Tor-böngészőből. További információ: [Azure Active Directory kockázati észlelések](../reports-monitoring/concept-risk-events.md).  
- **Egy Alain Charon nevű tesztüzenet** – ha nem tudja, hogyan hozhat létre egy teszt fiókot, tekintse meg a [felhőalapú felhasználók hozzáadása](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)című témakört.

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

Ennek a lépésnek a célja, hogy ellenőrizze, hogy a teszt fiókja hozzáférhet-e a bérlőhöz a Tor böngésző használatával.

**A bejelentkezés tesztelése:**

1. **Alain Charon**jelentkezzen [](https://portal.azure.com) be a Azure Portalba.
1. Jelentkezzen ki.

## <a name="create-your-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Az ebben a rövid útmutatóban szereplő forgatókönyv egy Tor-böngészőből származó bejelentkezést használ a **Névtelen IP-címek** kockázati észlelése során észlelt bejelentkezések létrehozásához. A kockázat észlelésének kockázati szintje közepes. A kockázati észlelésre való reagáláshoz állítsa közepesre a bejelentkezési kockázati feltételt. Éles környezetben a bejelentkezési kockázati állapotot magas vagy közepes és magas értékre kell állítani.

Ez a szakasz bemutatja, hogyan hozhatja létre a kötelező feltételes hozzáférési szabályzatot. A házirendben állítsa be a következőket:

| Beállítás | Value |
| --- | --- |
| Felhasználók és csoportok | Alain Charon  |
| Felhőalkalmazások | Minden felhőalkalmazás |
| Bejelentkezési kockázat | Közepes |
| Hozzáférés | Hozzáférés letiltása |

![Szabályzat létrehozása](./media/app-sign-in-risk/130.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférési rendszergazdaként.
1. A Azure Portal a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. A **Azure Active Directory** lap **Biztonság** szakaszában kattintson a **feltételes hozzáférés**elemre.

   ![Feltételes hozzáférés](./media/app-sign-in-risk/03.png)

1. A **feltételes hozzáférés** lapon, a felső eszköztáron kattintson a **Hozzáadás**gombra.

   ![Name (Név)](./media/app-sign-in-risk/108.png)

1. Az **új** oldal **név** szövegmezőbe írja be a következőt: **hozzáférés tiltása közepes kockázati szinthez**.

   ![Name (Név)](./media/app-sign-in-risk/104.png)

1. A **hozzárendelés** szakaszban kattintson a **felhasználók és csoportok**elemre.

   ![Felhasználók és csoportok](./media/app-sign-in-risk/06.png)

1. A **felhasználók és csoportok** lapon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/107.png)

   1. Kattintson a **felhasználók és csoportok kiválasztása**elemre, majd válassza a **felhasználók és csoportok**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **kiválasztás** lapon válassza az **Alain Charon**elemet, majd kattintson a **kiválasztás**elemre.
   1. A **felhasználók és csoportok** lapon kattintson a **kész**gombra.
1. Kattintson a **Cloud apps**lehetőségre.

   ![Felhőalkalmazások](./media/app-sign-in-risk/08.png)

1. A **Cloud apps** oldalon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/109.png)

   1. Kattintson **a minden felhőalapú alkalmazás**lehetőségre.
   1. Kattintson a **Done** (Kész) gombra.
1. Kattintson a **feltételek**elemre.

   ![Hozzáférés-szabályozás](./media/app-sign-in-risk/19.png)

1. A **feltételek** lapon:

   ![Bejelentkezési kockázati szint](./media/app-sign-in-risk/21.png)

   1. Kattintson a **bejelentkezési kockázat**elemre.
   1. **Konfigurálásként**kattintson az **Igen**gombra.
   1. Bejelentkezési kockázati szintként válassza a **közepes**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **feltételek** lapon kattintson a **kész**gombra.
1. A **hozzáférés** -vezérlések szakaszban kattintson a **Grant (Engedélyezés**) elemre.

   ![Hozzáférés-szabályozás](./media/app-sign-in-risk/10.png)

1. A **támogatás** lapon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/105.png)

   1. Válassza a **hozzáférés letiltása**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
1. A **házirend engedélyezése** szakaszban kattintson **a be**gombra.

   ![Szabályzat engedélyezése](./media/app-sign-in-risk/18.png)

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, érdemes tudnia, hogy a várt módon működik-e. Első lépésként használja a feltételes hozzáférés, **Mi a teendő, ha a szabályzat eszközzel** szimulálni kívánja a felhasználó bejelentkezési adatait. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

Ha ezt a forgatókönyvet alkalmazza a **mi** esetben, ha ehhez a forgatókönyvhöz, a **közepes kockázati szinthez való hozzáférés tiltása** elemnek szerepelnie kell az **alkalmazandó házirendek**területen.

![Felhasználó](./media/app-sign-in-risk/117.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. A [feltételes hozzáférés – házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson a **What if**elemre.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Kattintson a **felhasználó**elemre, válassza az **Alan Charon** elemet a **felhasználók** lapon, majd kattintson a **kiválasztás**elemre.

   ![Felhasználó](./media/app-sign-in-risk/116.png)

1. **Bejelentkezési kockázatként**válassza a **közepes**lehetőséget.

   ![Felhasználó](./media/app-sign-in-risk/119.png)

1. Kattintson **What if**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulta, hogyan értékelheti ki a szimulált bejelentkezést. A szimuláción kívül a feltételes hozzáférési szabályzatot is tesztelni kell, hogy az a várt módon működik-e.

A szabályzat teszteléséhez próbáljon meg **Alan Charon** bejelentkezni a [Azure Portalba](https://portal.azure.com) a Tor böngésző használatával. A bejelentkezési kísérletet a feltételes hozzáférési szabályzatnak le kell tiltania.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a teszt felhasználót, a Tor böngészőt és a feltételes hozzáférési szabályzatot:

- Ha nem tudja, hogyan törölhet egy Azure AD-felhasználót, tekintse meg [a felhasználók törlése az Azure ad-ból](../fundamentals/add-users-azure-active-directory.md#delete-a-user)című témakört.
- A szabályzat törléséhez válassza ki a szabályzatot, majd kattintson a gyorselérési eszköztár **Törlés** elemére.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- A Tor-böngésző eltávolításával kapcsolatos utasításokért [](https://tb-manual.torproject.org/uninstalling/)lásd: eltávolítása.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A használati feltételek](require-tou.md)
> elfogadásának megkövetelése az[MFA megkövetelése adott alkalmazásokhoz](app-based-mfa.md)
