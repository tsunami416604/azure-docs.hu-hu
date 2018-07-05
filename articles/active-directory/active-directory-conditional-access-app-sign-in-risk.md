---
title: Rövid útmutató – Azure Active Directory feltételes hozzáférés a munkamenet kockázata észlelésekor elérésének letiltása |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogy hogyan konfigurálhat egy Azure Active Directory (Azure AD) feltételes hozzáférési szabályzat blokkolja a bejelentkezések alapján a munkamenet kockázatok.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, az Azure AD feltételes hozzáférés, biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 98e2f88c85d21cde2ecc7196f93d531a80b14d13
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450157"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Gyors útmutató: Letiltja a hozzáférést, az Azure Active Directory feltételes hozzáférés a munkamenet kockázata észlelésekor  

A védett környezet megtartásához érdemes blokkolja a gyanús felhasználói tevékenység insign az aláíró. [Az Azure Active Directory (Azure AD) Identity Protection](active-directory-identityprotection.md) minden bejelentkezési elemzi, és kiszámítja a valószínűsége, hogy egy bejelentkezési kísérlet nem hajtottak végre egy felhasználói fiók jogos tulajdonosa. Annak a valószínűségét (alacsony, közepes, nagy) nevű számított érték képernyőn jelzett [bejelentkezési kockázati szintek](active-directory-conditional-access-conditions.md#sign-in-risk). A bejelentkezési kockázati feltétellel beállításával konfigurálhatja a feltételes hozzáférési szabályzatot, konkrét bejelentkezési kockázati szinteknek megfelelően válaszolnak. 

Ez a rövid útmutató ismerteti, hogyan konfigurálható egy [feltételes hozzáférési szabályzat](active-directory-conditional-access-azure-portal.md) , amely blokkolja egy bejelentkezési beállított bejelentkezési kockázati szint észlelésekor. 

![Szabályzat létrehozása](./media/active-directory-conditional-access-app-sign-in-risk/1000.png)


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.



## <a name="prerequisites"></a>Előfeltételek 

Ebben az oktatóanyagban a forgatókönyv végrehajtásához szükséges:

- **Hozzáférés az Azure AD Premium P2 kiadás** – amíg feltételes hozzáférés az Azure AD Premium P1 képesség, mivel ez a rövid útmutató példahelyzet Identity Protection egy P2 kiadás kell. 

- **Identity Protection** – ebben a rövid útmutatóban példahelyzet Identity Protection engedélyezni kell. Ha nem ismeri az Identity Protection használatának engedélyezése, [engedélyezése az Azure Active Directory Identity Protection](active-directory-identityprotection-enable.md).

- **Tor böngésző** – a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) célja, hogy segít megőrizni az adatvédelemmel kapcsolatban. Identity Protection észleli a bejelentkezési, Tor böngészőből **névtelen IP-címekről történő bejelentkezések**, amely rendelkezik egy közepes méretű kockázati szintű. További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](active-directory-reporting-risk-events.md) foglalkozó cikket.  

- **Tesztfiók nevű Alain Charon** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [adja hozzá a felhőalapú felhasználók](fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése 

Ez a lépés célja, győződjön meg arról, hogy a teszt fiók hozzáférhessen-e a Tor böngészővel a bérlő.

**A bejelentkezés teszteléséhez:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , **Alain Charon**.

2. Jelentkezzen ki. 


## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása 

Ebben a rövid útmutatóban a forgatókönyvet használja a bejelentkezési Tor böngészőből létrehozni egy észlelt **névtelen IP-címekről történő bejelentkezések** kockázati esemény. A kockázati esemény kockázati szintje közepes.   

Ez a szakasz bemutatja, hogyan hozhat létre a feltételes hozzáférési szabályzat. Állítsa be a szabályzat:

|Beállítás |Érték|
|---     | --- |
| Felhasználók és csoportok | Alain Charon  |
| Felhőalkalmazások | Minden felhőalkalmazás |
| Hozzáférés | Hozzáférés letiltása |
 

![Szabályzat létrehozása](./media/active-directory-conditional-access-app-sign-in-risk/115.png)

 


**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférési rendszergazdájaként.

2. Az Azure Portalon, a bal oldali navigációs sávon kattintson **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-sign-in-risk/02.png)

3. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-sign-in-risk/03.png)
 
4. Az a **feltételes hozzáférési** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Name (Név)](./media/active-directory-conditional-access-app-sign-in-risk/108.png)

