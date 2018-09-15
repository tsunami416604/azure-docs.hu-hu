---
title: Rövid útmutató – hozzáférés letiltása az Azure Active Directory Identity Protection egy munkamenet kockázati észlelésekor |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogy hogyan konfigurálhat egy Azure Active Directory (Azure AD) Identity Protection bejelentkezési kockázat feltételes hozzáférési szabályzat blokkolja a bejelentkezések alapján a munkamenet kockázatok.
services: active-directory
keywords: Identitásvédelem, alkalmazások, a feltételes hozzáférés az Azure ad-ben, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatokat a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 6879822e8451e6170bf17fff4ab66b672f711a93
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632387"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Gyors útmutató: Letiltja a hozzáférést, az Azure Active Directory Identity Protection egy munkamenet kockázati észlelésekor  

A védett környezet megtartásához érdemes gyanús felhasználó bejelentkezésének blokkolására. Az Azure Active Directory (Azure AD) Identity Protection minden bejelentkezési elemzi, és kiszámítja a valószínűsége, hogy egy bejelentkezési kísérlet nem hajtottak végre egy felhasználói fiók jogos tulajdonosa. Annak a valószínűségét (alacsony, közepes, nagy) formájában bejelentkezési kockázati szint nevű számított érték jelzi. A bejelentkezési kockázati feltétellel beállításával konfigurálhatja a bejelentkezési kockázat feltételes hozzáférési szabályzatot, konkrét bejelentkezési kockázati szinteknek megfelelően válaszolnak. 

Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a bejelentkezési kockázat feltételes hozzáférési szabályzatot, amely blokkolja a bejelentkezés során egy közepes méretű, és a fenti bejelentkezési kockázati szint észlelhető. 

![Szabályzat létrehozása](./media/quickstart-sign-in-risk-policy/1004.png)


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.



## <a name="prerequisites"></a>Előfeltételek 

Ebben az oktatóanyagban a forgatókönyv végrehajtásához szükséges:

- **Hozzáférés az Azure AD Premium P2 kiadás** – Azure AD Identity Protection az Azure AD Premium P2 szolgáltatása. 

- **Identity Protection** – ebben a rövid útmutatóban példahelyzet Identity Protection engedélyezni kell. Ha nem ismeri az Identity Protection használatának engedélyezése, [engedélyezése az Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Tor böngésző** – a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) célja, hogy segít megőrizni az adatvédelemmel kapcsolatban. Identity Protection észleli a bejelentkezési, Tor böngészőből **névtelen IP-címekről történő bejelentkezések**, amely rendelkezik egy közepes méretű kockázati szintű. További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](../reports-monitoring/concept-risk-events.md) foglalkozó cikket.  

- **Tesztfiók nevű Alain Charon** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [új felhasználó hozzáadása](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése 

Ez a lépés célja, győződjön meg arról, hogy a teszt fiók hozzáférhessen-e a Tor böngészővel a bérlő.

**A bejelentkezés teszteléséhez:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , **Alain Charon**.

2. Jelentkezzen ki. 


## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása 

Ebben a rövid útmutatóban a forgatókönyvet használja a bejelentkezési Tor böngészőből létrehozni egy észlelt **névtelen IP-címekről történő bejelentkezések** kockázati esemény. A kockázati esemény kockázati szintje közepes. A kockázati esemény válaszolni a bejelentkezési kockázati feltétellel közepes beállítása. 

Ez a szakasz bemutatja, hogyan hozhat létre a szükséges bejelentkezési kockázat feltételes hozzáférési szabályzat. Állítsa be a szabályzat:

|Beállítás |Érték|
|---     | --- |
| Felhasználók  | Alain Charon  |
| Feltételek | Bejelentkezési kockázati, közepes és afeletti |
| Vezérlők | Hozzáférés letiltása |
 

![Szabályzat létrehozása](./media/quickstart-sign-in-risk-policy/201.png)

 


**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdaként.

2. Nyissa meg a [Azure AD Identity Protection lapról](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
 
3. Az a **Azure AD Identity Protection** lap a **konfigurálása** területén kattintson **bejelentkezési kockázati házirend**.
 
4. A szabályzat lapján az a **hozzárendelések** területén kattintson **felhasználók**.

5. Az a **felhasználók** kattintson **válassza ki a felhasználók**.

6. Az a **válassza ki a felhasználók** lapra, jelölje be **Alain Charon**, és kattintson a **kiválasztása**.

7. Az a **felhasználók** kattintson **kész**. 

8. A szabályzat lapján az a **hozzárendelések** területén kattintson **feltételek**.

9. Az a **feltételek** kattintson **bejelentkezési kockázat**.

10. A a **bejelentkezési kockázat** lapon jelölje be **közepes és a fenti**, és kattintson a **válassza**. 

11. Az a **feltételek** kattintson **kész**.

12. A szabályzat lapján az a **vezérlők** területén kattintson **hozzáférés**.

13. A a **hozzáférés** kattintson **engedélyezi a hozzáférést**, jelölje be **többtényezős hitelesítés megkövetelése**, és kattintson a **válassza**.

14. A szabályzat lapján kattintson a **mentése**.  


## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési házirend tesztelése

Ha tesztelni szeretné a szabályzatot, próbálja meg, jelentkezzen be a [az Azure portal](https://portal.azure.com) , **Alan Charon** a Tor böngészővel. A bejelentkezési kísérlet a feltételes hozzáférési szabályzat által le kell tiltani.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználó számára, a Tor böngészőben, és tiltsa le a bejelentkezési kockázat feltételes hozzáférési szabályzatot:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [hozzáadása vagy törlése a felhasználók](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Eltávolítja a Tor böngésző útmutatásért lásd: [eltávolítása](https://tb-manual.torproject.org/en-US/uninstalling.html).


