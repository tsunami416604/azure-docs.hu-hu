---
title: Hibák diagnosztizálása a Azure Active Directory csatlakoztatott szolgáltatással
description: Az Active Directory csatlakoztatott szolgáltatás inkompatibilis hitelesítési típust észlelt
services: active-directory
ms.service: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c54db2ab923a9de5f07f12e0b0202649ef76cb8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326100"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Hibák diagnosztizálása a Azure Active Directory csatlakoztatott szolgáltatással

A korábbi hitelesítési kód észlelése közben az Azure Active Directory-összekötő kiszolgáló inkompatibilis hitelesítési típust észlelt.

A projekt korábbi hitelesítési kódjának megfelelő észleléséhez a projektnek felépítve kell lennie.  Ha ezt a hibát látja, és nem rendelkezik korábbi hitelesítési kóddal a projektben, építse újra, majd próbálkozzon újra.

## <a name="project-types"></a>Projektek típusai

A csatlakoztatott szolgáltatás ellenőrzi, hogy milyen típusú projektet fejleszt, így a megfelelő hitelesítési logikát beillesztheti a projektbe. Ha van olyan vezérlő, amely a projektből `ApiController` származik, a projekt egy WebAPI projektnek tekintendő. Ha csak a projektből származtatott `MVC.Controller` vezérlők találhatók, akkor a projekt MVC-projektnek tekintendő. A csatlakoztatott szolgáltatás nem támogatja a más típusú projekteket.

## <a name="compatible-authentication-code"></a>Kompatibilis hitelesítési kód

A csatlakoztatott szolgáltatás a korábban konfigurált vagy a szolgáltatással kompatibilis hitelesítési beállításokat is ellenőrzi. Ha minden beállítás megtalálható, akkor a rendszer újbóli beosztású esetnek tekinti, és a csatlakoztatott szolgáltatás megnyitja a beállításokat.  Ha csak néhány beállítás van jelen, akkor ez egy hiba esetének minősül.

Egy MVC-projektben a csatlakoztatott szolgáltatás a következő beállítások bármelyikét ellenőrzi, ami a szolgáltatás korábbi használatából ered:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Emellett a csatlakoztatott szolgáltatás a következő beállítások bármelyikét ellenőrzi egy webes API-projektben, amely a szolgáltatás korábbi használatából ered:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Inkompatibilis hitelesítési kód

Végül a csatlakoztatott szolgáltatás megpróbálja észlelni a Visual Studio korábbi verzióival konfigurált hitelesítési kód verzióit. Ha ezt a hibát kapta, az azt jelenti, hogy a projekt inkompatibilis hitelesítési típust tartalmaz. A csatlakoztatott szolgáltatás a következő típusú hitelesítést észleli a Visual Studio korábbi verzióiból:

* Windows-hitelesítés
* Egyéni felhasználói fiókok
* Szervezeti fiókok

Az MVC-projektekben a Windows-hitelesítés észleléséhez a csatlakoztatott `authentication` elem a `web.config` fájlban található elemet keresi.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

A Windows-hitelesítés webes API-projektben való észleléséhez a csatlakoztatott szolgáltatás a `IISExpressWindowsAuthentication` `.csproj` projekt fájljában keresi az elemet:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Az egyes felhasználói fiókok hitelesítésének észleléséhez a csatlakoztatott szolgáltatás megkeresi a csomag elemét `packages.config` a fájlban.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

A szervezeti fiók hitelesítésének régi formáját a csatlakoztatott szolgáltatás a következő elemre`web.config`keresi:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

A hitelesítés típusának módosításához távolítsa el a nem kompatibilis hitelesítési típust, és próbálkozzon újra a csatlakoztatott szolgáltatás hozzáadásával.

További információ: [hitelesítési forgatókönyvek az Azure ad-hez](authentication-scenarios.md).
