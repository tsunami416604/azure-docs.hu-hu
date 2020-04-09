---
title: Mobilalkalmazás-hívó webes API-k előkészítése éles környezetben | Azure
titleSuffix: Microsoft identity platform
description: További információ a webes API-kat meghívjaó mobilalkalmazás létrehozásáról. (Alkalmazások előkészítése éles környezetben.)
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
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882539"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobilalkalmazások előkészítése éles környezetre

Ez a cikk részletesen ismerteti, hogyan javíthatja a mobilalkalmazás minőségét és megbízhatóságát, mielőtt éles környezetbe helyezne.

## <a name="handle-errors"></a>Hibakezelés

Amikor előkészíti a mobilalkalmazást az éles környezetben, számos hibakörülmény fordulhat elő. A fő esetek, amelyeket kezelni fog, a csendes hibák és az interakcióhoz való tartalék. Egyéb feltételek, amelyekfigyelembe kell vennie a hálózati helyzetek nélküli, szolgáltatás kimaradások, felügyeleti hozzájáruláskövetelményeinek és egyéb forgatókönyv-specifikus esetek.

Minden Microsoft Authentication Library (MSAL) típushoz megtalálható a hibafeltételek kezelését leíró mintakód és wikitartalom:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Problémák enyhítése és vizsgálata

Az alkalmazás problémáinak jobb diagnosztizálása érdekében gyűjtsön adatokat. Az adatgyűjtéshez szükséges adatokról az [MSAL-alkalmazások naplózása](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)című témakörben talál további információt.

Íme néhány javaslat az adatgyűjtésre:

- A felhasználók segítséget kérhetnek, ha problémáik vannak. Ajánlott eljárás a naplók rögzítése és ideiglenes tárolása. Adjon meg egy helyet, ahol a felhasználók feltölthetik a naplókat. Az MSAL naplózási bővítményeket biztosít a hitelesítéssel kapcsolatos részletes információk rögzítéséhez.

- Ha a telemetriai adatok rendelkezésre állnak, engedélyezze, hogy az MSAL-on keresztül adatokat gyűjtsön arról, hogy a felhasználók hogyan jelentkeznek be az alkalmazásba.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

További minták kipróbálásáról az [Asztali és mobil nyilvános ügyfélalkalmazások](sample-v2-code.md#desktop-and-mobile-public-client-apps)ban talál.
