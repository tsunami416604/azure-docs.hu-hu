---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126738"
---
## <a name="disable-email-verification"></a>E-mailes ellenőrzés letiltása

Alapértelmezés szerint a Azure Active Directory B2C (Azure AD B2C) ellenőrzi az ügyfél e-mail-címét a helyi fiókok számára (fiókok azon felhasználók számára, akik e-mail-címmel vagy felhasználónévvel jelentkeznek be). Azure AD B2C az érvényes e-mail-címeket biztosítja, hogy az ügyfeleknek meg kell győződniük a regisztrációs folyamat során. Emellett megakadályozza, hogy a rosszindulatú szereplők automatizált folyamatokat használjanak az alkalmazásokban lévő csalárd fiókok létrehozásához.

Egyes alkalmazás-fejlesztők szívesebben szeretnék kihagyni az e-mailek ellenőrzését a regisztrálási folyamat során, hanem az ügyfelek az e-mail-címüket később is ellenőrzik. Ennek támogatásához Azure AD B2C konfigurálható az e-mail ellenőrzésének letiltására. Ez létrehoz egy simább regisztrációs folyamatot, és lehetővé teszi a fejlesztők számára a rugalmasságot, hogy megkülönböztessék azokat az ügyfeleket, akik nem rendelkeznek az e-mail-címükkel.

> [!WARNING]
> Ha letiltja az e-mailek ellenőrzését a regisztrációs folyamatban, a levélszemét is vezethet. Ha letiltja az alapértelmezett Azure AD B2C által biztosított e-mail-ellenőrzést, javasoljuk, hogy hozzon létre egy helyettesítő ellenőrzési rendszerét.
