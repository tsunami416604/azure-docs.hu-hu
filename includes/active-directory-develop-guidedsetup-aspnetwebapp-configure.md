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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528530"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Regisztrálja az alkalmazást, és adja hozzá az alkalmazás regisztrációs adatok a megoldáshoz, két lehetősége van:

### <a name="option-1-express-mode"></a>Option 1: Az Expressz mód

Az alkalmazás gyorsan regisztrálhatja az alábbiak szerint:

1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ablaktáblán.
1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.

### <a name="option-2-advanced-mode"></a>Option 2: Speciális mód

Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio és:
   1. a Megoldáskezelőben válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (Ha nem látja a Tulajdonságok ablakban, az F4).
   1. Az SSL engedélyezve van módosítsa `True`.
   1. Kattintson a jobb gombbal a projektre a Visual Studióban, majd kattintson a **tulajdonságok**, és a **webes** fülre. Az a *kiszolgálók* szakaszban módosítsa a *projekt URL-címe* kell az SSL URL-címet.
   1. Másolja az SSL URL-címet. Az URL-cím átirányítási URL-címek listáját a következő lépésben a regisztrációs portál átirányítási URL-címek listájában fel fog venni:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
1. Válassza ki **új regisztrációs**.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `ASPNET-Tutorial`).
   1. A SSL volna a Visual Studióból az 1. lépésben másolt URL-címet (például `https://localhost:44368/`) a **válasz URL-cím**, és kattintson a **regisztrálása**.
1. Válassza a **Hitelesítés** menüt, állítsa be az **Azonosító jogkivonatok** értékét az **Implicit engedélyezés** területen, majd válassza a **Mentés** elemet.
1. Adja hozzá a következő a `web.config` a legfelső szintű mappában található a szakasz `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Cserélje le `ClientId` az imént regisztrált alkalmazás azonosítójával.
1. Cserélje le `redirectUri` a projekt SSL URL-címével.
