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
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Az alkalmazás regisztrációs adatokat az ASP.NET webalkalmazás konfigurálása

Ebben a lépésben konfigurálása az SSL használatát a projekthez, és az SSL URL-cím segítségével konfigurálhatja az alkalmazás regisztrációs adatokat. Ezt követően az alkalmazás hozzáadása "a megoldás a adatainak *web.config*.

1.  A Megoldáskezelőben, válassza ki a projektet, és tekintse meg a `Properties` (Ha nem látja a Tulajdonságok ablak az F4) ablak
2.  Változás `SSL Enabled` számára`True`
3.  Másolja az értéket `SSL URL` fent és illessze be a `Redirect URL` Ez a lap tetején mezőben, majd kattintson az *frissítés*:<br/><br/>![Projekt tulajdonságai](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  Adja hozzá a következő `web.config` szakaszban legfelső mappában található fájl `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
