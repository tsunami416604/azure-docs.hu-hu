---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: 8363d023e89c77aabc0d123f19264c9a0758a656
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208243"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Mobil vagy natív alkalmazásának regisztrálásához használja a táblázatban megadott beállításokat.

![Példák az új mobil vagy natív alkalmazások regisztrációs beállításaira](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Beállítás      | Mintaérték  | Leírás                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name (Név)** | Contoso B2C-alkalmazás | Adjon meg egy olyan **nevet**, amely ismerteti az alkalmazást a felhasználók számára. |
| **Natív ügyfél** | Igen | Válassza az **Igen** lehetőséget mobil vagy natív alkalmazás esetén. |
| **Egyéni átirányítási URI** | `com.onmicrosoft.contoso.appname://redirect/path` | Adjon meg egy egyéni sémával rendelkező átirányítási URI-t. Ügyeljen arra, hogy [megfelelő átirányítási URI-t](../articles/active-directory-b2c/active-directory-b2c-app-registration.md) válasszon, és különleges karakterek, például aláhúzásjelek ne szerepeljenek benne. |

Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.

Az újonnan regisztrált alkalmazás a B2C-bérlő alkalmazásainak listájában jelenik meg. Válassza ki mobil vagy natív alkalmazását a listáról. Ekkor megjelenik az alkalmazás tulajdonságpanelje.

![Az alkalmazás tulajdonságai](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Jegyezze fel az alkalmazás globálisan egyedi **ügyfél-azonosítóját**. Ezt az azonosítót használja az alkalmazás kódjában.

Ha natív alkalmazása az Azure AD B2C által védett webes API-t hív meg, kövesse a következő lépéseket:
   1. Hozzon létre egy alkalmazáskulcsot a **Kulcsok** panel **Kulcs létrehozása** gombjára kattintva. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket fogja használni az alkalmazás kódjában alkalmazáskulcsként.
   2. Kattintson az **API-hozzáférés**, majd a **Hozzáadás**, lehetőségre, és válassza ki webes API-ját és a hatóköröket (engedélyeket).

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
> 
