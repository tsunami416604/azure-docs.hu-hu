---
title: Gyors útmutató – letiltja a hozzáférést, ha a rendszer a munkamenet kockázatát észleli Azure Active Directory Identity Protection | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhat egy Azure Active Directory (Azure AD) Identity Protection bejelentkezési kockázati feltételes hozzáférési szabályzatot a bejelentkezések munkamenet-kockázatok alapján történő blokkolásához.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5e5e26a27e13ba09ffcc97e0b2b0f1b37bc8bd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127697"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Gyors útmutató: Hozzáférés letiltása, ha a rendszer munkamenet-kockázatot észlel Azure Active Directory Identity Protection  

A környezet védelme érdekében érdemes lehet letiltani a gyanús felhasználókat a bejelentkezéshez. Azure Active Directory (Azure AD) Identity Protection elemzi az egyes bejelentkezéseket, és kiszámítja annak a valószínűségét, hogy a bejelentkezési kísérletet nem a felhasználói fiók jogos tulajdonosa végezte el. A valószínűség (alacsony, közepes, magas) a bejelentkezési kockázati szint nevű számított érték formájában van megadva. A bejelentkezési kockázat feltételének beállításával beállíthatja, hogy a bejelentkezési kockázat feltételes hozzáférési szabályzata adott bejelentkezési kockázati szintre reagáljon. 

Ez a rövid útmutató bemutatja, hogyan konfigurálható a bejelentkezési kockázat feltételes hozzáférési szabályzata, amely letiltja a bejelentkezést egy közepes és a fenti bejelentkezési kockázati szint észlelése esetén. 

![Szabályzat létrehozása](./media/quickstart-sign-in-risk-policy/1004.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- **Hozzáférés egy prémium szintű Azure ad P2 kiadáshoz** – a Azure AD Identity Protection egy prémium szintű Azure ad P2 funkció. 
- **Identitáskezelés** – az ebben a rövid útmutatóban szereplő forgatókönyvben engedélyezni kell az Identity Protection használatát. Ha nem tudja, hogyan engedélyezheti az Identitáskezelés védelmét, tekintse meg a [Azure Active Directory Identity Protection engedélyezése](../identity-protection/enable.md)című témakört.
- **Tor böngésző** – a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) úgy lett kialakítva, hogy segítsen megőrizni az adatvédelmet online. Az Identity Protection egy közepes kockázati szinttel rendelkező **Névtelen IP-címekről érkező**bejelentkezést észlel egy Tor-böngészőből. További információ: [Azure Active Directory kockázati észlelések](../reports-monitoring/concept-risk-events.md).  
- **Egy Alain Charon nevű tesztüzenet** – ha nem tudja, hogyan hozhat létre egy teszt fiókot, tekintse meg [az új felhasználó hozzáadása](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)című témakört.

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése 

Ennek a lépésnek a célja, hogy ellenőrizze, hogy a teszt fiókja hozzáférhet-e a bérlőhöz a Tor böngésző használatával.

**A bejelentkezés tesztelése:**

1. **Alain Charon**jelentkezzen [](https://portal.azure.com) be a Azure Portalba.
2. Jelentkezzen ki. 

## <a name="create-your-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása 

Az ebben a rövid útmutatóban szereplő forgatókönyv egy Tor-böngészőből származó bejelentkezést használ a **Névtelen IP-címek** kockázati észlelése során észlelt bejelentkezések létrehozásához. A kockázat észlelésének kockázati szintje közepes. A kockázati észlelésre való reagáláshoz állítsa közepesre a bejelentkezési kockázati feltételt. 

Ez a szakasz bemutatja, hogyan hozhatja létre a szükséges bejelentkezési kockázat feltételes hozzáférési szabályzatát. A házirendben állítsa be a következőket:

|Beállítás |Value|
|---     | --- |
| Felhasználók  | Alain Charon  |
| Feltételek | Bejelentkezési kockázat, közepes és újabb |
| Szabályzók | Hozzáférés letiltása |

![Szabályzat létrehozása](./media/quickstart-sign-in-risk-policy/201.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként.
2. Lépjen a [Azure ad Identity Protection lapra](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. A **Azure ad Identity Protection** lap **Konfigurálás** szakaszában kattintson a **bejelentkezési kockázati házirend**elemre.
4. A szabályzat lap hozzárendelések szakaszában kattintson a **felhasználók**elemre.
5. A **felhasználók** lapon kattintson a **felhasználók kiválasztása**elemre.
6. A **felhasználók kiválasztása** lapon válassza az **Alain Charon**, majd a **kiválasztás**lehetőséget.
7. A **felhasználók** lapon kattintson a **kész**gombra. 
8. A szabályzat lap hozzárendelések szakaszában kattintson a **feltételek**elemre.
9. A **feltételek** lapon kattintson a **bejelentkezési kockázat**elemre.
10. A **bejelentkezési kockázat** lapon válassza a **közepes vagy újabb**lehetőséget, majd kattintson a **kiválasztás**elemre. 
11. A **feltételek** lapon kattintson a **kész**gombra.
12. A házirend lap **vezérlők** szakaszában kattintson a **hozzáférés**elemre.
13. A **hozzáférés** lapon kattintson a **hozzáférés engedélyezése**elemre, jelölje be a többtényezős **hitelesítés**megkövetelése jelölőnégyzetet, majd kattintson a **kiválasztás**gombra.
14. A szabályzat lapon kattintson a **Mentés**gombra.  

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

A szabályzat teszteléséhez próbáljon meg **Alan Charon** bejelentkezni a [Azure Portalba](https://portal.azure.com) a Tor böngésző használatával. A bejelentkezési kísérletet a feltételes hozzáférési szabályzatnak le kell tiltania.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a teszt felhasználót, a Tor böngészőt, és tiltsa le a bejelentkezési kockázat feltételes hozzáférési szabályzatot:

- Ha nem tudja, hogyan törölhet egy Azure AD-felhasználót, tekintse [meg a felhasználók hozzáadása és törlése](../fundamentals/add-users-azure-active-directory.md#delete-a-user)című témakört.
- A Tor-böngésző eltávolításával kapcsolatos utasításokért [](https://tb-manual.torproject.org/uninstalling/)lásd: eltávolítása.
