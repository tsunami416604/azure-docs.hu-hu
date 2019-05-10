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
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199122"
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
   1. Az a **átirányítási URI-k** szakaszában az átirányítási URI-k listájában:
   1. Az a **típus** oszlop kiválasztása **(mobil és asztali) nyilvános ügyfél**.
   1. Adja meg `urn:ietf:wg:oauth:2.0:oob` a a **ÁTIRÁNYÍTÁSI URI-t** oszlop.
1. Kattintson a **Mentés** gombra.
1. Lépjen a Visual Studióban nyissa meg a *App.xaml.cs* fájlt, és cserélje `Enter_the_Application_Id_here` az imént regisztrált és másolt alkalmazás azonosítójával.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
