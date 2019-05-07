---
title: Démon alkalmazások hívó webes API-k (áttekintés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy démon alkalmazást, amely meghívja a webes API-k
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075880"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Forgatókönyv: Démon alkalmazások, hogy a hívások webes API-k

Ismerje meg kell, amely meghívja a webes API-k démon-alkalmazás létrehozása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Áttekintés

Az alkalmazás a webes API-hívás maga nevében (nem felhasználó) nevében egy token vásárolhatja meg. Ebben a forgatókönyvben a démon alkalmazások esetén hasznos. A szabványos OAuth 2.0-s használ [ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) megadása.

![Démonalkalmazások](./media/scenario-daemon-app/daemon-app.svg)

Íme néhány példa a használati esetek démon alkalmazások esetében:

- Webalkalmazások, amelyek kiépítéséhez használt vagy a felhasználókat kezeljen, vagy a batch-címtárban folyamatok
- Asztali alkalmazások például (a windows szolgáltatások a Windows) vagy a Linuxos démonok folyamatokat, amelyek elvégzik kötegelt feladatok, illetve egy operációs rendszer szolgáltatás fut a háttérben
- Webes API-k szeretné címtárak esetén a nem meghatározott felhasználók kezelése

Van egy másik gyakori esetben, ha nem démon alkalmazások használnak, ügyfél-hitelesítő adatok: azok a felhasználók nevében, ha technikai okokból webes API-k vagy -azonosítót fog kérni az erőforrás eléréséhez szükséges. Ilyen például, a titkos kulcsokat a KeyVault vagy egy Azure SQL database Cache-gyorsítótárhoz a hozzáférést.

Az alkalmazások saját identitásuk szükséges jogkivonat beszerzése:

- Vannak bizalmas ügyfélalkalmazások számára. Ezeket az alkalmazásokat, hogy egy felhasználó függetlenül erőforrásokhoz férnek hozzá kell igazolnia az identitását. Azok is inkább bizalmas adatokat kezelő alkalmazásokhoz, amelyre szükségük van az Azure Active Directory (Azure AD) bérlő rendszergazdái által jóvá kell hagyni.
- Regisztrált az Azure ad-vel egy titkos kulcsot (alkalmazás-jelszó vagy tanúsítvány). A titkos kód átadva a jogkivonat beszerzéséhez az Azure AD a hívás során.

## <a name="specifics"></a>Tulajdonságairól

> [!IMPORTANT]
>
> - Felhasználói beavatkozás nem lehetséges egy démon alkalmazással. Egy démon alkalmazás használatához a saját identitását. Ez az alkalmazástípus egy hozzáférési jogkivonatot kér az alkalmazás azonosítóját és az Alkalmazásazonosítót, hitelesítő adat (jelszó vagy tanúsítvány) és alkalmazás bemutatása az Azure AD-Alkalmazásazonosító URI-ja. Sikeres hitelesítést követően a démon kap a Microsoft identity platform végpont, amely szolgál majd a webes API hívása (és szükség szerint frissülnek) hozzáférési jogkivonat (és a egy frissítési jogkivonat).
> - Felhasználói beavatkozás során ez nem lehetséges, mert a növekményes jóváhagyás nem lehetséges. Az összes szükséges API-engedélyek: alkalmazás regisztrációja konfigurálni kell, és a kódot az alkalmazás csak a statikusan meghatározott engedélyt kér. Ez azt is jelenti, hogy démon alkalmazások nem támogatja a növekményes jóváhagyás.

A fejlesztők számára a teljes körű élményt, ebben a forgatókönyvben a rendelkezik a következő szempontokat:

- Démon alkalmazások az Azure AD-bérlőt csak működik. Ez nincs értelme megtiltani a démon-alkalmazás, amely megpróbálja módosítani személyes Microsoft-fiókok létrehozásához. Ha Ön egy – üzletági (LOB) alkalmazás fejlesztője, a bérlőben a démon alkalmazást fog létrehozni. Ha Ön független Szoftverszállító, érdemes több-bérlős démon alkalmazások létrehozásához. El kell lennie a minden Bérlői rendszergazda által jóváhagyott
- Során a [alkalmazásregisztráció](./scenario-daemon-app-registration.md), a **válasz URI** nem szükséges. Meg kell osztania a titkos kódok és tanúsítványok az Azure ad-vel, és az alkalmazások engedélyek kéréséhez, és biztosítson rendszergazdai jóváhagyás ezen engedélyek használata szükséges.
- A [Alkalmazáskonfiguráció](./scenario-daemon-app-configuration.md) kell adnia a megosztáshoz ügyfél-hitelesítő adatok az Azure AD-alkalmazás regisztrálásakor.
- A [hatókör](scenario-daemon-acquire-token.md#scopes-to-request) használt folyamat kell lennie egy statikus hatókört az ügyfél-hitelesítő adatok a tokenek beszerzésére.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Démon alkalmazások – alkalmazásregisztráció](./scenario-daemon-app-registration.md)
