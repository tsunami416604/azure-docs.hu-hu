---
title: Az Azure Active Directoryval hibák diagnosztizálása kapcsolódó szolgáltatás
description: Az active directory kapcsolódó szolgáltatás nem kompatibilis hitelesítési típust észlelt
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
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Az Azure Active Directory szolgáltatással kapcsolódó hibák diagnosztizálása

Előző hitelesítési kód észlelése, közben az Azure Active Directory connect-kiszolgáló azt észlelte egy nem kompatibilis hitelesítési típus.

A projekt megfelelően észlelheti az előző hitelesítési kódot, a projekt kell kialakítani.  Ha ezt a hibát észlelt, és nem kell egy előző hitelesítési kódot a projektben, építse újra, és próbálkozzon újra.

## <a name="project-types"></a>Projekttípusok

A csatlakoztatott szolgáltatás ellenőrzi a projekt, akkor a megfelelő hitelesítési logikát is szúrjon be a projekt kidolgozása típusú. Ha minden tartományvezérlő abból származó `ApiController` a projektben a projekt WebAPI projekt számít. Ha csak azok a tartományvezérlők, amelyek a `MVC.Controller` a projektben a projekt egy MVC projekt számít. A csatlakoztatott szolgáltatás bármely más projekttípus nem támogatja.

## <a name="compatible-authentication-code"></a>Kompatibilis hitelesítési kód

A csatlakoztatott szolgáltatás is ellenőrzi a hitelesítési beállításokat, amelyek korábban konfigurált vagy kompatibilisek-e a szolgáltatás. Ha meg adva az összes beállítást, akkor tekinthető párhuzamosan eset, és a csatlakoztatott szolgáltatás megnyílik a beállítások megjelenítése.  Ha csak néhány beállítás találhatók, hibás esetnek minősül.

MVC projekt, a csatlakoztatott szolgáltatás ellenőrzi az alábbi beállításokat, amelyeket a szolgáltatás előző használatából bármelyikét:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Emellett a csatlakoztatott szolgáltatás ellenőrzi a Web API-projektet, a következő beállítások eredményeként a szolgáltatás előző használata:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nem kompatibilis hitelesítési kód

Végezetül a csatlakoztatott szolgáltatás megpróbálja észlelni a hitelesítési kód verzióit, amelyek a Visual Studio korábbi verziói vannak konfigurálva. Ha ezt a hibát kapott, az azt jelenti, a projekt nem kompatibilis hitelesítési típust tartalmaz. A csatlakoztatott szolgáltatás észleli a következő típusú hitelesítés a Visual Studio korábbi verzióiról:

* Windows-hitelesítés
* Egyéni felhasználói fiókok
* Munkahelyi és iskolai fiókok

MVC projekt észleli a Windows-hitelesítést, a csatlakoztatott keresi a `authentication` eleme a `web.config` fájlt.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Windows-hitelesítés azonosíthatók a Web API-projektet, a csatlakoztatott szolgáltatás megkeresi a `IISExpressWindowsAuthentication` a projekt elem `.csproj` fájlt:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Az egyes felhasználói fiókok hitelesítési észlelését, a csatlakoztatott szolgáltatás keresi a csomag elemet a `packages.config` fájlt.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Egy szervezeti fiók hitelesítése régi formája észlelését, a csatlakoztatott szolgáltatás keresi a következő elem`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

A hitelesítési típus módosításához távolítsa el a nem kompatibilis hitelesítés típusát, és próbálja meg újból hozzáadni a csatlakoztatott szolgáltatás.

További információkért lásd: [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).