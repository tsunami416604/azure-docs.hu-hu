---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: afa3fea0c172134afb06abc63c90805a32c89639
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907693"
---
## <a name="set-up-authentication"></a>Hitelesítés beállítása

A szolgáltatás eléréséhez meg kell adnia egy fiókkulcs, a hozzáférési jogkivonat vagy az AAD-hitelesítési token.

### <a name="account-keys"></a>Fiókkulcsok

Fiók kulcsai, amely lehetővé teszi az alkalmazás hitelesítéséhez az Azure térbeli horgonyok szolgáltatással hitelesítő adatot. Az importálni kívánt Fiókkulcsok célja, hogy segít gyorsan használatba vehető. Különösen fejlesztési fázisában Azure térbeli horgonyok integrálható az alkalmazás. Emiatt a Fiókkulcsok is használhatja a történő beágyazásával őket az ügyfél-alkalmazások fejlesztése során. Végrehajtási kívül, mert erősen ajánlott át olyan hitelesítési mechanizmust, amely az éles környezetre, a felhasználó AAD-alapú hitelesítés, illetve hozzáférési jogkivonatok is támogat. Fejlesztési fiók kulcsára, látogasson el a térbeli horgonyok Azure-fiókjával, és keresse meg a "Kulcsok" lapot.
