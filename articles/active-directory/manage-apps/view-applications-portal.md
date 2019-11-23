---
title: Quickstart - View tenant applications using Azure Active Directory
description: In this Quickstart, use the Azure portal to view the applications in your Azure Active Directory (Azure AD) tenant.
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
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421815"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Quickstart: View your Azure Active Directory tenant applications

Ebben a rövid útmutatóban az Azure Portal használatával tekintheti meg az Azure Active Directory- (Azure AD-) bérlő alkalmazásait.

## <a name="before-you-begin"></a>Előzetes teendők

Az eredmények megtekintéséhez legalább egy alkalmazásnak lennie kell az Azure AD-bérlőn. Az alkalmazás hozzáadásának lépéseit lásd az [Alkalmazás hozzáadása](add-application-portal.md) rövid útmutatóban.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

## <a name="find-the-list-of-tenant-applications"></a>A bérlői alkalmazások listájának megkeresése

Az Azure AD-bérlői alkalmazások az Azure Portal **Vállalati alkalmazások** szakaszában tekinthetők meg.

A bérlői alkalmazások megkeresésének lépései:

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and choose **Apply**. Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.
1. To view more applications, select **Load more** at the bottom of the list. A bérlői alkalmazások számától függően a listában való görgetés helyett hasznosabbnak bizonyulhat [az adott alkalmazás megkeresése](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Select options according to what you're looking for.

1. You can view the applications by **Application Type**, **Application Status**, and **Application visibility**.
1. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:

    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.

1. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.
1. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. The **Hidden** option shows applications that are in the tenant, but aren't visible to users.
1. After choosing the options you want, select **Apply**.

## <a name="search-for-a-tenant-application"></a>Bérlői alkalmazás keresése

To search for a particular application:

1. In the **Application Type** menu, select **All applications**, and choose **Apply**.
1. Adja meg a megkeresni kívánt alkalmazás nevét. If the application has been added to your Azure AD tenant, it appears in the search results. This example shows that GitHub hasn't been added to the tenant applications.

    ![Example shows an app hasn't been added to the tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával. Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.

    ![Example shows all apps that start with Sales](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Következő lépések

In this quickstart, you learned how to view the applications in your Azure AD tenant. You learned how to filter the list of applications by application type, status, and visibility. Emellett azt is megtanulta, hogyan kereshet meg egy adott alkalmazást.

Now that you've found the application you were looking for, you can continue to [Add more applications to your tenant](add-application-portal.md). Or, you can select the application to view or edit properties and configuration options. Konfigurálhat például egyszeri bejelentkezést.

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](configure-single-sign-on-non-gallery-applications.md)
