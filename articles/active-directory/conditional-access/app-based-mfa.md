---
title: Gyors útmutató – többtényezős hitelesítés (MFA) megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan lehet a hitelesítési követelményeket a Azure Active Directory (Azure AD) feltételes hozzáférés használatával összekapcsolt felhőalapú alkalmazás típusához kötni.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a23f8fc10e0e5a19be1f93cc6d6e5e8e301f86d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474040"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Rövid útmutató: többtényezős hitelesítés megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel

A felhasználók bejelentkezési élményének egyszerűsítése érdekében előfordulhat, hogy engedélyezni szeretné a felhasználóknak, hogy felhasználónevet és jelszót használjanak a felhőalapú alkalmazásokba való bejelentkezéshez. Számos környezetnek azonban legalább néhány alkalmazással kell rendelkeznie, amelyekhez javasolt a fiókok ellenőrzésének erősebb formája, például a többtényezős hitelesítés (MFA) megkövetelése. Ez a szabályzat igaz lehet a szervezete e-mail-rendszeréhez vagy a HR-alkalmazásaihoz való hozzáféréshez. Azure Active Directory (Azure AD) esetében ezt a célt egy feltételes hozzáférési szabályzattal hajthatja végre.

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy [Azure ad feltételes hozzáférési szabályzatot](../active-directory-conditional-access-azure-portal.md) , amely megköveteli a többtényezős hitelesítést egy kiválasztott felhőalapú alkalmazás számára a környezetben.

