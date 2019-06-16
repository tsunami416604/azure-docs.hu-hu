---
title: Az Azure AD .NET protokolljának áttekintése | Microsoft Docs
description: A HTTP-üzenetek használata a bérlőben található webalkalmazásokhoz és webes API-khoz való hozzáférés engedélyezéséhez az Azure AD-vel.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121519"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálnia kell az alkalmazás az Azure Active Directory (Azure AD-) bérlőben. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
* Válassza ki az Azure AD-bérlő a fiókra a lapon, majd kattintson a jobb felső sarkában kattintson a **címtár váltása** navigációs és válassza ki a megfelelő bérlő. 
  * Ezt a lépést kihagyhatja, ha fiókjában csak egy Azure AD-bérlővel rendelkezik, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.
* A bal oldali navigációs ablakban kattintson az **Azure Active Directory** elemre.
* Kattintson a **Alkalmazásregisztrációk** , majd kattintson a **új regisztrációs**.
* Kövesse az utasításokat az új alkalmazás létrehozásához. Akkor nem számít, hogy a webalkalmazás vagy egy nyilvános (mobil és asztali) ügyfélalkalmazás ebben az oktatóanyagban, de ha konkrét példákat a webalkalmazásokra vagy nyilvános ügyfélalkalmazások, tekintse meg a [útmutatóink](../articles/active-directory/develop/v1-overview.md).
  * A **név** az alkalmazás neve, amely a végfelhasználók számára ad leírást az alkalmazásról.
  * A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
  * Adja meg a **átirányítási URI**. Webalkalmazások esetén ez az alap URL-címe az alkalmazás felhasználók hol jelentkezhetnek be.  Például: `http://localhost:12345`. Az Azure AD (mobil és asztali) nyilvános ügyfél használja jogkivonatválaszok visszaadására azt. Adjon meg egy adott értéket az alkalmazáshoz.  Például: `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Regisztráció befejezése után az Azure AD rendeli az alkalmazást egy egyedi ügyfél-azonosítót (az **Alkalmazásazonosító**). Ez az érték szükséges a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.
* Keresse meg az alkalmazás az Azure Portalon, kattintson a **alkalmazásregisztrációk**, és kattintson a **minden alkalmazás megtekintése**.
