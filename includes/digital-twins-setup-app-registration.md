---
author: baanders
description: fájl belefoglalása a hozzáférési engedélyek lépéshez az Azure digitális Twins beállítása beállításban
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408291"
---
Miután beállította az Azure Digital Twins-példányt, gyakori, hogy egy ügyfélalkalmazás használatával kommunikáljon az adott példánnyal. Egy működő ügyfélalkalmazás létrehozásához meg kell győződnie arról, hogy az ügyfélalkalmazás képes lesz hitelesíteni az Azure digitális Twins szolgáltatásban. Ezt úgy teheti meg, hogy egy [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) **alkalmazás regisztrációját** állítja be a használni kívánt ügyfélalkalmazás számára.

Az alkalmazás regisztrálása az [Azure Digital Twins API](../articles/digital-twins/how-to-use-apis-sdks.md)-khoz való hozzáférési engedélyek konfigurálására szolgál. Később az ügyfélalkalmazás hitelesíteni fogja az alkalmazás regisztrációját, és ennek eredményeképpen a konfigurált hozzáférési engedélyek lesznek elérhetők az API-khoz.

>[!TIP]
> Az előfizetés tulajdonosaként érdemes lehet új alkalmazás-regisztrációt beállítani minden új Azure Digital Twins-példányhoz, *vagy* ezt csak egyszer kell létrehoznia, amely egyetlen alkalmazás-regisztrációt hoz létre, amely az előfizetés összes Azure digitális Twins-példánya között meg lesz osztva.

### <a name="create-the-registration"></a>A regisztráció létrehozása