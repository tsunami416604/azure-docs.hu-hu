---
author: baanders
description: fájl belefoglalása az Azure digitális Twins szolgáltatásba – az alkalmazások regisztrálásának előfeltételei
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231446"
---
A cikkben használt összes erőforrás hitelesítéséhez **be kell állítania egy [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) alkalmazás regisztrációját**. Ennek beállításához kövesse az útmutató [*: alkalmazás regisztrációjának létrehozása*](../articles/digital-twins/how-to-create-app-registration.md) című témakör utasításait. 

Ha már rendelkezik az alkalmazás regisztrálásával, szüksége lesz a regisztrációs **_alkalmazás (ügyfél) azonosítójának_** és **_könyvtárának (BÉRLŐi) azonosítójának_** ([Keresse meg a Azure Portal](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Jegyezze fel ezeket az értékeket, hogy később használhassa őket az Azure digitális Twins API-khoz.