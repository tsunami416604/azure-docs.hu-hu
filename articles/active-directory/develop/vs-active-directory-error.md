---
title: "Az Azure Active Directory kapcsolat varázsló hibák diagnosztizálása"
description: "Az active directory kapcsolat varázsló nem kompatibilis hitelesítési típust észlelt"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 4f29f62b2996cae98b02c1ed5fcb59eca09301ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Az Azure Active Directory kapcsolat varázsló hibák diagnosztizálása
Történt észlelésekor előző hitelesítési kódot, a varázsló nem kompatibilis hitelesítési típust észlelt.   

## <a name="what-is-being-checked"></a>Mi ellenőrzés?
**Megjegyzés:** megfelelően azonosíthatók a projekt korábbi hitelesítési kódot, a projekt kell kialakítani.  Ha ezt a hibát észlelt, és nem kell egy előző hitelesítési kódot a projektben, építse újra, és próbálkozzon újra.

### <a name="project-types"></a>Projekttípusok
A varázsló ellenőrzi, így azt a megfelelő hitelesítési logikát is szúrjon be a projekt kidolgozása projekt típusától.  Ha minden tartományvezérlő abból származó `ApiController` a projektben a projekt WebAPI projekt számít.  Ha csak azok a tartományvezérlők, amelyek a `MVC.Controller` a projektben a projekt egy MVC projekt számít.  Bármi más a varázsló által nem támogatott.

### <a name="compatible-authentication-code"></a>Kompatibilis hitelesítési kód
A varázsló is ellenőrzi a hitelesítési beállításokat, amelyek a varázsló a korábban konfigurált vagy kompatibilisek-e a varázslót.  Ha minden beállítás, akkor tekinthető párhuzamosan eset, és megnyílik a varázsló megjeleníti a beállításokat.  Ha csak néhány beállítás találhatók, hibás esetnek minősül.

MVC projekt a varázsló ellenőrzi, az alábbi beállításokat, amelyeket a varázsló előző használatából bármelyikéhez:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Emellett a varázsló ellenőrzi a Web API-projektet, a következő beállítások a varázsló előző használatából fakadó:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Nem kompatibilis hitelesítési kód
Végezetül a varázsló megpróbálja észlelni a hitelesítési kód vannak konfigurálva a Visual Studio korábbi verziói-verziók. Ha ezt a hibát kapott, az azt jelenti, a projekt nem kompatibilis hitelesítési típust tartalmaz. A varázsló észleli a következő típusú hitelesítés a Visual Studio korábbi verzióiról:

* Windows-hitelesítés 
* Egyéni felhasználói fiókok 
* Munkahelyi és iskolai fiókok 

MVC projekt észleli a Windows-hitelesítést, a varázsló megkeresi a `authentication` elem a **web.config** fájlt.

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Windows-hitelesítés azonosíthatók a Web API-projektet, a varázsló megkeresi a `IISExpressWindowsAuthentication` a projekt elem **.csproj** fájlt:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Egyes felhasználói fiókok hitelesítési észlelését, a varázsló megkeresi a csomag elem a **Packages.config** fájlt.

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Egy szervezeti fiók hitelesítése régi formája észlelését, a varázsló megkeresi a következő elem **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

A hitelesítési típus módosításához távolítsa el a nem kompatibilis hitelesítés típusát, és futtassa újra a varázslót.

További információkért lásd: [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Következő lépések
- [Hitelesítési forgatókönyvek az Azure AD-hez](active-directory-authentication-scenarios.md)