5. Az a **új** lap a **neve** szövegmezőbe írja be **közepes kockázati szint hozzáférésének blokkolása**.

    ![Name (Név)](./media/active-directory-conditional-access-app-sign-in-risk/104.png)

6. Az a **hozzárendelés** területén kattintson **felhasználók és csoportok**.

    ![Felhasználók és csoportok](./media/active-directory-conditional-access-app-sign-in-risk/06.png)

7. Az a **felhasználók és csoportok** oldalon:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-sign-in-risk/107.png)

    a. Kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki **felhasználók és csoportok**.

    b. Kattintson a **Kiválasztás** gombra.

    c. Az a **válassza** lapra, jelölje be **Alain Charon**, és kattintson a **kiválasztása**.

    d. Az a **felhasználók és csoportok** kattintson **kész**.

8. Kattintson a **Felhőalkalmazások**.

    ![Felhőalkalmazások](./media/active-directory-conditional-access-app-sign-in-risk/08.png)

9. Az a **Felhőalkalmazások** oldalon:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-sign-in-risk/109.png)

    a. Kattintson a **az összes felhőalapú alkalmazások**.

    b. Kattintson a **Done** (Kész) gombra.

10. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

    ![Hozzáférés-szabályozás](./media/active-directory-conditional-access-app-sign-in-risk/10.png)

11. Az a **Grant** oldalon:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-sign-in-risk/105.png)

    a. Válassza ki **blokkolhatja**.

    b. Kattintson a **Kiválasztás** gombra.

12. Az a **házirend engedélyezése** területén kattintson **a**.

    ![Házirend engedélyezése](./media/active-directory-conditional-access-app-sign-in-risk/18.png)

13. Kattintson a **Create** (Létrehozás) gombra.


## <a name="evaluate-a-simulated-sign-in"></a>Egy szimulált bejelentkezési kiértékelése

Most, hogy a feltételes hozzáférési szabályzat van beállítva, érdemes tudni, hogy a várt módon működik-e azt. Első lépésként, a feltételes hozzáférés használata a **mi történik, ha házirend eszközzel** egy jelentkezzen be a tesztfelhasználó szimulálásához. A szimuláció becslése a hatás bejelentkezési ebben a szabályzatok a, és a szimuláció jelentést hoz létre.  

Futtatásakor a **mi történik, ha a házirend-eszköz** ebben a forgatókönyvben a **közepes kockázati szint hozzáférésének blokkolása** alatt kell szerepelnie **érvényes szabályzatok**. 

![Felhasználó](./media/active-directory-conditional-access-app-sign-in-risk/117.png)


**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  
 
    ![What If](./media/active-directory-conditional-access-app-sign-in-risk/14.png)

2. Kattintson a **felhasználói**, jelölje be **Alan Charon** a a **felhasználók** lapon, és kattintson a **kiválasztása**.

    ![Felhasználó](./media/active-directory-conditional-access-app-sign-in-risk/116.png)

3. Mint **bejelentkezési kockázat**válassza **Közepes**.

    ![Felhasználó](./media/active-directory-conditional-access-app-sign-in-risk/119.png)


3. Kattintson a **mi történik, ha**.


## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési házirend tesztelése

Az előző szakaszban megtanulhatta egy szimulált bejelentkezési kiértékelése. A szimuláció mellett is érdemes tesztelnie győződjön meg arról, hogy az elvárt módon működik, a feltételes hozzáférési szabályzatot. 

Ha tesztelni szeretné a szabályzatot, próbálja meg, jelentkezzen be a [az Azure portal](https://portal.azure.com) , **Alan Charon** a Tor böngészővel. A bejelentkezési kísérlet a feltételes hozzáférési szabályzat által le kell tiltani.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználó számára, a Tor-böngésző és a feltételes hozzáférési szabályzatot:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [felhasználók törlése az Azure ad-ből](fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Törli a szabályzatot, válassza ki a szabályzatot, és kattintson **törlése** a gyorselérési eszköztáron.

    ![Multi-Factor Authentication](./media/active-directory-conditional-access-app-sign-in-risk/33.png)

- Eltávolítja a Tor böngésző útmutatásért lásd: [eltávolítása](https://tb-manual.torproject.org/en-US/uninstalling.html).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati feltételek elfogadása szükséges](./active-directory-conditional-access-tou.md)
> [adott alkalmazások a többtényezős hitelesítés megkövetelése](./active-directory-conditional-access-app-based-mfa.md)

