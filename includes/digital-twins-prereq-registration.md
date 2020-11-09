---
author: baanders
description: fájl belefoglalása az Azure digitális Twins szolgáltatásba – az alkalmazások regisztrálásának előfeltételei
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124858"
---
A cikkben használt összes erőforrás hitelesítéséhez be kell állítania egy [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) **alkalmazás regisztrációját**. Ennek beállításához kövesse az útmutató [*: alkalmazás regisztrációjának létrehozása*](../articles/digital-twins/how-to-create-app-registration.md) című témakör utasításait. 

Ha már rendelkezik az alkalmazás regisztrálásával, szüksége lesz a regisztrációs **_alkalmazás (ügyfél) azonosítójának_** és **_könyvtárának (BÉRLŐi) azonosítójának_** ( [Keresse meg a Azure Portal](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Jegyezze fel ezeket az értékeket, hogy később használhassa őket az Azure digitális Twins API-khoz.