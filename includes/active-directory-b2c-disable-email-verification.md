---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126738"
---
## <a name="disable-email-verification"></a>E-mailes ellenőrzés letiltása

Alapértelmezés szerint az Azure Active Directory B2C (Azure AD B2C) ellenőrzi az ügyfél e-mail címét a helyi fiókok (fiókok a felhasználók számára, akik feliratkozik az e-mail cím vagy felhasználónév). Az Azure AD B2C biztosítja az érvényes e-mail-címeket azáltal, hogy az ügyfelektől megköveteli, hogy a regisztrációs folyamat során ellenőrizzék azokat. Azt is megakadályozza, hogy a rosszindulatú szereplők automatizált folyamatok segítségével hozzanak létre csalárd fiókokat az alkalmazásokban.

Egyes alkalmazásfejlesztők szívesebben hagyják ki az e-mailek ellenőrzését a regisztrációs folyamat során, és ehelyett az ügyfelek később ellenőrzik az e-mail címüket. Ennek támogatása érdekében az Azure AD B2C konfigurálható az e-mail-ellenőrzés letiltására. Ezzel zökkenőmentesebb regisztrációs folyamatot hoz létre, és rugalmasságot biztosít a fejlesztők számára, hogy megkülönböztessék azokat az ügyfeleket, akik ellenőrizték az e-mail címüket azoktól az ügyfelektől, akik nem.

> [!WARNING]
> Az e-mailek ellenőrzésének letiltása a regisztrációs folyamatban levélszeméthez vezethet. Ha letiltja az alapértelmezett Azure AD B2C által biztosított e-mail-ellenőrzés, azt javasoljuk, hogy egy csere-ellenőrzési rendszer.
