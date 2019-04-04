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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca57746d1b13d2020f78d3c527e7d0c7a444769
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918399"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Azure Active Directory-bérlői alkalmazások megtekintése

Ebben a rövid útmutatóban az Azure Portal használatával tekintheti meg az Azure Active Directory- (Azure AD-) bérlő alkalmazásait.

## <a name="before-you-begin"></a>Előkészületek

Az eredmények megtekintéséhez legalább egy alkalmazásnak lennie kell az Azure AD-bérlőn. Az alkalmazás hozzáadásának lépéseit lásd az [Alkalmazás hozzáadása](add-application-portal.md) rövid útmutatóban.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

## <a name="find-the-list-of-tenant-applications"></a>A bérlői alkalmazások listájának megkeresése

Az Azure AD-bérlői alkalmazások az Azure Portal **Vállalati alkalmazások** szakaszában tekinthetők meg.

A bérlői alkalmazások megkeresésének lépései:

1. Az a  **[az Azure portal](https://portal.azure.com)**, válassza a bal oldali navigációs panelen, **Azure Active Directory**. 

2. Az a **Azure Active Directory** ablaktáblán válassza előbb **vállalati alkalmazások**. 

3. A a **alkalmazástípus** legördülő menüjében válassza **minden alkalmazás**, és válassza a **alkalmaz**. Ekkor egy véletlenszerűen kiválasztott minta jelenik meg a bérlői alkalmazásokból.
   
4. További alkalmazások megtekintéséhez jelölje ki **Továbbiak betöltése** a lista alján. A bérlői alkalmazások számától függően a listában való görgetés helyett hasznosabbnak bizonyulhat [az adott alkalmazás megkeresése](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Megtekintési beállítások kiválasztása

Válassza ki a beállítások szerint, amit keres.

1. Megtekintheti, hogy az alkalmazások által **alkalmazástípus**, **Alkalmazásállapot**, és **alkalmazás láthatósága**. 

2. Az **Alkalmazás típusa** területen az alábbi beállítások egyikét választhatja ki:

    - **Vállalati alkalmazások** – a nem a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Microsoft-alkalmazások** – a Microsoft által fejlesztett alkalmazások megjelenítése.
    - **Minden alkalmazás** – a nem a Microsoft által fejlesztett alkalmazások és a Microsoft-alkalmazások együttes megjelenítése.

3. Az **Alkalmazás állapota** területen a **Bármely**, **Letiltva** vagy **Engedélyezve** lehetőségek közül választhat. A **Bármely** beállítás magában foglalja a letiltott és az engedélyezett alkalmazásokat is.

4. Az **Alkalmazás láthatósága** területen a **Bármely** vagy a **Rejtett** lehetőségek közül választhat. A **rejtett** beállítást választja, megjelenik az alkalmazásokat, amelyek a bérlőbe, de nem látható a felhasználók számára.

5. Miután kiválasztotta a kívánt beállításokat, válassza ki a **alkalmaz**.
 

## <a name="search-for-a-tenant-application"></a>Bérlői alkalmazás keresése

Egy adott alkalmazás keresése:

1. Az a **alkalmazástípus** menüjében válassza **minden alkalmazás**, és válassza a **alkalmaz**.

2. Adja meg a megkeresni kívánt alkalmazás nevét. Ha az alkalmazás az Azure AD-bérlő van adva, megjelenik a keresési eredmények között. Ez a példa bemutatja, hogy a GitHub még nem sikerült hozzáadni a bérlő alkalmazásokhoz.

    ![Alkalmazás keresése](media/view-applications-portal/search-for-tenant-application.png)

3. Próbálkozzon az alkalmazásnév első néhány betűjének beírásával. Ebben a példában az összes olyan alkalmazás látható, amelyek neve a **Sales** sztringgel kezdődik.

    ![Keresés előtaggal](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan az alkalmazások megtekintéséhez az Azure AD-bérlővel. Megtudhatta, hogyan az alkalmazások által alkalmazástípus, állapotát és látható-e a lista szűréséhez. Emellett azt is megtanulta, hogyan kereshet meg egy adott alkalmazást.

Most, hogy az alkalmazás talált, keresnek a továbbra is [adja hozzá a bérlő több alkalmazás](add-application-portal.md). Másik lehetőségként kiválaszthatja, hogy az alkalmazás megtekintéséhez vagy módosításához a tulajdonságok és a konfigurációs beállításokat. Konfigurálhat például egyszeri bejelentkezést. 

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](configure-single-sign-on-portal.md)