![Feltételes hozzáférési szabályzat – példa a Azure Portal](./media/app-based-mfa/32.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a rövid útmutatóban szereplő forgatókönyv végrehajtásához a következőkre lesz szüksége:

- **Hozzáférés egy prémium szintű Azure ad kiadáshoz** – az Azure ad feltételes hozzáférés egy prémium szintű Azure ad képesség.
- **Egy Isabella Simonsen nevű tesztüzenet** – ha nem tudja, hogyan hozhat létre egy teszt fiókot, tekintse meg a [felhőalapú felhasználók hozzáadása](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)című témakört.

Az ebben a rövid útmutatóban szereplő forgatókönyv megköveteli, hogy a felhasználónkénti MFA-azonosítók ne legyenek engedélyezve a tesztelési fiókjához. További információkért lásd: [kétlépéses ellenőrzés megkövetelése a felhasználók](../authentication/howto-mfa-userstates.md)számára.

## <a name="test-your-experience"></a>A felhasználói élmény tesztelése

Ennek a lépésnek a célja, hogy benyomást kapjon a feltételes hozzáférési szabályzat nélkül.

**A környezet inicializálása:**

1. Jelentkezzen be a Azure Portalba Isabella Simonsen.
1. Jelentkezzen ki.

## <a name="create-your-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ez a szakasz bemutatja, hogyan hozhatja létre a kötelező feltételes hozzáférési szabályzatot. Az ebben a rövid útmutatóban szereplő forgatókönyv a következőket használja:

- Az MFA-t igénylő felhőalapú alkalmazások helyőrzőként való Azure Portal. 
- A minta felhasználója a feltételes hozzáférési szabályzat teszteléséhez.  

A házirendben állítsa be a következőket:

| Beállítás | Érték |
| --- | --- |
| Felhasználók és csoportok | Isabella Simonsen |
| Felhőalapú alkalmazások | Microsoft Azure-kezelés |
| Hozzáférés biztosítása | Többtényezős hitelesítés megkövetelése |

![Kiterjesztett feltételes hozzáférési szabályzat](./media/app-based-mfa/31.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférési rendszergazdaként.
1. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. A **Azure Active Directory** lap **Biztonság** szakaszában kattintson a **feltételes hozzáférés**elemre.

   ![Feltételes hozzáférés](./media/app-based-mfa/03.png)

1. A **feltételes hozzáférés** lapon, a felső eszköztáron kattintson az **új házirend**elemre.

   ![Hozzáadás](./media/app-based-mfa/04.png)

1. Az **új** lap **név** szövegmezőbe írja be az **MFA megkövetelése Azure Portal hozzáféréshez**értéket.

   ![Name (Név)](./media/app-based-mfa/05.png)

1. A **hozzárendelés** szakaszban kattintson a **felhasználók és csoportok**elemre.

   ![Felhasználók és csoportok](./media/app-based-mfa/06.png)

1. A **felhasználók és csoportok** lapon hajtsa végre a következő lépéseket:

   ![Felhasználók és csoportok](./media/app-based-mfa/24.png)

   1. Kattintson a **felhasználók és csoportok kiválasztása**elemre, majd válassza a **felhasználók és csoportok**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **kiválasztás** lapon válassza az **Isabella Simonsen**elemet, majd kattintson a **kiválasztás**elemre.
   1. A **felhasználók és csoportok** lapon kattintson a **kész**gombra.

1. Kattintson a **Cloud apps**lehetőségre.

   ![Felhőalapú alkalmazások](./media/app-based-mfa/08.png)

1. A **Cloud apps** oldalon hajtsa végre a következő lépéseket:

   ![Felhőalapú alkalmazások kiválasztása](./media/app-based-mfa/26.png)

   1. Kattintson az **alkalmazások kiválasztása**elemre.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **kiválasztás** lapon válassza a **Microsoft Azure felügyelet**lehetőséget, majd kattintson a **kiválasztás**gombra.
   1. A **Cloud apps** lapon kattintson a **kész**gombra.

1. A **hozzáférés-vezérlések** szakaszban kattintson a **Grant (Engedélyezés**) elemre.

   ![Hozzáférés-vezérlés](./media/app-based-mfa/10.png)

1. A **támogatás** lapon hajtsa végre a következő lépéseket:

   ![Grant](./media/app-based-mfa/11.png)

   1. Válassza a **hozzáférés engedélyezése**lehetőséget.
   1. Válassza a **többtényezős hitelesítés megkövetelése**lehetőséget.
   1. Kattintson a **Kiválasztás** gombra.

1. A **házirend engedélyezése** szakaszban kattintson **a be**gombra.

   ![Házirend engedélyezése](./media/app-based-mfa/18.png)

1. Kattintson a **Létrehozás** elemre.

## <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, érdemes tudnia, hogy a várt módon működik-e. Első lépésként használja a feltételes hozzáférés, mi a teendő, ha a szabályzat eszközzel szimulálni kívánja a felhasználó bejelentkezési adatait. A szimuláció azt a hatást becsüli meg, hogy a bejelentkezés milyen hatással van a szabályzatokra, és létrehoz egy szimulációs jelentést.  

A **What if** Policy értékelési eszköz inicializálásához állítsa be a következőt:

- **Isabella Simonsen** felhasználóként
- **Microsoft Azure-kezelés** Felhőbeli alkalmazásként

A **What if** elemre kattintva létrehoz egy szimulációs jelentést, amely a következőket jeleníti meg:

- **MFA Megkövetelése Azure Portal-hozzáféréshez** az **alkalmazandó szabályzatok** alatt
- **Többtényezős hitelesítés megkövetelése** **engedélyezési vezérlőkként**.

![Mi a teendő, ha a házirend eszköz](./media/app-based-mfa/23.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. A [feltételes hozzáférés – házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson a **What if**elemre.  

   ![mi van, ha](./media/app-based-mfa/14.png)

1. Kattintson a **felhasználók**elemre, jelölje ki az **Isabella Simonsen**, majd kattintson a **kiválasztás**elemre.

   ![Felhasználó](./media/app-based-mfa/15.png)

1. A felhőalapú alkalmazások kiválasztásához hajtsa végre a következő lépéseket:

   ![Felhőalapú alkalmazások](./media/app-based-mfa/16.png)

   1. Kattintson a **Cloud apps**lehetőségre.
   1. A **Cloud apps lapon**kattintson az **alkalmazások kiválasztása**elemre.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **kiválasztás** lapon válassza a **Microsoft Azure felügyelet**lehetőséget, majd kattintson a **kiválasztás**gombra.
   1. A Cloud apps lapon kattintson a **kész**gombra.

1. Kattintson **What if**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulta, hogyan értékelheti ki a szimulált bejelentkezést. A szimuláción kívül a feltételes hozzáférési szabályzatot is tesztelni kell, hogy az a várt módon működjön.

A szabályzat teszteléséhez próbáljon meg bejelentkezni a [Azure Portalba](https://portal.azure.com) az **Isabella Simonsen** -teszt fiók használatával. Meg kell jelennie egy párbeszédpanelnek, amely megköveteli, hogy állítsa be a fiókját a további biztonsági ellenőrzéshez.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a teszt felhasználót és a feltételes hozzáférési szabályzatot:

- Ha nem tudja, hogyan törölhet egy Azure AD-felhasználót, tekintse meg [a felhasználók törlése az Azure ad-ból](../fundamentals/add-users-azure-active-directory.md#delete-a-user)című témakört.
- A szabályzat törléséhez válassza ki a szabályzatot, majd kattintson a gyorselérési eszköztár **Törlés** elemére.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> A használati [feltételek elfogadásának Megkövetelése](require-tou.md)
> a [hozzáférés letiltása a munkamenet-kockázat észlelésekor](app-sign-in-risk.md)
