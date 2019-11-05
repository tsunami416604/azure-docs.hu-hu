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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522994"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálja az alkalmazást a Azure Active Directory (Azure AD) Bérlővel. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
   
1. Válassza ki az Azure AD-bérlőt a lap jobb felső sarkában lévő fiók kiválasztásával, majd kattintson a **Váltás a címtár** -navigációra lehetőségre, majd válassza ki a megfelelő bérlőt. 
   - Hagyja ki ezt a lépést, ha csak egy Azure AD-bérlője van a fiókjában, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.
   
1. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.
   
1. A **Azure Active Directory** bal oldali menüben válassza az **alkalmazás-regisztrációk**lehetőséget, majd válassza az **új regisztráció**lehetőséget.
   
1. Kövesse az utasításokat az új alkalmazás létrehozásához. Nem számít, hogy ez egy webalkalmazás vagy egy nyilvános ügyfél (mobil & asztali) alkalmazás ebben az oktatóanyagban, de ha konkrét példákat szeretne a webalkalmazások vagy a nyilvános ügyfélalkalmazások számára [, tekintse](../articles/active-directory/develop/v1-overview.md)meg a gyors útmutatókat.
   
   - A **név** az alkalmazás neve, amely a végfelhasználók számára ad leírást az alkalmazásról.
   - A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
   - Adja meg az **átirányítási URI**-t. Webalkalmazások esetében ez az alkalmazás alap URL-címe, ahol a felhasználók bejelentkezhetnek.  Például: `http://localhost:12345`. A nyilvános ügyfél (Mobile & Desktop) esetében az Azure AD a jogkivonat-válaszok visszaküldésére használja. Adja meg az alkalmazáshoz tartozó értéket.  Például: `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. A regisztráció befejezését követően az Azure AD egyedi ügyfél-azonosítót (az **alkalmazás azonosítóját**) rendeli hozzá az alkalmazáshoz. Ezt az értéket a következő részekben kell megadnia, ezért másolja azt az alkalmazás oldaláról.
   
1. Ha szeretné megkeresni az alkalmazást a Azure Portalban, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **összes alkalmazás megtekintése**lehetőséget.
