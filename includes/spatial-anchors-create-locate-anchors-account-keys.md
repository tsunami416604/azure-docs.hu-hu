---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993106"
---
## <a name="set-up-authentication"></a>Hitelesítés beállítása

A szolgáltatás eléréséhez meg kell adnia egy fiókot, hozzáférési tokent vagy Azure Active Directory hitelesítési tokent. Erről további információt a [hitelesítési koncepció oldalon](../articles/spatial-anchors/concepts/authentication.md)olvashat.

### <a name="account-keys"></a>Fiók kulcsai

A fiók kulcsa olyan hitelesítő adat, amely lehetővé teszi az alkalmazás számára az Azure térbeli horgonyok szolgáltatással történő hitelesítést. A fiók kulcsainak célja, hogy segítséget nyújtson a gyors kezdéshez. Különösen az alkalmazás Azure térbeli Horgonyokkal való integrációjának fejlesztési fázisában. Ennek megfelelően a fiókok kulcsait úgy használhatja, hogy a fejlesztés során beágyazza őket az ügyfélalkalmazásokba. Ahogy a fejlesztésen kívül halad, javasoljuk, hogy olyan hitelesítési mechanizmusra váltson, amely a hozzáférési jogkivonatok által támogatott, illetve a felhasználói hitelesítés Azure Active Directory. A fiók kulcsának beszerzéséhez látogasson el az Azure térbeli horgonyok fiókjába, és lépjen a "kulcsok" lapra.