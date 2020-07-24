---
title: Webes API-kat meghívó Webalkalmazás létrehozása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó webalkalmazást (áttekintés)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0c4bef25a0be12c0335a82345ec998274f1faa67
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058397"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webalkalmazás

Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely aláírja a felhasználókat a Microsoft Identity platformon, majd meghívja a webes API-kat a bejelentkezett felhasználó nevében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ez a forgatókönyv feltételezi, hogy már elvégezte a következő forgatókönyvet:

> [!div class="nextstepaction"]
> [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Áttekintés

A webalkalmazáshoz hozzá kell adnia a hitelesítést, így a felhasználók a bejelentkezett felhasználó nevében is bejelentkezhetnek a webes API-ra.

![Webes API-kat hívó webalkalmazás](./media/scenario-webapp/web-app.svg)

A webes API-kat meghívó webalkalmazások bizalmas ügyfélalkalmazások.
Ezért regisztrálják a titkos kódot (az alkalmazás jelszavát vagy tanúsítványát) Azure Active Directory (Azure AD). Ezt a titkot a rendszer átadja az Azure AD-nek a jogkivonat beszerzésére irányuló hívásakor.

## <a name="specifics"></a>Sajátosságai

> [!NOTE]
> Ha a bejelentkezést egy webalkalmazásba helyezi, a webalkalmazás védelmét is maga a szolgáltatás védi. Ez a védelem *middleware* -kódtárak, nem a Microsoft Authentication Library (MSAL) használatával érhető el. Az előző forgatókönyv, a [webalkalmazás, amely aláírja a felhasználókat](scenario-web-app-sign-user-overview.md), a tárgyat lefedi.
>
> Ez a forgatókönyv ismerteti, hogyan hívhat webes API-kat egy webalkalmazásból. Ehhez a webes API-khoz hozzáférési jogkivonatokat kell beszereznie. Ezeket a jogkivonatokat a MSAL-kódtárak használatával szerzi be.

Ehhez a forgatókönyvhöz a következő konkrét feladatok szükségesek:

- Az [alkalmazás regisztrálása](scenario-web-app-call-api-app-registration.md)során meg kell adnia egy válasz URI-t, titkos kulcsot vagy tanúsítványt az Azure ad-vel való megosztáshoz. Ha az alkalmazást több helyre is telepíti, minden helyhez meg kell adnia egy válasz URI-t.
- Az [alkalmazás-konfigurációnak](scenario-web-app-call-api-app-configuration.md) meg kell adnia az Azure ad-vel megosztott ügyfél-hitelesítő adatokat az alkalmazás regisztrálása során.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webalkalmazás: alkalmazás regisztrálása](scenario-web-app-call-api-app-registration.md)
