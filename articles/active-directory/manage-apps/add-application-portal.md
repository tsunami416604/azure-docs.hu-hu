---
title: Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz | Microsoft Docs
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 04/09/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466660a1e064ef41eb330b36107dbdcb1d097498
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477305"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Gyors útmutató: Alkalmazás hozzáadása a Azure Active Directory-bérlőhöz

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
1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások**lehetőséget.
1. Megnyílik az **összes alkalmazás** ablaktábla, amely az Azure ad-bérlőben található alkalmazások véletlenszerű mintáját jeleníti meg. A **minden alkalmazás** ablaktábla tetején kattintson az **új alkalmazás** lehetőségre, és adjon hozzá egy Gallery-alkalmazást a bérlőhöz.

    ![Válassza az új alkalmazás lehetőséget, ha egy katalógus-alkalmazást szeretne hozzáadni a bérlőhöz](media/add-application-portal/new-application.png)

1. A **Kategóriák** ablaktáblán a **Kiemelt alkalmazások** területen látható ikonokat láthatja, amelyek a katalógusbeli alkalmazások véletlenszerű mintája. További alkalmazások megjelenítéséhez válassza a továbbiak **megjelenítése** lehetőséget, de a keresés nem ajánlott, mert több ezer alkalmazás található a gyűjteményben.

    ![Alkalmazás keresése név vagy kategória szerint](media/add-application-portal/categories.png)

1. Egy alkalmazás kereséséhez a **Hozzáadás**a katalógusból területen adja meg a hozzáadni kívánt alkalmazás nevét. Válassza ki az alkalmazást az eredmények közül, és válassza a **Hozzáadás**lehetőséget. A következő példa az **alkalmazás hozzáadása** űrlapot mutatja be, amely a GitHub.com keresése után jelenik meg.

    ![Bemutatja, hogyan adhat hozzá egy alkalmazást a katalógusból](media/add-application-portal/add-an-application.png)

1. Az alkalmazásspecifikus űrlapon módosíthatja a tulajdonságokat. Például szerkesztheti az alkalmazás nevét, hogy az megfeleljen a vállalat igényeinek. Ebben a példában a **GitHub-test** nevet használjuk.
1. Ha végzett a tulajdonságok módosításával, válassza a **Hozzáadás**lehetőséget.
1. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

Befejezte az alkalmazás hozzáadását. Nyugodtan tartson szünetet. A következő szakaszokban megtudhatja, hogyan módosíthatja az alkalmazás emblémáját és szerkesztheti az alkalmazás egyéb tulajdonságait.

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
1. Válassza a **Tulajdonságok** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához.

    ![A tulajdonságok képernyő és a szerkeszthető alkalmazás tulajdonságainak megjelenítése](media/add-application-portal/edit-properties.png)

1. Szánjon egy kis időt a bejelentkezési beállítások megismerésére. A beállítások határozzák meg, hogy az alkalmazásba való kiosztott vagy hozzá nem rendelt felhasználók hogyan jelentkezhetnek be az alkalmazásba. Emellett a beállítások határozzák meg, hogy a felhasználó láthatja-e az alkalmazást a hozzáférési panelen.

    - Az **Engedélyezett a felhasználók számára a bejelentkezés** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók bejelentkezhetnek-e.
    - A **felhasználó-hozzárendelés kötelezően** meghatározza, hogy az alkalmazáshoz nem rendelt felhasználók bejelentkezhetnek-e.
    - A **Felhasználók számára látható** meghatározza, hogy az alkalmazáshoz hozzárendelt felhasználók láthatják-e az alkalmazást a hozzáférési panelen és az O365 indítójában.

1. Az alábbi táblázatok segítségével kiválaszthatja az igényeinek legmegfelelőbb beállításokat.

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
       | Engedélyezve van a felhasználók számára a bejelentkezés? | Kell felhasználó-hozzárendelés? | Felhasználók számára látható? | Bejelentkezhetnek a nem hozzárendelt felhasználók? | Látható az alkalmazás a nem hozzárendelt felhasználók számára?* |
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

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az alkalmazást az Azure AD-szervezethez, [válassza ki a használni kívánt egyszeri bejelentkezési módszert](what-is-single-sign-on.md#choosing-a-single-sign-on-method) , és tekintse meg az alábbi megfelelő cikket:

- [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
- [Jelszó egyszeri bejelentkezésének konfigurálása](configure-password-single-sign-on-non-gallery-applications.md)
- [Csatolt bejelentkezés konfigurálása](configure-linked-sign-on.md)
