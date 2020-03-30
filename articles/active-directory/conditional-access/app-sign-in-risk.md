---
title: Kockázatészlelés használata az Azure Active Directory feltételes hozzáférésével
description: Ebben a rövid útmutatóban megtudhatja, hogyan konfigurálhat egy Azure Active Directory (Azure AD) feltételes hozzáférési szabályzatot a bejelentkezések munkamenet-kockázatok alapján való letiltásához.
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
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77186610"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Rövid útmutató: Hozzáférés blokkolása, ha munkamenet-kockázatot észlel az Azure Active Directory feltételes hozzáféréssel  

A környezet védelme érdekében érdemes letiltani a gyanús felhasználók bejelentkezését. [Az Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) elemzi az egyes bejelentkezések, és kiszámítja annak valószínűségét, hogy a bejelentkezési kísérlet nem hajtott végre a felhasználói fiók jogos tulajdonosa. A valószínűség (alacsony, közepes, magas) a [bejelentkezési kockázati szinteknek](concept-conditional-access-conditions.md#sign-in-risk)nevezett számított érték formájában van feltüntetve. A bejelentkezési kockázati feltétel beállításával konfigurálhatja a feltételes hozzáférési szabályzatot, hogy válaszoljon az adott bejelentkezési kockázati szintekre.

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy [feltételes hozzáférési szabályzatot,](../active-directory-conditional-access-azure-portal.md) amely blokkolja a bejelentkezést, ha egy konfigurált bejelentkezési kockázati szintet észlelt.

![Szabályzat létrehozása](./media/app-sign-in-risk/1000.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- **Hozzáférés egy Azure AD Premium P2-kiadáshoz** – Bár a feltételes hozzáférés egy Azure AD Premium P1-funkció, p2-kiadásra van szüksége, mert ebben a rövid útmutatóban az ebben a rövid útmutatóban az identitásvédelemre van szükség.
- **Identity Protection** – Ebben a rövid útmutatóban az identitásvédelem engedélyezéséhez szükséges forgatókönyv. Ha nem tudja, hogyan engedélyezheti az Identitásvédelem szolgáltatást, olvassa [el az Azure Active Directory-identitásvédelem engedélyezése című témakört.](../identity-protection/overview-identity-protection.md)
- **Tor Browser** - A [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) célja, hogy segítsen megőrizni az adatvédelmet online. Az Identity Protection észleli a tor-böngészőből érkező bejelentkezéseket névtelen IP-címekről való bejelentkezésként, amelyek közepes kockázati szinttel rendelkeznek. További információ: [Azure Active Directory-kockázatészlelések.](../reports-monitoring/concept-risk-events.md)  
- **Alain Charon nevű tesztfiók** – Ha nem tudja, hogyan hozhat létre tesztfiókot, olvassa el a Felhőalapú felhasználók hozzáadása című [témakört.](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

A lépés célja annak biztosítása, hogy a tesztfiók a Tor böngésző használatával hozzáférhessen a bérlőhöz.

**A bejelentkezés tesztelése:**

1. Jelentkezzen be [az Azure Portalra](https://portal.azure.com) **Alain Charon**néven.
1. Jelentkezzen ki.

## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési házirend létrehozása

Ebben a rövid útmutatóban a forgatókönyv egy Tor böngészőből való bejelentkezést használ az észlelt bejelentkezések létrehozásához **a névtelen IP-címek kockázatészleléséből.** A kockázatészlelés kockázati szintje közepes. A kockázatészlelésre való válaszul állítsa be a bejelentkezési kockázati feltételt közepesre. Éles környezetben a bejelentkezési kockázati feltételmagas vagy közepes és magas.

Ez a szakasz bemutatja, hogyan hozhat létre a szükséges feltételes hozzáférési szabályzatot. A házirendben állítsa be a következőket:

| Beállítás | Érték |
| --- | --- |
| Felhasználók és csoportok | Alain Charon  |
| Felhőalapú alkalmazások | Minden felhőalapú alkalmazás |
| Bejelentkezési kockázat | Közepes |
| Engedély | Fájlhozzáférés |

![Szabályzat létrehozása](./media/app-sign-in-risk/130.png)

**A feltételes hozzáférési házirend konfigurálása:**

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Az Azure Portalon a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Az **Azure Active Directory (Azure Active Directory)** lap **Biztonsági** szakaszában kattintson a **Feltételes hozzáférés gombra.**

   ![Feltételes hozzáférés](./media/app-sign-in-risk/03.png)

1. A **Feltételes hozzáférés** lap tetején kattintson a **Hozzáadás**gombra.

   ![Név](./media/app-sign-in-risk/108.png)

1. Az **Új** lap **Név** mezőjébe írja be a **Hozzáférés tiltása közepes kockázati szinthez jelölőnégyzetet.**

   ![Név](./media/app-sign-in-risk/104.png)

1. A **Hozzárendelés** csoportban kattintson a **Felhasználók és csoportok**elemre.

   ![Felhasználók és csoportok](./media/app-sign-in-risk/06.png)

1. A **Felhasználók és csoportok** lapon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/107.png)

   1. Kattintson **a Felhasználók és csoportok kijelölése**gombra, majd válassza a Felhasználók és **csoportok**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **Kijelölés** lapon válassza az **Alain Charon**lehetőséget, majd kattintson a **Kijelölés gombra.**
   1. A **Felhasználók és csoportok** lapon kattintson a **Kész gombra.**
1. Kattintson **a Felhőalapú alkalmazások**elemre.

   ![Felhőalapú alkalmazások](./media/app-sign-in-risk/08.png)

1. A **Felhőalkalmazások** lapon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/109.png)

   1. Kattintson **az Összes felhőalapú alkalmazás elemre.**
   1. Kattintson a **Done** (Kész) gombra.
1. Kattintson **a Feltételek gombra.**

   ![Hozzáférés-vezérlés](./media/app-sign-in-risk/19.png)

1. A **Feltételek** lapon:

   ![Bejelentkezés kockázati szintje](./media/app-sign-in-risk/21.png)

   1. Kattintson **a Bejelentkezési kockázat gombra.**
   1. A **konfigurálás kor**kattintson az **Igen**gombra.
   1. Bejelentkezési kockázati szintként válassza **a Közepes**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **Feltételek** lapon kattintson a **Kész gombra.**
1. A **Hozzáférés vezérlői** csoportban kattintson a **Támogatás**gombra.

   ![Hozzáférés-vezérlés](./media/app-sign-in-risk/10.png)

1. A **Grant** oldalon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/105.png)

   1. Válassza **a Hozzáférés blokkolása**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
1. A **Házirend engedélyezése** csoportban kattintson a **Be gombra.**

   ![Szabályzat engedélyezése](./media/app-sign-in-risk/18.png)

1. Kattintson **a Létrehozás gombra.**

## <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, valószínűleg tudni szeretné, hogy a várt módon működik-e. Első lépésként használja a feltételes **hozzáférés, mi van, ha a házirend eszköz** szimulálja a bejelentkezést a teszt felhasználó. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

Ha a mi a **teendő, ha házirend eszközt** futtatja ebben a forgatókönyvben, a közepes kockázati szint **hozzáférésének letiltása** a házirendek alatt **jelenik meg.**

![Felhasználó](./media/app-sign-in-risk/117.png)

**A feltételes hozzáférési házirend kiértékelése:**

1. A [Feltételes hozzáférés - Házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lap tetején kattintson a **Mi lenne, ha**a lehetőségre.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Kattintson **a Felhasználó**gombra, válassza a **Felhasználók** lapon az **Alan Charon** elemet, majd kattintson a **Kijelölés gombra.**

   ![Felhasználó](./media/app-sign-in-risk/116.png)

1. Bejelentkezési **kockázatként**válassza **a Közepes**lehetőséget.

   ![Felhasználó](./media/app-sign-in-risk/119.png)

1. Kattintson **a Mi lenne, ha**a gombra.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulta, hogyan értékelheti ki a szimulált bejelentkezést. A szimuláció mellett tesztelje a feltételes hozzáférési szabályzatot is, hogy megbizonyosodjon arról, hogy az elvárt módon működik.

A szabályzat teszteléséhez próbáljon meg alan **charonként** bejelentkezni az [Azure Portalra](https://portal.azure.com) a Tor böngésző használatával. A bejelentkezési kísérletet a feltételes hozzáférési szabályzatnak le kell tiltania.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználót, a Tor böngészőt és a feltételes hozzáférés házirendet:

- Ha nem tudja, hogyan kell törölni egy Azure AD-felhasználót, olvassa el [a Felhasználók törlése az Azure AD-ből című](../fundamentals/add-users-azure-active-directory.md#delete-a-user)témakört.
- A házirend törléséhez jelölje ki a házirendet, majd kattintson a **Gyorselérési** eszköztár Törlés gombjára.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- A Tor browser eltávolításáról az Eltávolítás című [témakörben](https://tb-manual.torproject.org/uninstalling/)talál.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A használati feltételek elfogadásának](require-tou.md)
> [megkövetelése: Bizonyos alkalmazásokhoz többkori többkori fa szükséges](app-based-mfa.md)
