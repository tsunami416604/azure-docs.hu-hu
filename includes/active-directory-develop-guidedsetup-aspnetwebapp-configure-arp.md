---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f3048daa7d597d92041733cdf5c6f299cebc2f73
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097928"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Az alkalmazás regisztrációs adatokat az ASP.NET-webalkalmazás konfigurálása

Ebben a lépésben konfigurálása az SSL használatához a projekthez, és az SSL URL-cím használatával az alkalmazás regisztrációs adatok konfigurálása. Ezt követően az alkalmazás hozzáadása ", a megoldás a regisztrációs adatok *web.config*.

1. A Megoldáskezelőben, válassza ki a projektet, és tekintse meg a `Properties` (Ha nem látja a Tulajdonságok ablakban, az F4) ablak
2. Változás `SSL Enabled` , `True`
3. Másolja az értéket a `SSL URL` felett, és illessze be a a `Redirect URL` Ez az oldal tetején található mezőbe, majd kattintson a *frissítés*:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4. Adja hozzá a következő a `web.config` szakasz gyökérszintű a mappában található fájl `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
