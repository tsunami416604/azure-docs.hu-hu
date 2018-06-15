---
title: Az Azure multi-factor Authentication - elv
description: Az Azure Multi-Factor Authentication szolgáltatás révén biztonságosabb a hozzáférés az adatokhoz és az alkalmazásokhoz, és a felhasználók is egyszerűbben jelentkezhetnek be.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 709fab070533984f94a72ff2136a8bc32fbe6ec6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33865935"
---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure multi-factor Authentication működése
A kétlépéses ellenőrzést biztonságát a réteges megközelítésének rejlik. Jelentős kihívás a támadók számára a több hitelesítési tényezők veszélyeztetése mutatja be. Akkor is, ha egy támadó kezeli, és ismerje meg a jelszót, akkor nem használható a megbízható eszköz a birtokában nélkül is. 

![Proofup](./media/concept-mfa-howitworks/howitworks.png)

Az Azure Multi-Factor Authentication szolgáltatás révén biztonságosabb a hozzáférés az adatokhoz és az alkalmazásokhoz, és a felhasználók is egyszerűbben jelentkezhetnek be.  További biztonságot nyújt, azzal, hogy egy második hitelesítési mód, és kézbesíti az erős hitelesítés keresztül egyszerűen ellenőrzési lehetőségek közül.


## <a name="methods-available-for-two-step-verification"></a>Módszer áll rendelkezésre a kétlépéses ellenőrzéshez
Amikor egy felhasználó bejelentkezik, egy további ellenőrzési küld a felhasználónak.  Az alábbiakban a második ellenőrzési célból használt módszerek listáját.

| Ellenőrzési módszert | Leírás |
| --- | --- |
| Telefonhívás |A meghívásra kerül a felhasználó regisztrált telefonjára. A felhasználó megadja a PIN-kódot, ha szükséges, majd a a # billentyűt nyomja. |
| Szöveges üzenet |Egy szöveges üzenetet küld a felhasználó mobiltelefonra egy hatjegyű kódot. A felhasználó megadja ezt a kódot a bejelentkezési oldalon. |
| Mobilalkalmazás-értesítés |A felhasználó okostelefon egy ellenőrzési kérés érkezik. A felhasználó a PIN-kód kerül, ha szükséges, majd kiválasztja **ellenőrizze** meg a mobilalkalmazásban. |
| Mobilalkalmazásbeli ellenőrző kód |A mobilalkalmazás, amelyen a felhasználó intelligens telefonon, 30 másodperces megváltoztató ellenőrzőkódot jeleníti meg. A felhasználó megkeresi a legújabb kódot, és megadja azt a bejelentkezési oldalon. |
| Harmadik féltől származó OATH jogkivonatokkal | Az Azure multi-factor Authentication kiszolgáló beállítható úgy, hogy fogadja el a harmadik fél hitelesítési módszerek. |

Az Azure multi-factor Authentication választható ellenőrzési módszereket biztosít a felhő- és a kiszolgáló. Kiválaszthatja, melyik módszerei érhetőek el a felhasználók számára: telefonhívás, szöveges, alkalmazásban megjelenő értesítésre vagy alkalmazás kódokat. További információkért lásd: [választható hitelesítési módszerek](howto-mfa-mfasettings.md#selectable-verification-methods).

## <a name="next-steps"></a>További lépések

- Olvassa el a különböző [a Azure multi-factor Authentication verziók és használat](concept-mfa-licensing.md)

- Válassza ki, hogy az Azure MFA telepítendő [a felhőben, vagy a helyszíni](concept-mfa-whichversion.md)

- Olvasási válaszok az alkalmazásával kapcsolatos [gyakran ismételt kérdések](multi-factor-authentication-faq.md)