---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "76694317"
---
## <a name="set-up-authentication"></a>Hitelesítés beállítása

A szolgáltatás eléréséhez meg kell adnia egy fiókot, hozzáférési tokent vagy Azure Active Directory hitelesítési tokent. Erről további információt a [hitelesítési koncepció oldalon](/azure/spatial-anchors/concepts/authentication)olvashat.

### <a name="account-keys"></a>Fiók kulcsai

A fiók kulcsa olyan hitelesítő adat, amely lehetővé teszi az alkalmazás számára az Azure térbeli horgonyok szolgáltatással történő hitelesítést. A fiók kulcsainak célja, hogy segítséget nyújtson a gyors kezdéshez. Különösen az alkalmazás Azure térbeli Horgonyokkal való integrációjának fejlesztési fázisában. Ennek megfelelően a fiókok kulcsait úgy használhatja, hogy a fejlesztés során beágyazza őket az ügyfélalkalmazásokba. Ahogy a fejlesztésen kívül halad, javasoljuk, hogy olyan hitelesítési mechanizmusra váltson, amely a hozzáférési jogkivonatok által támogatott, illetve a felhasználói hitelesítés Azure Active Directory. A fiók kulcsának beszerzéséhez látogasson el az Azure térbeli horgonyok fiókjába, és lépjen a "kulcsok" lapra.
