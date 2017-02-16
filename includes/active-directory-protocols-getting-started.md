---
title: "Az Azure AD .NET protokolljának áttekintése | Microsoft Docs"
description: "A HTTP-üzenetek használata a bérlőben található webalkalmazásokhoz és webes API-khoz való hozzáférés engedélyezéséhez az Azure AD-vel."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 8db20ca36190197abcb73200eeed79e004435af9
ms.openlocfilehash: d851dec517c162073ade6ffb4ebcf972c5054698


---
<!--TODO: Introduction -->

## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálnia kell az alkalmazást az Azure Active Directory- (Azure AD-) bérlőben. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

* Jelentkezzen be az [Azure portálra](https://portal.azure.com).
* Az oldal jobb felső részén kattintson a fiókra, és válassza ki az Azure AD-bérlőt.
* A bal oldali navigációs ablakban kattintson az **Azure Active Directory** elemre.
* Kattintson az **Alkalmazásregisztrációk**, majd a **Hozzáadás** lehetőségre.
* Kövesse az utasításokat az új alkalmazás létrehozásához. Ebben az oktatóanyagban nincs jelentősége, hogy ez egy webalkalmazás vagy egy natív alkalmazás-e, de ha konkrét példákat szeretne látni a webalkalmazásokra vagy a natív alkalmazásokra, tekintse meg a [gyorsindítókat](../articles/active-directory/active-directory-developers-guide.md).
  * Webalkalmazások esetében adja meg a **Bejelentkezési URL-címet**, vagyis az alkalmazás alap URL-címét, amelyen a felhasználók bejelentkezhetnek (például: `http://localhost:12345`).
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Natív alkalmazások esetében adja meg az **Átirányítási URI-t**, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg egy, az alkalmazáshoz tartozó értéket, például: `http://MyFirstAADApp`
* A regisztráció befejeztével az Azure AD egy egyedi ügyfél-azonosítót (az alkalmazásazonosítót) rendel hozzá az alkalmazáshoz. Erre az értékre szükség lesz a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.



<!--HONumber=Dec16_HO4-->


