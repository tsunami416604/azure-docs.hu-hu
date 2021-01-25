---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760643"
---
Ez a cikk áttekintést nyújt a különböző típusú hibákról és javaslatokról a gyakori bejelentkezési hibák kezeléséhez.

## <a name="msal-error-handling-basics"></a>MSAL hibák kezelésének alapjai

A Microsoft Authentication Library (MSAL) kivételei az alkalmazások fejlesztői számára készültek a hibák megoldásához, nem pedig a végfelhasználók számára. A kivételek üzenetei nincsenek honosítva.

A kivételek és hibák feldolgozásakor használhatja a kivétel típusát és a hibakódot a kivételek megkülönböztetése érdekében.  A hibakódok listáját lásd: [Azure ad-hitelesítés és-engedélyezési hibakódok](../articles/active-directory/develop/reference-aadsts-error-codes.md).

A bejelentkezési folyamat során hibák merülhetnek fel a beleegyezések, a feltételes hozzáférés (MFA, eszközkezelés, hely-alapú korlátozások), a jogkivonat kiállítása és a visszaváltás, valamint a felhasználói tulajdonságok miatt.

A következő szakasz további részleteket tartalmaz az alkalmazás hibáinak kezelésére vonatkozóan.