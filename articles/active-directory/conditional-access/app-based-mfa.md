---
title: Rövid útmutató – többtényezős hitelesítés (MFA) szükséges konkrét alkalmazások esetén az Azure Active Directory feltételes hozzáférés |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan köthet a hitelesítési követelményeknek, az Azure Active Directory (Azure AD) feltételes hozzáférés használatával elért felhőalapú alkalmazás típusát.
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
ms.openlocfilehash: 36cb3b1555a339249528e290e376454dd78f1e53
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509058"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Gyors útmutató: Többtényezős hitelesítés konkrét alkalmazások esetén az Azure Active Directory feltételes hozzáférés

A bejelentkezési élmény a felhasználók egyszerűbben érdemes, hogy jelentkezzen be a felhőalapú alkalmazások, a felhasználónév és jelszó használatával. Azonban sok környezet rendelkezik, amelynek tanácsos igényelnek, például a többtényezős hitelesítés (MFA) a Fiókellenőrzés erősebb egyfajta legalább néhány alkalmazással. Ez a szabályzat lehet igaz, a hozzáférés a szervezet e-mail system vagy a HR-alkalmazások. Az Azure Active Directoryban (Azure AD) a cél a feltételes hozzáférési szabályzattal együtt is elérheti.

Ez a rövid útmutató ismerteti, hogyan konfigurálható egy [Azure AD feltételes hozzáférési szabályzat](../active-directory-conditional-access-azure-portal.md) a multi-factor authentication szolgáltatás egy kijelölt felhőalkalmazás a környezetben, amely igényel.

