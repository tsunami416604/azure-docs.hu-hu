---
author: baanders
description: fájl belefoglalása az Azure digitális Twins szolgáltatásba – az alkalmazások regisztrálásának előfeltételei
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020303"
---
A cikkben használt összes erőforrás hitelesítéséhez be kell állítania egy [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) **alkalmazás regisztrációját**. Ennek beállításához kövesse az útmutató [*: alkalmazás regisztrációjának létrehozása*](../articles/digital-twins/how-to-create-app-registration.md) című témakör utasításait. 

Ha már rendelkezik az alkalmazás regisztrálásával, szüksége lesz a regisztrációs **_alkalmazás (ügyfél) azonosítójának_** és **_könyvtárának (BÉRLŐi) azonosítójának_** ([Keresse meg a Azure Portal](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Jegyezze fel ezeket az értékeket, hogy később használhassa őket az Azure digitális Twins API-khoz.