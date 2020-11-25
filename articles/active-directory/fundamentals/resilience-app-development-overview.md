---
title: Növelje a fejlesztés alatt álló hitelesítési és engedélyezési alkalmazások rugalmasságát
titleSuffix: Microsoft identity platform
description: A Azure Active Directory és a Microsoft Identity platform használatával való alkalmazásfejlesztés rugalmassági útmutatójának áttekintése
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: c2c2f9d0ad7bfa50f543b57326b9fc8dab0069c6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029302"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Növelje a fejlesztés alatt álló hitelesítési és engedélyezési alkalmazások rugalmasságát

A Microsoft Identity modern, jogkivonat-alapú hitelesítést és engedélyezést használ. Ez azt jelenti, hogy egy alkalmazás jogkivonatokat vásárol az identitás-szolgáltatótól a felhasználó hitelesítéséhez és az alkalmazás engedélyezéséhez a védett API-k meghívásához.

Egy jogkivonat egy bizonyos ideig érvényes, mielőtt az alkalmazásnak újat kell bekérnie. Ritkán előfordul, hogy a token lekérésére irányuló hívás egy probléma, például a hálózati vagy infrastrukturális hiba vagy a hitelesítési szolgáltatás kimaradása miatt sikertelen lehet. Ebben a dokumentumban felvázoljuk a fejlesztőknek az alkalmazásokban való rugalmasság növelésének lépéseit, ha jogkivonat-beszerzési hiba történik.

Ez a cikk útmutatást nyújt az alkalmazások rugalmasságának növeléséhez a Microsoft Identity platform és a Azure Active Directory használatával. A bejelentkezett felhasználó nevében dolgozó ügyfélalkalmazások, valamint a saját nevében működő Daemon-alkalmazások is útmutatást nyújtanak. Ajánlott eljárásokat tartalmaznak a jogkivonatok használatához, valamint az erőforrások meghívásához.

- [Rugalmasság kiépítése a bejelentkezési felhasználókat használó alkalmazásokba](resilience-client-app.md)
- [Rugalmasság létrehozása a felhasználók nélküli alkalmazásokban](resilience-daemon-app.md)
- [Hozzon létre rugalmasságot az identitás-és hozzáférés-kezelési infrastruktúrában](resilience-in-infrastructure.md)
- [Hozzon létre rugalmasságot az ügyfelek identitásában és a hozzáférés-kezelésben Azure Active Directory B2C](resilience-b2c.md)
