---
title: A mobilalkalmazás, hogy hívások webes API-kat (Váltás az éles környezetben) – a Microsoft identity platform
description: Ismerje meg a mobilalkalmazások, hogy a hívások webes API-k (Váltás az éles környezetben)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962359"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobilalkalmazás, amely meghívja a webes API-k – helyezze át az éles környezetbe

Ez a cikk részletesen ismerteti a minőség és a megbízhatóság az alkalmazás javítása, az éles környezetbe való áthelyezése előtt.

## <a name="handling-errors-in-mobile-applications"></a>A mobilalkalmazások hibák kezelése

Számos esetben hiba fordul elő az alkalmazás ezen a ponton. A főbb forgatókönyvek kezeléséhez a következők: beavatkozás nélküli hibák és interakció DecoderReplacementFallback(""). Egyéb feltételeket, amelyeket érdemes éles környezetben nem hálózati helyzetek, szolgáltatás-kimaradások, rendszergazdai jóváhagyás követelményei és más forgatókönyvekre jellemző esetben tartalmazza.

Minden egyes MSAL függvénytár minta kódja és wiki tartalom, amely azt ismerteti, hogyan kezelje ezeket a feltételeket:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Az MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Csökkentése és a problémák kivizsgálása

Diagnosztizálhatja a problémákat az alkalmazásban, segít a adatainak gyűjtéséről. Milyen típusú adatok kapcsolatos információk gyűjtése, tekintse meg az MSAL platform wikit.

- Felhasználók előfordulhat, hogy kérjen segítséget, ha problémákba. Ajánlott eljárás, hogy rögzíteni és ideiglenesen naplók tárolására, és adjon meg egy helyet, ahol a felhasználók is feltölthetők. Az MSAL naplózási bővítmények hitelesítési vonatkozó részletes információkat biztosít.
- Ha elérhető, az MSAL felhasználók bejelentkezik módját, az alkalmazás kapcsolatos adatokhoz keresztül telemetria engedélyezése.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
