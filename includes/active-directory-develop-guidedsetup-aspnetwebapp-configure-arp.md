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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5940195207f85d8011f61336c0318e456c2a8a4c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58214181"
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
