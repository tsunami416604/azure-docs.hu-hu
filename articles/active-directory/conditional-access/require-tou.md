---
title: A feltételes hozzáférés használati feltételeket igényel – Azure Active Directory
description: Ebből a rövid útmutatóból megtudhatja, hogyan kérheti a használati feltételek elfogadását, mielőtt a kiválasztott felhőalapú alkalmazásokhoz való hozzáférést Azure Active Directory feltételes hozzáférési jogosultsággal adja meg.
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
ms.openlocfilehash: 53f99456449e778ff7c1fd3ab096e1afd3562c1e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88948861"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Rövid útmutató: a használati feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt

A környezet bizonyos felhőalapú alkalmazásaihoz való hozzáférés előtt érdemes lehet a felhasználók beleegyezni a használati feltételek elfogadásával. A Azure Active Directory (Azure AD) feltételes hozzáférés a következőket biztosítja:

- Egyszerű módszer a felhasználási feltételek konfigurálására
- A használati feltételek feltételes hozzáférési szabályzattal való elfogadásának megkövetelése  

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy olyan [Azure ad feltételes hozzáférési szabályzatot](./overview.md) , amely megköveteli, hogy a környezetében egy kiválasztott felhőalapú alkalmazásnak el kell fogadnia a felhasználási feltételeket.

![Szabályzat létrehozása](./media/require-tou/5555.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a rövid útmutatóban szereplő forgatókönyv végrehajtásához a következőkre lesz szüksége:

- **Hozzáférés egy prémium szintű Azure ad kiadáshoz** – az Azure ad feltételes hozzáférés egy prémium szintű Azure ad képesség.
- **Egy Isabella Simonsen nevű tesztüzenet** – ha nem tudja, hogyan hozhat létre egy teszt fiókot, tekintse meg a [felhőalapú felhasználók hozzáadása](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)című témakört.

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

Ennek a lépésnek a célja, hogy betekintse a bejelentkezési élményt feltételes hozzáférési szabályzat nélkül.

**A bejelentkezés tesztelése:**

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) Isabella Simonsen.
1. Jelentkezzen ki.

## <a name="create-your-terms-of-use"></a>Használati feltételek létrehozása

Ez a szakasz a minta felhasználási feltételek létrehozásának lépéseit ismerteti. A használati feltételek létrehozásakor ki kell választania egy értéket a **kényszerített hozzáférési házirend-sablonokkal**. Ha az **Egyéni házirendet** választja, megnyílik a párbeszédpanel, amely azonnal létrehoz egy új feltételes hozzáférési szabályzatot, amint a rendszer létrehozta a felhasználási feltételeket.

**A használati feltételek létrehozása:**

1. A Microsoft Wordben hozzon létre egy új dokumentumot.
1. Írja be a **saját használati feltételeket**, majd mentse a dokumentumot a számítógépen **mytou.pdf**.
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférési rendszergazdaként.
1. A Azure Portal a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

   ![Azure Active Directory](./media/require-tou/02.png)

1. A **Azure Active Directory** lap **Biztonság** szakaszában kattintson a **feltételes hozzáférés**elemre.

   ![Feltételes hozzáférés](./media/require-tou/03.png)

1. A **kezelés** szakaszban kattintson a **használati feltételek**elemre.

   ![Használati feltételek](./media/require-tou/04.png)

1. A felső menüben kattintson az **új feltételek**elemre.

   ![Használati feltételek](./media/require-tou/05.png)

1. Az **új használati feltételek** lapon:

   ![Használati feltételek](./media/require-tou/112.png)

   1. A **név** szövegmezőbe írja be a **saját felhasználási**feltételeket.
   1. A **megjelenítendő név** szövegmezőbe írja be a **saját felhasználási**feltételeket.
   1. Töltse fel a PDF-fájl használati feltételeit.
   1. **Nyelvként**válassza az **angol**nyelvet.
   1. Ahogy a felhasználók a használati **feltételek kibontását kérik**, válassza **a be**lehetőséget.
   1. A **feltételes hozzáférési házirend-sablonokkal való betartatáshoz**válassza az **egyéni házirend**elemet.
   1. Kattintson a **Létrehozás** elemre.

## <a name="create-your-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ez a szakasz bemutatja, hogyan hozhatja létre a kötelező feltételes hozzáférési szabályzatot. Az ebben a rövid útmutatóban szereplő forgatókönyv a következőket használja:

- A használati feltételek elfogadását igénylő felhőalapú alkalmazások helyőrzőként való Azure Portal. 
- A minta felhasználója a feltételes hozzáférési szabályzat teszteléséhez.  

A házirendben állítsa be a következőket:

| Beállítás | Érték |
| --- | --- |
| Felhasználók és csoportok | Isabella Simonsen |
| Felhőalkalmazások | Microsoft Azure-kezelés |
| Hozzáférés biztosítása | Saját felhasználási feltételek |

