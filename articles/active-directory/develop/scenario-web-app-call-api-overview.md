---
title: Webalkalmazás, hogy a hívások webes API-k (áttekintés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, hogy a hívások webes API-k (áttekintés)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076300"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Forgatókönyv: Webalkalmazás, hogy a hívások webes API-k

Bejelentkezés felhasználók webes alkalmazás létrehozása a Microsoft identity platform, és amely meghívja a webes API-kat a bejelentkezett felhasználó nevében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ebben a forgatókönyvben supposes, hogy az alábbi forgatókönyv forrásanyagot:

> [!div class="nextstepaction"]
> [Webes alkalmazás, hogy jelentkezik be felhasználókat](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Áttekintés

Hitelesítés hozzáadása a webalkalmazást, amely így bejelentkezhetnek a felhasználók, és meghív egy webes API-t a bejelentkezett felhasználó nevében.

![Webalkalmazás, hogy a hívások webes API-k](./media/scenario-webapp/web-app.svg)

Web Apps, amely meghívja a webes API-kat:

- Vannak bizalmas ügyfélalkalmazások számára.
- ezért azokat az Azure ad-vel regisztrálta egy titkos kulcsot (alkalmazás-jelszó vagy tanúsítvány). A titkos kód egy token beszerzése az Azure AD-hívása során átadott a rendszer.

## <a name="specifics"></a>Tulajdonságairól

> [!NOTE]
> Bejelentkezés hozzáadása egy webalkalmazás nem használja a az MSAL kódtárakat, mivel ez a webalkalmazás védelméről. Közbenső nevű kódtárak használatával érhető el kódtárak védelme. Ez volt az objektum az előző forgatókönyvben [webalkalmazás bejelentkezési felhasználók](scenario-web-app-sign-user-overview.md)
>
> Amikor webes API-k hívása egy webalkalmazásból, szüksége lesz a hozzáférési tokenekhez ezek webes API-k. Az MSAL kódtárak használatával ezek a jogkivonatok beszerzéséhez.

Ebben a forgatókönyvben a fejlesztők a teljes körű tapasztalata rendelkezik, ezért különleges szempontok szerint:

- Során a [alkalmazásregisztráció](scenario-web-app-call-api-app-registration.md), meg kell adnia egy, vagy több (ha az alkalmazás üzembe helyezése több helyre) válasz URI-k, titkos kulcsok, vagy tanúsítványok meg kell osztani az Azure ad-ben.
- A [Alkalmazáskonfiguráció](scenario-web-app-call-api-app-configuration.md) kell adnia a megosztáshoz ügyfél-hitelesítő adatok az Azure AD-alkalmazás regisztrálásakor

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-app-call-api-app-registration.md)
