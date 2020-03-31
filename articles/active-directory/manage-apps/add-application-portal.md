---
title: Rövid útmutató – Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240491"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Rövid útmutató: Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz

Az Azure Active Directory (Azure AD) rendelkezik egy katalógussal, amely több ezer előre integrált alkalmazást tartalmaz. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, a következőket teheti:

- Az alkalmazáshoz való felhasználói hozzáférés kezelése feltételes hozzáférési szabályzattal.
- Felhasználók konfigurálása az alkalmazásba való egyszeri bejelentkezésre az Azure AD-fiókkal.

## <a name="before-you-begin"></a>Előkészületek

Ahhoz, hogy hozzáadhasson egy alkalmazást a bérlőhöz, a következőkre van szüksége:

- Azure AD-előfizetés
- Az egyszeri bejelentkezés használatára képes előfizetés az alkalmazáshoz

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

Javasoljuk, hogy az oktatóanyag lépéseinek teszteléséhez ne éles környezetet használjon. Ha nem rendelkezik nem éles Azure AD-környezettel, szerezze be az [egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Alkalmazás hozzáadása az Azure AD-bérlőhöz

Katalógusbeli alkalmazás hozzáadása az Azure AD-bérlőhöz:

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

2. Az **Azure Active Directory** ablaktáblában válassza a **Vállalati alkalmazások**lehetőséget. A **Minden alkalmazás** ablaktábla megnyílik, és az Azure AD-bérlőben lévő alkalmazások véletlenszerű mintáját jeleníti meg.

3. Ha katalógusalkalmazást szeretne hozzáadni a bérlőhöz, válassza az **Új alkalmazás**lehetőséget. 

    ![Válassza az Új alkalmazás lehetőséget, ha katalógusalkalmazást szeretne hozzáadni a bérlőhöz](media/add-application-portal/new-application.png)

 4. Váltás az új galéria előnézeti felület: A banner tetején a **Add a application oldal**, válassza ki a linket, hogy **kattintson ide, hogy próbálja ki az új és továbbfejlesztett app galéria**.

5. Megnyílik **az Azure AD-gyűjtemény (előzetes verzió)** ablaktábla, amely a felhőalapú platformok, a helyszíni alkalmazások és a kiemelt alkalmazások csempéit jeleníti meg. Vegye figyelembe, hogy a **Kiemelt alkalmazások** szakaszban felsorolt alkalmazások ikonok jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és a kiépítést.

    ![Alkalmazás keresése név vagy kategória szerint](media/add-application-portal/browse-gallery.png)

6. A gyűjteményben tallózhat a hozzáadni kívánt alkalmazásközött, vagy megkeresheti az alkalmazást a keresőmezőbe a nevének megadásával. Ezután válassza ki az alkalmazást az eredmények közül. Az űrlapon módosíthatja az alkalmazás nevét, hogy megfeleljen a szervezet igényeinek. Ebben a példában megváltoztattuk a nevet **GitHub-tesztre.**

    ![Alkalmazás hozzáadása a gyűjteményből](media/add-application-portal/create-application.png)

7. Kattintson a **Létrehozás** gombra. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

Befejezte a jelentkezés hozzáadását. A következő szakaszokban megtudhatja, hogyan módosíthatja az alkalmazás emblémáját és szerkesztheti az alkalmazás egyéb tulajdonságait.

## <a name="find-your-azure-ad-tenant-application"></a>Azure AD-bérlői alkalmazás megkeresése

Tegyük fel, hogy időközben valamiért el kellett mennie, és most folytatja az alkalmazás konfigurálását. Az első dolog, hogy megtalálja a kérelmet.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.
1. Az **Azure Active Directory** ablaktáblában válassza a **Vállalati alkalmazások**lehetőséget.
1. Az **Alkalmazás típusa** legördülő menüben válassza a **Minden alkalmazás**lehetőséget, majd válassza az **Alkalmaz parancsot.** A megtekintési lehetőségekről további információkat a [bérlői alkalmazások megtekintését](view-applications-portal.md) ismertető cikkben olvashat.
1. Most az Azure AD-bérlőn található összes alkalmazás listáját látja. A lista egy véletlenszerű minta. Ha több alkalmazást szeretne látni, jelölje be **a Több** egy vagy több alkalommal megjelenítése jelölőnégyzetet.
1. Ha gyorsan meg szeretne találni egy alkalmazást a bérlőben, írja be az alkalmazás nevét a keresőmezőbe, és válassza az **Alkalmaz lehetőséget.** Ez a példa megkeresi a korábban hozzáadott GitHub-tesztalkalmazást.

    ![Alkalmazás keresése a keresőmező használatával](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Felhasználó bejelentkezési tulajdonságainak konfigurálása

Most, hogy megtalálta az alkalmazást, megnyithatja, és konfigurálhatja az alkalmazás tulajdonságait.

Az alkalmazás tulajdonságainak szerkesztése:

1. Válassza ki a megnyitáshoz az alkalmazást.
2. A **Tulajdonságok** lehetőséget választva nyissa meg a tulajdonságok ablaktáblát szerkesztésre.

    ![A Tulajdonságok képernyő és a szerkeszthető alkalmazás tulajdonságainak megjelenítése](media/add-application-portal/edit-properties.png)

3. Szánjon egy kis időt a bejelentkezési beállítások megismerésére. A beállítások határozzák meg, hogy az alkalmazáshoz hozzárendelt vagy hozzá nem rendelt felhasználók hogyan jelentkezhetnek be az alkalmazásba. A beállítások azt is meghatározzák, hogy a felhasználó láthatja-e az alkalmazást a hozzáférési panelen.

    - **Engedélyezve van a felhasználók számára a bejelentkezéshez?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók bejelentkezhetnek-e.
    - **Felhasználó-hozzárendelés szükséges?** meghatározza, hogy az alkalmazáshoz nem rendelt felhasználók bejelentkezhetnek-e.
    - **Látható a felhasználók számára?** meghatározza, hogy az alkalmazáshoz rendelt felhasználók láthatják-e a hozzáférési panelen és az O365 indítópulton.

4. Az alábbi táblázatok segítségével kiválaszthatja az igényeinek leginkább megfelelő lehetőségeket.

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
       | Engedélyezve van a felhasználók számára a bejelentkezéshez? | Felhasználó-hozzárendelés szükséges? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
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
1. A bal oldali ablaktáblában válassza a **Tulajdonságok lehetőséget.**
1. Töltse fel az emblémát.
1. Ha végzett, válassza a **Mentés gombot.** 

    ![Az embléma módosítása az alkalmazás Tulajdonságok lapjáról](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > **A Tulajdonságok** ablaktáblán megjelenő miniatűr nem frissül azonnal. Bezárhatja, majd újra megnyithatja a tulajdonságokat a frissített ikon megtekintéséhez.

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válasszon ki egy bejelentkezési módszert,](what-is-single-sign-on.md#choosing-a-single-sign-on-method) amelyet használni szeretne, és olvassa el az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezéskonfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
