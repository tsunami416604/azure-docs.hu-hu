---
title: Az Azure multi-factor Authentication - elv
description: "Az Azure Multi-Factor Authentication szolgáltatás révén biztonságosabb a hozzáférés az adatokhoz és az alkalmazásokhoz, és a felhasználók is egyszerűbben jelentkezhetnek be. További biztonságot nyújt, azzal, hogy egy második hitelesítési mód, és kézbesíti az erős hitelesítés keresztül egyszerűen ellenőrzési lehetőségek közül."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 0791ec4c3bf4a69e2a93b8f3dd1dcf9b649f4cff
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure multi-factor Authentication működése
A kétlépéses ellenőrzést biztonságát a réteges megközelítésének rejlik. Jelentős kihívás a támadók számára a több hitelesítési tényezők veszélyeztetése mutatja be. Akkor is, ha egy támadó kezeli, és ismerje meg a jelszót, akkor nem használható a megbízható eszköz a birtokában nélkül is. 

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

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

Az Azure multi-factor Authentication választható ellenőrzési módszereket biztosít a felhő- és a kiszolgáló. Kiválaszthatja, melyik módszerei érhetőek el a felhasználók számára: telefonhívás, szöveges, alkalmazásban megjelenő értesítésre vagy alkalmazás kódokat. További információkért lásd: [választható hitelesítési módszerek](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Következő lépések

- Olvassa el a különböző [a Azure multi-factor Authentication verziók és használat](multi-factor-authentication-versions-plans.md)

- Válassza ki, hogy az Azure MFA telepítendő [a felhőben, vagy a helyszíni](multi-factor-authentication-get-started.md)

- Olvasási válaszok az alkalmazásával kapcsolatos [gyakran ismételt kérdések](multi-factor-authentication-faq.md)