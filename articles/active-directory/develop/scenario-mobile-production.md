---
title: Mobile App – webes API-k hívása éles környezetben | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást. (Alkalmazások előkészítése éles környezethez)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1a7407c947c1c5bae1eed00acb0f216722f3d09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121034"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobile apps for Products előkészítése

Ez a cikk részletesen ismerteti a mobileszköz minőségének és megbízhatóságának javítását, mielőtt az éles környezetbe helyezi őket.

## <a name="handle-errors"></a>Hibakezelés

Amikor előkészíti a Mobile apps-t éles környezetben, több hiba is előfordulhat. Az Ön által kezelt fő esetek a beavatkozási hibák és tartalékok. Egyéb feltételek, amelyeket érdemes figyelembe vennie a nem hálózati helyzetek, a szolgáltatási kimaradások, a rendszergazdai belefoglalási követelmények és az egyéb forgatókönyv-specifikus esetekben.

Minden Microsoft Authentication Library (MSAL) típushoz megtalálhatja a hibakódokat és a wiki-tartalmakat, amelyek a hibák állapotának kezelését ismertetik:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Problémák enyhítése és vizsgálata

Az alkalmazásban felmerülő problémák jobb diagnosztizálásához gyűjtsön adatokat. További információ a gyűjtött adatok típusairól: a [MSAL-alkalmazások naplózása](./msal-logging.md).

Az adatgyűjtés néhány javaslata:

- A felhasználók segítségére lehetnek, amikor problémák léptek fel. Az ajánlott eljárás az, hogy rögzítse és átmenetileg tárolja a naplókat. Adja meg azt a helyet, ahol a felhasználók fel tudják tölteni a naplókat. A MSAL naplózási bővítményeket biztosít a hitelesítés részletes adatainak rögzítéséhez.

- Ha a telemetria elérhető, engedélyezze a MSAL-en keresztül, hogy adatokat gyűjtsön arról, hogy a felhasználók hogyan jelentkeznek be az alkalmazásba.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

További minták kipróbálásához tekintse meg az [asztali és mobil nyilvános ügyfélalkalmazások](sample-v2-code.md#desktop-and-mobile-public-client-apps)című témakört.