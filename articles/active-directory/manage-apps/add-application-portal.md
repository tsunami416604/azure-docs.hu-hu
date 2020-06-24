---
title: Rövid útmutató – alkalmazás hozzáadása a Azure Active Directory-bérlőhöz
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: b89ab7e44bc71fa442f332cbba35af450f7d5155
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760660"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rövid útmutató: Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz

Az Azure Active Directory (Azure AD) rendelkezik egy katalógussal, amely több ezer előre integrált alkalmazást tartalmaz. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, a következőket teheti:

- Az alkalmazáshoz való felhasználói hozzáférés kezelése feltételes hozzáférési házirenddel.
- Felhasználók konfigurálása az alkalmazásba való egyszeri bejelentkezésre az Azure AD-fiókkal.

## <a name="before-you-begin"></a>Előkészületek

Ahhoz, hogy hozzáadhasson egy alkalmazást a bérlőhöz, a következőkre van szüksége:

- Azure AD-előfizetés
- Az egyszeri bejelentkezés használatára képes előfizetés az alkalmazáshoz

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

Javasoljuk, hogy az oktatóanyag lépéseinek teszteléséhez ne éles környezetet használjon. Ha nem rendelkezik nem éles Azure AD-környezettel, szerezze be az [egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Alkalmazás hozzáadása az Azure AD-bérlőhöz

Katalógusbeli alkalmazás hozzáadása az Azure AD-bérlőhöz:

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

2. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások**lehetőséget. Megnyílik a **minden alkalmazás** panel, és az Azure ad-bérlőben található alkalmazások véletlenszerű mintája jelenik meg.

3. Ha szeretne hozzáadni egy Gallery-alkalmazást a bérlőhöz, válassza az **új alkalmazás**lehetőséget. 

    ![Válassza az új alkalmazás lehetőséget, ha egy katalógus-alkalmazást szeretne hozzáadni a bérlőhöz](media/add-application-portal/new-application.png)

 4. Váltson az új katalógus előzetes verziójára: az **alkalmazás hozzáadása lap**tetején található szalagcímen válassza ki azt a hivatkozást, amely az **új és továbbfejlesztett alkalmazás-katalógus kipróbálásához kattintson ide**.

5. Megnyílik a **Tallózás Azure ad-katalógus (előzetes verzió)** panel, amelyen a Felhőbeli platformok, a helyszíni alkalmazások és a Kiemelt alkalmazások csempéi láthatók. Vegye figyelembe, hogy a **Kiemelt alkalmazások** szakaszban felsorolt alkalmazások rendelkeznek ikonokkal, amelyek azt jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és az üzembe helyezést.

    ![Alkalmazás keresése név vagy kategória szerint](media/add-application-portal/browse-gallery.png)

6. Böngészhet a katalógusban a hozzáadni kívánt alkalmazáshoz, vagy megkeresheti az alkalmazást úgy, hogy beírja a nevét a keresőmezőbe. Ezután válassza ki az alkalmazást az eredmények közül. Az űrlapon szerkesztheti az alkalmazás nevét, hogy megfeleljen a szervezet igényeinek. Ebben a példában a nevet a **GitHub-test**értékre módosították.

    ![Bemutatja, hogyan adhat hozzá egy alkalmazást a katalógusból](media/add-application-portal/create-application.png)

7. Kattintson a **Létrehozás** gombra. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

Befejezte az alkalmazás hozzáadását. A következő szakaszokban megtudhatja, hogyan módosíthatja az alkalmazás emblémáját és szerkesztheti az alkalmazás egyéb tulajdonságait.

## <a name="find-your-azure-ad-tenant-application"></a>Azure AD-bérlői alkalmazás megkeresése

Tegyük fel, hogy időközben valamiért el kellett mennie, és most folytatja az alkalmazás konfigurálását. Az első dolog, hogy megkeresse az alkalmazást.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.
1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások**lehetőséget.
1. Az **alkalmazás típusa** legördülő menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra. A megtekintési lehetőségekről további információkat a [bérlői alkalmazások megtekintését](view-applications-portal.md) ismertető cikkben olvashat.
1. Most az Azure AD-bérlőn található összes alkalmazás listáját látja. A lista egy véletlenszerű minta. További alkalmazások megjelenítéséhez válassza a **továbbiak megjelenítése** lehetőséget.
1. Ha gyorsan szeretne megkeresni egy alkalmazást a bérlőben, írja be az alkalmazás nevét a keresőmezőbe, és kattintson az **alkalmaz**gombra. Ez a példa megkeresi a korábban hozzáadott GitHub-test alkalmazást.

    ![Bemutatja, hogyan kereshet meg egy alkalmazást a keresőmező használatával](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

Most, hogy megtalálta az alkalmazást, megnyithatja és konfigurálhatja az alkalmazás tulajdonságait.

Az alkalmazás tulajdonságainak szerkesztése:

1. Válassza ki az alkalmazást a megnyitásához.
2. Válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához.

    ![A tulajdonságok képernyő és a szerkeszthető alkalmazás tulajdonságainak megjelenítése](media/add-application-portal/edit-properties.png)

3. Szánjon egy kis időt a bejelentkezési beállítások megismerésére. A beállítások határozzák meg, hogy az alkalmazásba való kiosztott vagy hozzá nem rendelt felhasználók hogyan jelentkezhetnek be az alkalmazásba. Emellett a beállítások határozzák meg, hogy a felhasználó láthatja-e az alkalmazást a hozzáférési panelen.

    - **Engedélyezve van a felhasználók számára a bejelentkezés?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz hozzárendelt felhasználók.
    - **Felhasználói hozzárendelés szükséges?** meghatározza, hogy be tud-e jelentkezni az alkalmazáshoz nem rendelt felhasználók.
    - **Látható a felhasználók számára?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók láthatják-e a hozzáférési panelen és a O365-Indítóban.

4. Az alábbi táblázatok segítségével kiválaszthatja az igényeinek legmegfelelőbb beállításokat.

   - Működés **hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Engedélyezett a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a hozzárendelt felhasználók? | Látható az alkalmazás a hozzárendelt felhasználók számára?* |
       | igen | igen | igen | igen | igen  |
       | igen | igen | nem  | igen | nem   |
       | igen | nem  | igen | igen | igen  |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

   - Működés **nem hozzárendelt** felhasználók esetében:

       | Alkalmazás tulajdonságainak beállításai | | | Felhasználói élmény – nem hozzárendelt felhasználók | |
       |---|---|---|---|---|
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
       | igen | igen | igen | nem  | nem   |
       | igen | igen | nem  | nem  | nem   |
       | igen | nem  | igen | igen | nem   |
       | igen | nem  | nem  | igen | nem   |
       | nem  | igen | igen | nem  | nem   |
       | nem  | igen | nem  | nem  | nem   |
       | nem  | nem  | igen | nem  | nem   |
       | nem  | nem  | nem  | nem  | nem   |

     *Látható az alkalmazás a felhasználó számára a hozzáférési panelen és az Office 365 alkalmazás indítójában?

## <a name="use-a-custom-logo"></a>Egyéni embléma használata

Egyéni embléma használata:

1. Hozzon létre egy emblémát, amelynek mérete 215 × 215 képpont, és mentse PNG formátumban.
1. Mivel már megtalálta az alkalmazást, válassza ki az alkalmazást.
1. A bal oldali ablaktáblán válassza a **Tulajdonságok**lehetőséget.
1. Töltse fel az emblémát.
1. Ha elkészült, válassza a **Mentés**lehetőséget. 

    ![Bemutatja, hogyan módosítható az embléma az alkalmazás Tulajdonságok lapján](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A **Tulajdonságok** ablaktáblán megjelenő miniatűr nem azonnal frissül. A frissített ikon megjelenítéséhez a tulajdonságok bezárásával és újbóli megnyitásával megjelenítheti a tulajdonságokat.

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válassza ki a használni kívánt egyszeri bejelentkezési módszert](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , és tekintse meg az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezésének konfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
