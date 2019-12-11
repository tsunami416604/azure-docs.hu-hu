---
title: Webes API-kat meghívó Webalkalmazás létrehozása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó webalkalmazást (áttekintés)
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
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962134"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webalkalmazás

Ismerje meg, hogyan hozhat létre olyan webalkalmazást, amely aláírja a felhasználókat a Microsoft Identity platformon, majd meghívja a webes API-kat a bejelentkezett felhasználó nevében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Ez a forgatókönyv azt feltételezi, hogy a következő forgatókönyvön esett át:

> [!div class="nextstepaction"]
> [Webes alkalmazás, amely bejelentkezik a felhasználók számára](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Áttekintés

A hitelesítést a webalkalmazáshoz adja hozzá, amely ezért bejelentkezhet a felhasználókba, és meghívja a webes API-t a bejelentkezett felhasználó nevében.

![Webes API-kat hívó webalkalmazás](./media/scenario-webapp/web-app.svg)

Webes API-kat meghívó Web Apps:

- bizalmas ügyfélalkalmazások.
- Ezért regisztráltak egy titkos kulcsot (az alkalmazás jelszava vagy tanúsítványa) az Azure AD-vel. Ezt a titkot a rendszer átadja az Azure AD-nek a jogkivonat beszerzése céljából való meghívása során

## <a name="specifics"></a>Sajátosságai

> [!NOTE]
> A bejelentkezés egy webalkalmazásba való felvétele nem a MSAL-kódtárakat használja, mivel ez a webalkalmazás védelmére szolgál. A kódtárak védelme a middleware nevű könyvtárak által érhető el. Ez volt az előző forgatókönyvnek a [webalkalmazásba való bejelentkezési felhasználójának](scenario-web-app-sign-user-overview.md) objektuma
>
> Amikor webes API-kat hív meg egy webalkalmazásból, hozzáférési jogkivonatokat kell kapnia ezekhez a webes API-khoz. A jogkivonatok megszerzéséhez MSAL-kódtárakat használhat.

Ebben a forgatókönyvben a fejlesztők teljes körű tapasztalata, ezért a következő konkrét szempontokkal rendelkezik:

- Az [alkalmazás regisztrálása](scenario-web-app-call-api-app-registration.md)során meg kell adnia egy vagy több (ha több helyen helyezi üzembe az alkalmazást), az Azure ad-vel meg kell osztania a válaszokat az URI-k, titkok vagy tanúsítványok számára.
- Az [alkalmazás konfigurációjának](scenario-web-app-call-api-app-configuration.md) az Azure ad-ben megosztott ügyfél-hitelesítő adatokat kell megadnia az alkalmazás regisztrálásakor

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-app-call-api-app-registration.md)
