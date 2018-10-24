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
ms.openlocfilehash: 44db736d5312b6850bb4fd8f47af5cd6b22535a7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960243"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálnia kell az alkalmazás az Azure Active Directory (Azure AD-) bérlőben. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
* Válassza ki az Azure AD-bérlő a fiókra a lapon, majd kattintson a jobb felső sarkában kattintson a **címtár váltása** navigációs és válassza ki a megfelelő bérlő. 
  * Ezt a lépést kihagyhatja, ha fiókjában csak egy Azure AD-bérlővel rendelkezik, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.
* A bal oldali navigációs ablakban kattintson az **Azure Active Directory** elemre.
* Kattintson a **Alkalmazásregisztrációk** , majd kattintson a **új alkalmazásregisztráció**.
* Kövesse az utasításokat az új alkalmazás létrehozásához. Ebben az oktatóanyagban nincs jelentősége, hogy ez egy webalkalmazás vagy egy natív alkalmazás-e, de ha konkrét példákat szeretne látni a webalkalmazásokra vagy a natív alkalmazásokra, tekintse meg a [gyorsindítókat](../articles/active-directory/develop/v1-overview.md).
  * Webalkalmazások esetén adja meg a **bejelentkezési URL-**, ahol felhasználók bejelentkezhetnek az alap URL-címét az alkalmazáshoz, azaz `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * A natív alkalmazások adjon meg egy **átirányítási URI-t**, amelyet az Azure ad-ben jogkivonatválaszok visszaadására használ. Adjon meg egy, az alkalmazáshoz tartozó értéket, például: `http://MyFirstAADApp`
* Regisztráció befejezése után az Azure AD rendeli az alkalmazást egy egyedi ügyfél-azonosítót a **Alkalmazásazonosító**. Ez az érték szükséges a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.
* Keresse meg az alkalmazás az Azure Portalon, kattintson a **alkalmazásregisztrációk**, és kattintson a **minden alkalmazás megtekintése**.