![Szabályzat létrehozása](./media/require-tou/1234.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Az **új** lap **név** szövegmezőbe írja be a következőt: az **Isabella kötelező felhasználási**helyének megadása.

   ![Név](./media/require-tou/71.png)

1. A **hozzárendelés** szakaszban kattintson a **felhasználók és csoportok**elemre.

   ![Felhasználók és csoportok](./media/require-tou/06.png)

1. A **felhasználók és csoportok** lapon:

   ![Felhasználók és csoportok](./media/require-tou/24.png)

   1. Kattintson a **felhasználók és csoportok kiválasztása**elemre, majd válassza a **felhasználók és csoportok**lehetőséget.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza az **Isabella Simonsen**elemet, majd kattintson a **kiválasztás**elemre.
   1. A **felhasználók és csoportok** lapon kattintson a **kész**gombra.
1. Kattintson a **Cloud apps**lehetőségre.

   ![Felhőalkalmazások](./media/require-tou/08.png)

1. A **Cloud apps** oldalon:

   ![Felhőalapú alkalmazások kiválasztása](./media/require-tou/26.png)

   1. Kattintson az **alkalmazások kiválasztása**elemre.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza a **Microsoft Azure felügyelet**lehetőséget, majd kattintson a **kiválasztás**gombra.
   1. A **Cloud apps** lapon kattintson a **kész**gombra.
1. A **hozzáférés-vezérlések** szakaszban kattintson a **Grant (Engedélyezés**) elemre.

   ![Hozzáférés-vezérlés](./media/require-tou/10.png)

1. A **támogatás** lapon:

   ![Engedély](./media/require-tou/111.png)

   1. Válassza a **Hozzáférés biztosítása** lehetőséget.
   1. Válassza **a saját felhasználási**feltételek lehetőséget.
   1. Kattintson a **Kiválasztás** elemre.
1. A **házirend engedélyezése** szakaszban kattintson **a be**gombra.

   ![Szabályzat engedélyezése](./media/require-tou/18.png)

1. Kattintson a **Létrehozás** elemre.

## <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, érdemes tudnia, hogy a várt módon működik-e. Első lépésként használja a feltételes hozzáférés, mi a teendő, ha a szabályzat eszközzel szimulálni kívánja a felhasználó bejelentkezési adatait. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

A **What if** Policy értékelési eszköz inicializálásához állítsa be a következőt:

- **Isabella Simonsen** felhasználóként
- **Microsoft Azure-kezelés** Felhőbeli alkalmazásként

A **What if** elemre kattintva létrehoz egy szimulációs jelentést, amely a következőket jeleníti meg:

- **Felhasználási feltételek megkövetelése az Isabella számára** az **alkalmazandó szabályzatok** alatt
- **A saját felhasználási** feltételek az **engedélyezési vezérlők**.

![Mi a teendő, ha a házirend eszköz](./media/require-tou/79.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. A [feltételes hozzáférés – házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson a **What if**elemre.  

   ![What If](./media/require-tou/14.png)

1. Kattintson a **felhasználók**elemre, jelölje ki az **Isabella Simonsen**, majd kattintson a **kiválasztás**elemre.

   ![Felhasználó](./media/require-tou/15.png)

1. Felhőalapú alkalmazás kiválasztása:

   ![Felhőalkalmazások](./media/require-tou/16.png)

   1. Kattintson a **Cloud apps**lehetőségre.
   1. A **Cloud apps lapon**kattintson az **alkalmazások kiválasztása**elemre.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza a **Microsoft Azure felügyelet**lehetőséget, majd kattintson a **kiválasztás**gombra.
   1. A Cloud apps lapon kattintson a **kész**gombra.
1. Kattintson **What if**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulta, hogyan értékelheti ki a szimulált bejelentkezést. A szimuláción kívül a feltételes hozzáférési szabályzatot is tesztelni kell, hogy az a várt módon működjön.

A szabályzat teszteléséhez próbáljon meg bejelentkezni a [Azure Portalba](https://portal.azure.com) az **Isabella Simonsen** -teszt fiók használatával. Meg kell jelennie egy párbeszédpanelnek, amely megköveteli a használati feltételek elfogadását.

![Használati feltételek](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a teszt felhasználót és a feltételes hozzáférési szabályzatot:

- Ha nem tudja, hogyan törölhet egy Azure AD-felhasználót, tekintse meg [a felhasználók törlése az Azure ad-ból](../fundamentals/add-users-azure-active-directory.md#delete-a-user)című témakört.
- A szabályzat törléséhez válassza ki a szabályzatot, majd kattintson a gyorselérési eszköztár **Törlés** elemére.

    ![Többtényezős hitelesítés](./media/require-tou/33.png)

- A használati feltételek törléséhez jelölje ki azt, majd kattintson a **feltételek törlése** elemre a felül található eszköztárban.

    ![Többtényezős hitelesítés](./media/require-tou/29.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [MFA megkövetelése adott alkalmazásokhoz](../authentication/tutorial-enable-azure-mfa.md)