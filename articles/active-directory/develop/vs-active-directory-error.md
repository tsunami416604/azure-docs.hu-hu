---
title: Hibák diagnosztizálása az Azure AD csatlakoztatott szolgáltatással (Visual Studio)
description: Az active directoryhoz csatlakoztatott szolgáltatás nem kompatibilis hitelesítési típust észlelt
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: a6ec31f0d60c7f6e3737dc4042b05a6d8bf3dd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699971"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Hibák diagnosztizálása az Azure Active Directoryhoz csatlakoztatott szolgáltatással

A korábbi hitelesítési kód észlelése közben az Azure Active Directoryhoz csatlakoztatott szolgáltatás nem kompatibilis hitelesítési típust észlelt.

A projekt korábbi hitelesítési kódjának helyes észleléséhez a projektet újra kell építeni. Ha ez a hiba megjelenik, és nincs korábbi hitelesítési kód a projektben, építse újra, és próbálkozzon újra.

## <a name="project-types"></a>Projekttípusok

A csatlakoztatott szolgáltatás ellenőrzi a fejlesztő projekt típusát, így a megfelelő hitelesítési logikát juttathatja be a projektbe. Ha van olyan vezérlő, amely `ApiController` a projektből származik, a projekt WebAPI-projektnek minősül. Ha csak olyan vezérlők vannak, amelyek a projektből `MVC.Controller` származnak, a projekt MVC-projektnek minősül. A csatlakoztatott szolgáltatás nem támogat semmilyen más projekttípust.

## <a name="compatible-authentication-code"></a>Kompatibilis hitelesítési kód

A csatlakoztatott szolgáltatás a korábban konfigurált vagy a szolgáltatással kompatibilis hitelesítési beállításokat is ellenőrzi. Ha minden beállítás jelen van, akkor újrabelépő esetnek minősül, és a csatlakoztatott szolgáltatás megnyitja a beállításokat.  Ha csak néhány beállítás van jelen, akkor hibaesetnek minősül.

Az MVC-projektekben a csatlakoztatott szolgáltatás a következő beállítások valamelyikét ellenőrzi, amelyek a szolgáltatás korábbi használatából származnak:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Emellett a csatlakoztatott szolgáltatás ellenőrzi a következő beállítások at a webes API-projekt, amelyek a szolgáltatás korábbi használatából ered:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nem kompatibilis hitelesítési kód

Végül a csatlakoztatott szolgáltatás megpróbálja észlelni a Visual Studio korábbi verzióival konfigurált hitelesítési kód verzióit. Ha ez a hibaüzenet jelenik meg, az azt jelenti, hogy a projekt nem kompatibilis hitelesítési típust tartalmaz. A csatlakoztatott szolgáltatás a Visual Studio korábbi verzióiból származó alábbi típusú hitelesítéseket észleli:

* Windows-hitelesítés
* Egyéni felhasználói fiókok
* Szervezeti fiókok

Ha észlelni szeretné a Windows-hitelesítést `authentication` egy MVC-projektben, a csatlakoztatott megkeresi a fájlban lévő `web.config` elemet.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

A Windows-hitelesítés webes API-projektekben való észleléséhez a csatlakoztatott szolgáltatás megkeresi a `IISExpressWindowsAuthentication` projekt fájljának elemét: `.csproj`

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Az egyéni felhasználói fiókok hitelesítésének észleléséhez a `packages.config` csatlakoztatott szolgáltatás megkeresi a csomagelemet a fájlban.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

A szervezeti fiókhitelesítés egy régi formájának észleléséhez a`web.config`csatlakoztatott szolgáltatás a következő elemet keresi meg a következő helyen:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

A hitelesítéstípus módosításához távolítsa el a nem kompatibilis hitelesítési típust, és próbálja meg újra hozzáadni a csatlakoztatott szolgáltatást.

További információ: [Authentication Scenarios for Azure AD](authentication-scenarios.md).