![Példa feltételes hozzáférési szabályzatot az Azure Portalon](./media/app-based-mfa/32.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a forgatókönyv végrehajtásához szükséges:

- **Hozzáférés az Azure AD Premium Edition** – Azure AD feltételes hozzáférése egy Azure AD Premium-funkció.
- **Tesztfiók nevű Isabella Simonsen** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [adja hozzá a felhőalapú felhasználók](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Ebben a rövid útmutatóban a forgatókönyvhöz szükséges, hogy felhasználónkénti MFA nincs engedélyezve a tesztfiók. További információkért lásd: [igénylése a kétlépéses ellenőrzés egy felhasználó](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>A felhasználói élmény tesztelése

Ebben a lépésben az a célja, hogy egy benyomást kell szereznie egy feltételes hozzáférési szabályzat nélkül a felhasználói élményt.

**A környezet inicializálása:**

1. Jelentkezzen be az Azure portal Isabella Simonsen.
1. Jelentkezzen ki.

## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre feltételes hozzáférési szabályzatot. Ebben a rövid útmutatóban a forgatókönyvet használja:

- Az Azure Portalon, helyőrző egy felhőalapú alkalmazás, amely a többtényezős Hitelesítést követel meg. 
- A Mintafelhasználó a feltételes hozzáférési házirend tesztelése.  

Állítsa be a szabályzat:

| Beállítás | Érték |
| --- | --- |
| Felhasználók és csoportok | Isabella Simonsen |
| Felhőalkalmazások | A Microsoft Azure Management |
| Hozzáférés biztosítása | Többtényezős hitelesítés megkövetelése |

![Kiterjesztett feltételes hozzáférési szabályzat](./media/app-based-mfa/31.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférési rendszergazdájaként.
1. Az Azure Portalon, a bal oldali navigációs sávon kattintson **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Az a **Azure Active Directory** lap a **biztonsági** területén kattintson **feltételes hozzáférési**.

   ![Feltételes hozzáférés](./media/app-based-mfa/03.png)

1. Az a **feltételes hozzáférési** oldalon, a felső eszköztáron kattintson **új szabályzat**.

   ![Hozzáadás](./media/app-based-mfa/04.png)

1. Az a **új** lap a **neve** szövegmezőbe írja be **többtényezős hitelesítés megkövetelése az Azure portál elérésére szolgáló**.

   ![Name (Név)](./media/app-based-mfa/05.png)

1. Az a **hozzárendelés** területén kattintson **felhasználók és csoportok**.

   ![Felhasználók és csoportok](./media/app-based-mfa/06.png)

1. Az a **felhasználók és csoportok** lapon, a következő lépésekkel:

   ![Felhasználók és csoportok](./media/app-based-mfa/24.png)

   1. Kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki **felhasználók és csoportok**.
   1. Kattintson a **Kiválasztás** gombra.
   1. Az a **válassza** lapra, jelölje be **Isabella Simonsen**, és kattintson a **kiválasztása**.
   1. Az a **felhasználók és csoportok** kattintson **kész**.

1. Kattintson a **Felhőalkalmazások**.

   ![Felhőalkalmazások](./media/app-based-mfa/08.png)

1. Az a **Felhőalkalmazások** lapon, a következő lépésekkel:

   ![Válassza ki a felhőalapú alkalmazások](./media/app-based-mfa/26.png)

   1. Kattintson a **alkalmazások kiválasztása**.
   1. Kattintson a **Kiválasztás** gombra.
   1. A a **válassza** lapra, jelölje be **a Microsoft Azure Management**, és kattintson a **válassza**.
   1. Az a **Felhőalkalmazások** kattintson **kész**.

1. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

   ![Hozzáférés-vezérlés](./media/app-based-mfa/10.png)

1. Az a **Grant** lapon, a következő lépésekkel:

   ![Támogatás](./media/app-based-mfa/11.png)

   1. Válassza ki **hozzáférést**.
   1. Válassza ki **többtényezős hitelesítés megkövetelése**.
   1. Kattintson a **Kiválasztás** gombra.

1. Az a **házirend engedélyezése** területén kattintson **a**.

   ![Szabályzat engedélyezése](./media/app-based-mfa/18.png)

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="evaluate-a-simulated-sign-in"></a>Egy szimulált bejelentkezési kiértékelése

Most, hogy a feltételes hozzáférési szabályzat van beállítva, érdemes tudni, hogy a várt módon működik-e azt. Első lépésként használja a feltételes hozzáférés, mi történik, ha a házirend eszköz szimulálása tesztelési felhasználói bejelentkezési. A szimuláció becslése a hatása a bejelentkezéskor a szabályzatok rendelkezik, és szimuláció jelentést hoz létre.  

Inicializálása a **mi történik, ha** házirend kiértékelési eszközével, állítsa be:

- **Isabella Simonsen** felhasználóként
- **A Microsoft Azure Management** felhőalapú alkalmazásként

Kattintson a **mi történik, ha** tartalmazó szimuláció jelentést hoz létre:

- **Többtényezős hitelesítés az Azure portál elérésére szolgáló** alatt **érvényes szabályzatok**
- **Többtényezős hitelesítés megkövetelése** , **engedély**.

![Mi történik, ha a házirend-eszköz](./media/app-based-mfa/23.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  

   ![mi van, ha](./media/app-based-mfa/14.png)

1. Kattintson a **felhasználók**válassza **Isabella Simonsen**, és kattintson a **kiválasztása**.

   ![Felhasználó](./media/app-based-mfa/15.png)

1. Válassza ki a cloud app, hajtsa végre az alábbi lépéseket:

   ![Felhőalkalmazások](./media/app-based-mfa/16.png)

   1. Kattintson a **Felhőalkalmazások**.
   1. Az a **felhőalapú alkalmazások lapjának**, kattintson a **alkalmazások kiválasztása**.
   1. Kattintson a **Kiválasztás** gombra.
   1. A a **válassza** lapra, jelölje be **a Microsoft Azure Management**, és kattintson a **válassza**.
   1. A felhőalapú alkalmazások lapján kattintson a **kész**.

1. Kattintson a **mi történik, ha**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési házirend tesztelése

Az előző szakaszban megtanulhatta, hogyan értékelheti ki egy szimulált bejelentkezési. A szimuláció mellett is érdemes tesztelnie a feltételes hozzáférési szabályzat, győződjön meg arról, hogy az elvárt módon működik.

A szabályzat teszteléséhez próbáljon meg bejelentkezni a [az Azure portal](https://portal.azure.com) használatával a **Isabella Simonsen** fiók teszteléséhez. Megjelenik egy párbeszédpanel, a további biztonsági ellenőrzéshez a fiók beállítása szükséges.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználó számára, és a feltételes hozzáférési szabályzatot:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [felhasználók törlése az Azure ad-ből](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Törli a szabályzatot, válassza ki a szabályzatot, és kattintson **törlése** a gyorselérési eszköztáron.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati feltételek elfogadása szükséges](require-tou.md)
> [letiltja a hozzáférést, a munkamenet kockázata észlelésekor](app-sign-in-risk.md)
