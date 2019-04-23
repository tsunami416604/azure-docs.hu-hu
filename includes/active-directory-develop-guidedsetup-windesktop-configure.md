---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799546"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Az alkalmazás két módon regisztrálhatja.

### <a name="option-1-express-mode"></a>Option 1: Az Expressz mód

Az alkalmazás gyorsan regisztrálhatja az alábbiak szerint:
1. Nyissa meg a [az Azure portal - alkalmazás regisztrációja](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.

### <a name="option-2-advanced-mode"></a>Option 2: Speciális mód

Az alkalmazása regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához tegye a következőket:
1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
1. Válassza ki **új regisztrációs**.
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `Win-App-calling-MsGraph`).
   - A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
1. Az **Átirányítási URI-k** szakaszban keresse meg a **Javasolt átirányítási URI-k nyilvános ügyfelek számára (mobil, asztali)** szakaszt, és válassza az **urn: ietf:wg:oauth:2.0:oob** címet.
1. Kattintson a **Mentés** gombra.
1. Lépjen a Visual Studióban nyissa meg a *App.xaml.cs* fájlt, és cserélje `Enter_the_Application_Id_here` az imént regisztrált és másolt alkalmazás azonosítójával.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
