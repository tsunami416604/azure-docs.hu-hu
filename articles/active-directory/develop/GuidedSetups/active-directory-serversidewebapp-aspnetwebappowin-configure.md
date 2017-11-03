---
title: "Az Azure AD v2 ASP.NET webalkalmazás-kiszolgáló kezdeti lépések - Config |} Microsoft Docs"
description: "A Microsoft bejelentkezés végrehajtási egy ASP.NET-megoldás a hagyományos böngészőalapú webalkalmazás a szabványos OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 0c627802ccfba230dcde2dafffee26cb1c895791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>Hozzon létre egy alkalmazást (Express)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítást
4.  Kövesse az utasításokat az alkalmazás átirányítási URL-cím hozzáadása

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Az alkalmazás regisztrációs adatokat hozzáadni a megoldás (speciális)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-maileket 
3.  Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4.  Kattintson a `Add Platform`, majd jelölje be`Web`
5.  Lépjen vissza a Visual Studio és a Megoldáskezelőben, válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (Ha nem látja a Tulajdonságok ablak az F4)
6.  Módosítsa az SSL engedélyezve`True`
7.  Másolja az SSL URL-címet, és vegye fel az URL-cím az átirányítási URL-t átirányítási URL-t a regisztrációs portál közül:<br/><br/>![Projekt tulajdonságai](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
8.  Adja hozzá a következő `web.config` a legfelső szintű mappában található a szakasz `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Cserélje le `ClientId` az imént regisztrált alkalmazás azonosítójával
</li>
<li>
Cserélje le `redirectUri` a SSL URL-címet a projekt
</li>
</ol>
<!-- End Docs -->
