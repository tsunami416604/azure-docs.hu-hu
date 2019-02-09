---
title: Bérlői alkalmazások megtekintése – Azure Active Directory | Microsoft Docs
description: Az Azure Portal használatával megtekintheti az Azure Active Directory- (Azure AD-) bérlő alkalmazásait.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: celested
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: 5783e382d0239cb8d256c3f8915769ebf1cf9122
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958781"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Azure Active Directory-bérlői alkalmazások megtekintése

Ebben a rövid útmutatóban az Azure Portal használatával tekintheti meg az Azure Active Directory- (Azure AD-) bérlő alkalmazásait.

## <a name="before-you-begin"></a>Előkészületek

Az eredmények megtekintéséhez legalább egy alkalmazásnak lennie kell az Azure AD-bérlőn. Az alkalmazás hozzáadásának lépéseit lásd az [Alkalmazás hozzáadása](add-application-portal.md) rövid útmutatóban.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

## <a name="find-the-list-of-tenant-applications"></a>A bérlői alkalmazások listájának megkeresése

Az Azure AD-bérlői alkalmazások az Azure Portal **Vállalati alkalmazások** szakaszában tekinthetők meg.

A bérlői alkalmazások megkeresésének lépései:

1. Az **[Azure Portal](https://portal.azure.com)** bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre. 

2. Az Azure Active Directory panelen kattintson a **Vállalati alkalmazások** elemre. 

3. Az **Alkalmazás típusa** legördülő listából válassza a **Minden alkalmazás** lehetőséget, majd kattintson az **Alkalmaz** gombra. Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.

    ![Vállalati alkalmazások](media/view-applications-portal/open-enterprise-apps.png)
   
4. További alkalmazások megtekintéséhez kattintson a **Továbbiak megjelenítése** gombra a lista alján. A bérlői alkalmazások számától függően a listában való görgetés helyett hasznosabbnak bizonyulhat [az adott alkalmazás megkeresése](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Ebben a szakaszban kiválaszthatja a keresési beállításokat.

1. Az alkalmazásokat megtekintheti az **Alkalmazás típusa**, az **Alkalmazás állapota** és az **Alkalmazás láthatósága** alapján. 

    ![Keresési beállítások](media/view-applications-portal/search-options.png)

2. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:

    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.

3. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.

4. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. A **Rejtett** beállítással olyan alkalmazásokat jeleníthet meg, amelyek megtalálhatók a bérlőn, de a felhasználók számára nem láthatók.

5. A kívánt beállítások megadását követően kattintson az **Alkalmaz** gombra.
 

## <a name="search-for-a-tenant-application"></a>Bérlői alkalmazás keresése

Egy adott alkalmazást az alábbi lépésekkel kereshet meg:

1. Az **Alkalmazás típusa** menüben válassza a **Minden alkalmazás** lehetőséget, majd kattintson az **Alkalmaz** gombra.

2. Adja meg a megkeresni kívánt alkalmazás nevét. Ha az alkalmazás hozzá lett adva az Azure AD-bérlőhöz, az megjelenik a keresési eredmények között. Ebben a példában az látható, hogy a GitHub nem lett hozzáadva a bérlői alkalmazásokhoz.

    ![Alkalmazás keresése](media/view-applications-portal/search-for-tenant-application.png)

3. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával.  Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.

    ![Keresés előtaggal](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megismerte, hogyan tekintheti meg az Azure AD-bérlőn lévő alkalmazásokat, illetve hogyan szűrheti az alkalmazásokat típus, állapot és láthatóság szerint. Emellett azt is megtanulta, hogyan kereshet meg egy adott alkalmazást.

Most, hogy megtalálta a keresett alkalmazást, folytathatja a [további bérlői alkalmazások hozzáadását](add-application-portal.md) ismertető oktatóanyaggal, vagy az alkalmazásra kattintva megtekintheti vagy szerkesztheti a tulajdonságokat és a konfigurációs beállításokat. Konfigurálhat például egyszeri bejelentkezést. 

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](configure-single-sign-on-portal.md)


