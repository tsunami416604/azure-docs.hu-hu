---
title: Rövid útmutató – bérlői alkalmazások megtekintése Azure Active Directory használatával
description: Ebben a rövid útmutatóban a Azure Portal használatával tekintheti meg az Azure Active Directory (Azure AD) bérlőben lévő alkalmazásokat.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74421815"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Gyors útmutató: Azure Active Directory bérlői alkalmazások megtekintése

Ebben a rövid útmutatóban az Azure Portal használatával tekintheti meg az Azure Active Directory- (Azure AD-) bérlő alkalmazásait.

## <a name="before-you-begin"></a>Előkészületek

Az eredmények megtekintéséhez legalább egy alkalmazásnak lennie kell az Azure AD-bérlőn. Az alkalmazás hozzáadásának lépéseit lásd az [Alkalmazás hozzáadása](add-application-portal.md) rövid útmutatóban.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

## <a name="find-the-list-of-tenant-applications"></a>A bérlői alkalmazások listájának megkeresése

Az Azure AD-bérlői alkalmazások az Azure Portal **Vállalati alkalmazások** szakaszában tekinthetők meg.

A bérlői alkalmazások megkeresésének lépései:

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.
1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások**lehetőséget.
1. Az **alkalmazás típusa** legördülő menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra. Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.
1. További alkalmazások megtekintéséhez válassza a lista alján található **továbbiak betöltés** lehetőséget. A bérlői alkalmazások számától függően a listában való görgetés helyett hasznosabbnak bizonyulhat [az adott alkalmazás megkeresése](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Válassza ki a beállításokat a keresett elemek alapján.

1. Az alkalmazásokat az **alkalmazás típusa**, az **alkalmazás állapota**és az **alkalmazás láthatósága**alapján tekintheti meg.
1. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:

    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.

1. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.
1. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. A **rejtett** beállítás megjeleníti a bérlőben található alkalmazásokat, de a felhasználók számára nem láthatók.
1. A kívánt beállítások kiválasztása után válassza az **alkalmaz**lehetőséget.

## <a name="search-for-a-tenant-application"></a>Bérlői alkalmazás keresése

Egy adott alkalmazás megkeresése:

1. Az **alkalmazás típusa** menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra.
1. Adja meg a megkeresni kívánt alkalmazás nevét. Ha az alkalmazás hozzá lett adva az Azure AD-bérlőhöz, az megjelenik a keresési eredmények között. Ez a példa azt mutatja, hogy a GitHub még nem lett hozzáadva a bérlői alkalmazásokhoz.

    ![Példa egy olyan alkalmazást mutat be, amely még nem lett hozzáadva a bérlőhöz](media/view-applications-portal/search-for-tenant-application.png)

1. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával. Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.

    ![Példa az összes értékesítést elindító alkalmazásra](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tekintheti meg az alkalmazásokat az Azure AD-bérlőben. Megtanulta, hogyan szűrheti az alkalmazások listáját az alkalmazás típusa, állapota és láthatósága alapján. Emellett azt is megtanulta, hogyan kereshet meg egy adott alkalmazást.

Most, hogy megtalálta a keresett alkalmazást, továbbra is [hozzáadhat további alkalmazásokat a bérlőhöz](add-application-portal.md). Vagy kiválaszthatja az alkalmazást a tulajdonságok és a konfigurációs beállítások megtekintéséhez vagy szerkesztéséhez. Konfigurálhat például egyszeri bejelentkezést.

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
