---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184348"
---
#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. A **Regisztrált alkalmazás** áttekintése lapon válassza a **Beállítások lehetőséget.**
1. Az **API Access csoportban**válassza a **Szükséges engedélyek lehetőséget.**
1. Válassza a **Microsoft Graph**lehetőséget.
1. Az **Alkalmazásengedélyek**csoportban jelölje be a felügyeleti alkalmazásnak megadandó engedély jelölőnégyzetét. Példa:
    * **Az összes naplónapló-adat olvasása:** Válassza ezt az engedélyt a könyvtár naplóinak olvasásához.
    * **Címtáradatok olvasása és írása:** Ezt az engedélyt válassza a felhasználó áttelepítéséhez vagy a felhasználókezelési forgatókönyvekhez.
    * **A szervezet megbízhatósági keretházirendjeinek olvasása és írása:** Válassza ezt az engedélyt a folyamatos integrációs/folyamatos kézbesítési (CI/CD) forgatókönyvekhez. Például egyéni szabályzat üzembe helyezését az Azure-folyamatok.
1. Kattintson a **Mentés** gombra.
1. Válassza **az Engedélyek megadása**lehetőséget, majd az **Igen**lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. A **Kezelés csoportban**válassza az **API-engedélyek lehetőséget.**
1. A **Konfigurált engedélyek**csoportban válassza **az Engedély hozzáadása**lehetőséget.
1. Válassza a **Microsoft API-k** lapot, majd a **Microsoft Graph**lehetőséget.
1. Válassza **az Alkalmazásengedélyek lehetőséget.**
1. Bontsa ki a megfelelő engedélycsoportot, és jelölje be a felügyeleti alkalmazásnak megadandó engedély jelölőnégyzetét. Példa:
    * **AuditLog** > **AuditLog.Read.All**: A könyvtár naplóinak olvasásához.
    * **Directory** > **Directory.ReadWrite.All**: Felhasználói áttelepítési vagy felhasználókezelési forgatókönyvekhez.
    * **Policy** > **Policy.ReadWrite.TrustFramework**: Folyamatos integrációs/folyamatos kézbesítési (CI/CD) forgatókönyvekhez. Például egyéni szabályzat üzembe helyezését az Azure-folyamatok.
1. Válassza **az Engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblépne a következő lépésre.
1. Válassza **a Rendszergazdai hozzájárulás megadása (a bérlő neve) lehetőséget.**
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy fiókkal az Azure AD B2C-bérlőben, amely legalább a *felhőalkalmazás-rendszergazdai* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **Frissítés**lehetőséget, majd ellenőrizze, hogy a "Granted for ..." megjelenik az **Állapot**csoportban. Az engedélyek propagálása eltarthat néhány percig.
