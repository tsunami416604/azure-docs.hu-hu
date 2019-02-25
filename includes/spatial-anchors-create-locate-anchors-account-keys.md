---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: cce71736369c5e7c448ccf92f5b569edc30a3966
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753028"
---
## <a name="setting-up-authentication"></a>Hitelesítés beállítása

A szolgáltatás eléréséhez meg kell adnia egy fiókkulcs, a hozzáférési jogkivonat vagy az AAD-hitelesítési token.

### <a name="account-keys"></a>Fiókkulcsok

Fiók kulcsai, amely lehetővé teszi az alkalmazás hitelesítéséhez az Azure térbeli horgonyok szolgáltatással hitelesítő adatot. Az importálni kívánt Fiókkulcsok célja, hogy segít gyorsan használatba vehető. Különösen fejlesztési fázisában Azure térbeli horgonyok integrálható az alkalmazás. Emiatt a Fiókkulcsok is használhatja a történő beágyazásával őket az ügyfél-alkalmazások fejlesztése során. Végrehajtási kívül, mert erősen ajánlott át olyan hitelesítési mechanizmust, amely az éles környezetre, a felhasználó AAD-alapú hitelesítés, illetve hozzáférési jogkivonatok is támogat. Fejlesztési fiók kulcsára, látogasson el a térbeli horgonyok Azure-fiókjával, és keresse meg a "Kulcsok" lapot.
