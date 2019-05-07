---
title: A mobilalkalmazás, hogy hívások webes API-kat (Váltás az éles környezetben) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre, amely meghívja a webes API-k (Váltás az éles környezetben) mobilalkalmazás
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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074950"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobilalkalmazás, amely meghívja a webes API-k – helyezze át az éles környezetbe

Ez a cikk részletesen a minőségi és helyezze át az éles környezetbe való az alkalmazás megbízhatóságát.

## <a name="handling-errors-in-mobile-applications"></a>A mobilalkalmazások hibák kezelése

Eddig már kihirdettük különböző folyamatot nincsenek különböző hibaállapotok, amelyek oka lehet. Az elsődleges forgatókönyv kezelésére csendes hibák és a tartalék való interakciót. Azt is figyelembe kell venni, éles környezetben, beleértve a nem hálózati helyzetek, szolgáltatás-kimaradások, rendszergazdai jóváhagyás szükséges és más forgatókönyvekre jellemző esetben kiegészítő feltételek vonatkoznak.

Minden egyes MSAL függvénytár minta kódja és wiki tartalom ismertetőinken mélyebb, ezek a feltételek kezelésére.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Az MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Csökkentése és a problémák kivizsgálása

Adatok gyűjtése segít az alkalmazás diagnosztizálhatja a problémákat. További részleteket az adatok típusát is gyűjthet, tekintse meg az egyes MSAL platformok wiki.

- Amikor egy probléma felhasználók kérhet segítséget. Ajánlott eljárás, hogy rögzítése és ideiglenesen naplók tárolására, és lehetővé teszi a felhasználók valahol fel őket. Az MSAL naplózási bővítmények Outlookhoz vonatkozó részletes információkat biztosít
- Ha elérhető, engedélyezze a telemetriai adatok gyűjtéséhez hogyan jelentkezik be az alkalmazás adatait az MSAL használatával.

## <a name="testing-your-app"></a>Az alkalmazás tesztelése

Tesztelje az alkalmazását a [integrációs ellenőrzőlista](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
