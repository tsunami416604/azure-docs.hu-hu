---
title: Az Azure AD .NET protokolljának áttekintése | Microsoft Docs
description: A HTTP-üzenetek használata a bérlőben található webalkalmazásokhoz és webes API-khoz való hozzáférés engedélyezéséhez az Azure AD-vel.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 72fb594265e69eb1dc16cb29ad4df6acb3a87720
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34663627"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálnia kell az alkalmazás és az Azure Active Directory (Azure AD) bérlő. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
* Az oldal jobb felső részén kattintson a fiókra, és válassza ki az Azure AD-bérlőt.
* A bal oldali navigációs ablakban kattintson az **Azure Active Directory** elemre.
* Kattintson a **App regisztrációk** , majd kattintson a **új alkalmazás regisztrációja**.
* Kövesse az utasításokat az új alkalmazás létrehozásához. Ebben az oktatóanyagban nincs jelentősége, hogy ez egy webalkalmazás vagy egy natív alkalmazás-e, de ha konkrét példákat szeretne látni a webalkalmazásokra vagy a natív alkalmazásokra, tekintse meg a [gyorsindítókat](../articles/active-directory/develop/active-directory-developers-guide.md).
  * A webes alkalmazásokhoz, adja meg a **bejelentkezési URL-cím**, az alap URL-CÍMÉT az alkalmazásához. Ez az adott felhasználó tud egyszerre bejelentkezni például `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * A natív alkalmazások adjon meg egy **átirányítási URI-**, amelyek alapján az Azure AD vissza a token válaszok. Adjon meg egy, az alkalmazáshoz tartozó értéket, például: `http://MyFirstAADApp`
* Miután végrehajtotta a regisztráció, az Azure AD rendeli az alkalmazás egy egyedi ügyfél-azonosítókra, a **Alkalmazásazonosító**. Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás oldalról.
* Az alkalmazás az Azure portálon kattintson **App regisztrációk**, és kattintson a **összes alkalmazás megtekintése**.