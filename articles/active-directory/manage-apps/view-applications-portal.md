---
title: Rövid útmutató – Bérlői alkalmazások megtekintése az Azure Active Directory használatával
description: Ebben a rövid útmutatóban az Azure Portal on tekintse meg az alkalmazásokat az Azure Active Directory (Azure AD) bérlőben.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d40e968bb7079d50e3fa18889ae996c9b59c90f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74421815"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Rövid útmutató: Az Azure Active Directory-bérlői alkalmazások megtekintése

Ebben a rövid útmutatóban az Azure Portal használatával tekintheti meg az Azure Active Directory- (Azure AD-) bérlő alkalmazásait.

## <a name="before-you-begin"></a>Előkészületek

Az eredmények megtekintéséhez legalább egy alkalmazásnak lennie kell az Azure AD-bérlőn. Az alkalmazás hozzáadásának lépéseit lásd az [Alkalmazás hozzáadása](add-application-portal.md) rövid útmutatóban.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

## <a name="find-the-list-of-tenant-applications"></a>A bérlői alkalmazások listájának megkeresése

Az Azure AD-bérlői alkalmazások az Azure Portal **Vállalati alkalmazások** szakaszában tekinthetők meg.

A bérlői alkalmazások megkeresésének lépései:

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.
1. Az **Azure Active Directory** ablaktáblában válassza a **Vállalati alkalmazások**lehetőséget.
1. Az **Alkalmazás típusa** legördülő menüben válassza a **Minden alkalmazás**lehetőséget, és válassza az **Alkalmaz parancsot.** Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.
1. További alkalmazások megtekintéséhez válassza a Lista alján található **További betöltés** lehetőséget. A bérlői alkalmazások számától függően a listában való görgetés helyett hasznosabbnak bizonyulhat [az adott alkalmazás megkeresése](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Válassza ki a kívánt beállításokat.

1. Az alkalmazásokat **alkalmazástípus**, **alkalmazásállapot**és alkalmazás láthatósága szerint **tekintheti meg.**
1. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:

    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.

1. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.
1. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. A **Rejtett** beállítás a bérlőben lévő, de a felhasználók számára nem látható alkalmazásokat jelenít meg.
1. Miután kiválasztotta a kívánt beállításokat, válassza **az Alkalmaz**lehetőséget.

## <a name="search-for-a-tenant-application"></a>Bérlői alkalmazás keresése

Adott alkalmazás keresése:

1. Az **Alkalmazás típusa** menüben válassza a **Minden alkalmazás**lehetőséget, és válassza az **Alkalmaz parancsot.**
1. Adja meg a megkeresni kívánt alkalmazás nevét. Ha az alkalmazás hozzá lett adva az Azure AD-bérlőhöz, az megjelenik a keresési eredmények között. Ez a példa azt mutatja, hogy a GitHub nem lett hozzáadva a bérlői alkalmazásokhoz.

    ![Példa: egy alkalmazás nem lett hozzáadva a bérlőhöz](media/view-applications-portal/search-for-tenant-application.png)

1. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával. Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.

    ![Példa az összes olyan alkalmazásra, amely az Értékesítéssel kezdődik](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tekintheti meg az alkalmazásokat az Azure AD-bérlőben. Megtanulta, hogyan szűrheti az alkalmazások listáját alkalmazástípus, állapot és láthatóság szerint. Emellett azt is megtanulta, hogyan kereshet meg egy adott alkalmazást.

Most, hogy megtalálta a keresett alkalmazást, továbbra is [hozzáadhat további alkalmazásokat a bérlőhöz.](add-application-portal.md) Vagy kiválaszthatja a tulajdonságok és konfigurációs beállítások megtekintéséhez vagy szerkesztéséhez az alkalmazást. Konfigurálhat például egyszeri bejelentkezést.

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
