---
title: Hogyan diagnosztizálhatja a hibákat az Azure Active Directory csatlakoztatott szolgáltatás
description: Az active directory csatlakoztatott szolgáltatás azt észlelte, nem kompatibilis hitelesítési típus
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: f4fc7d11516d2ebf5e091d519333b3cf1fd09c47
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495155"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Az Azure Active Directory csatlakoztatott szolgáltatás-hibák diagnosztizálása

Előző hitelesítési kód észlelése, miközben az Azure Active Directory connect-kiszolgáló azt észlelte, nem kompatibilis hitelesítési típust.

Előző hitelesítési kód megfelelően észleli a projektben, a projekt kell elkészíteni.  Ha ezt a hibát, és a projektben egy előző hitelesítési kód nem rendelkezik, építse újra, és próbálkozzon újra.

## <a name="project-types"></a>Projekttípus

A csatlakoztatott szolgáltatás ellenőrzi a projekt fejleszt, akkor is a megfelelő hitelesítési logikát behelyezése a projekt típusa. Ha minden tartományvezérlő származó `ApiController` a projektben a projekt számít a WebAPI-projekt. Ha csak a tartományvezérlőkkel, amelyek származtatást `MVC.Controller` a projektben a projekt számít az MVC-projektben. A csatlakoztatott szolgáltatás bármely más projekt típusa nem támogatja.

## <a name="compatible-authentication-code"></a>Kompatibilis a hitelesítési kód

A csatlakoztatott szolgáltatás hitelesítési beállítások, amelyek korábban konfigurált, vagy a szolgáltatással kompatibilis is keres. Ha minden beállítás megadva, akkor számít párhuzamosan esetet, és a csatlakoztatott szolgáltatás megnyílik a beállítások megjelenítéséhez.  Ha csak néhány beállítás megadva, akkor a hibás esetnek számít.

MVC-projektben a csatlakoztatott szolgáltatás ellenőrzi az alábbi beállításokat, amelyeket a szolgáltatás előző használatából bármelyikét:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Emellett a csatlakoztatott szolgáltatás ellenőrzi egy webes API-projektben a következő beállításokat, a szolgáltatás előző használatának eredményeként bármelyikét:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nem kompatibilis hitelesítési kód

Végül a csatlakoztatott szolgáltatás megkísérli hitelesítési kód megadására vannak konfigurálva, a Visual Studio korábbi verziói-verziók észlelése. Ha ez a hiba érkezett, az azt jelenti a projekt nem kompatibilis hitelesítési típust tartalmaz. A csatlakoztatott szolgáltatás észleli a következő típusú hitelesítés a Visual Studio korábbi verziói:

* Windows-hitelesítés
* Egyéni felhasználói fiókokhoz
* Szervezeti fiókok

MVC-projektben észleli a Windows-hitelesítés, a csatlakoztatott keres a `authentication` eleme a `web.config` fájlt.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Windows-hitelesítést a webes API projektet észleléséhez a csatlakoztatott szolgáltatás keres a `IISExpressWindowsAuthentication` elemet a projekt `.csproj` fájlt:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Az egyes felhasználói fiókok hitelesítési észleléséhez a csatlakoztatott szolgáltatás keres a csomag elem a `packages.config` fájlt.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

A szervezeti fiók hitelesítése egy régi formájában észleléséhez a csatlakoztatott szolgáltatás keres a következő elem`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

A hitelesítési típus módosításához távolítsa el a nem kompatibilis hitelesítési típust, és próbálja meg újból hozzáadni a csatlakoztatott szolgáltatás.

További információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md).