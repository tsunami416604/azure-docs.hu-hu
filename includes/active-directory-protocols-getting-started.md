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
ms.openlocfilehash: 5a4f08718175dd7ef369d801807e6c8e1ff28fd9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39485598"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálnia kell az alkalmazás az Azure Active Directory (Azure AD-) bérlőben. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
* Válassza ki az Azure AD-bérlő a fiókra a lapon, majd kattintson a jobb felső sarkában kattintson a **címtár váltása** navigációs és válassza ki a megfelelő bérlő. 
  * Ezt a lépést kihagyhatja, csak egy Azure AD-bérlőt, ha a fiók alatt, vagy ha már kiválasztotta a megfelelő Azure AD-bérlővel.
* A bal oldali navigációs ablakban kattintson az **Azure Active Directory** elemre.
* Kattintson a **Alkalmazásregisztrációk** , majd kattintson a **új alkalmazásregisztráció**.
* Kövesse az utasításokat az új alkalmazás létrehozásához. Ebben az oktatóanyagban nincs jelentősége, hogy ez egy webalkalmazás vagy egy natív alkalmazás-e, de ha konkrét példákat szeretne látni a webalkalmazásokra vagy a natív alkalmazásokra, tekintse meg a [gyorsindítókat](../articles/active-directory/develop/azure-ad-developers-guide.md).
  * Webalkalmazások esetén adja meg a **bejelentkezési URL-**, ahol felhasználók bejelentkezhetnek az alap URL-címét az alkalmazáshoz, azaz `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * A natív alkalmazások adjon meg egy **átirányítási URI-t**, amelyet az Azure ad-ben jogkivonatválaszok visszaadására használ. Adjon meg egy, az alkalmazáshoz tartozó értéket, például: `http://MyFirstAADApp`
* Regisztráció befejezése után az Azure AD rendeli az alkalmazást egy egyedi ügyfél-azonosítót a **Alkalmazásazonosító**. Ez az érték szükséges a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.
* Keresse meg az alkalmazás az Azure Portalon, kattintson a **alkalmazásregisztrációk**, és kattintson a **minden alkalmazás megtekintése**.
