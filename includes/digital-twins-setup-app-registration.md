---
author: baanders
description: fájl belefoglalása a hozzáférési engedélyek lépéshez az Azure digitális Twins beállítása beállításban
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009653"
---
Miután beállította az Azure Digital Twins-példányt, gyakori, hogy egy ügyfélalkalmazás használatával kommunikáljon az adott példánnyal. Egy működő ügyfélalkalmazás létrehozásához meg kell győződnie arról, hogy az ügyfélalkalmazás képes lesz hitelesíteni az Azure digitális Twins szolgáltatásban. Ezt úgy teheti meg, hogy egy [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) **alkalmazás regisztrációját** állítja be a használni kívánt ügyfélalkalmazás számára.

Az alkalmazás regisztrálása az [Azure Digital Twins API](../articles/digital-twins/how-to-use-apis-sdks.md)-khoz való hozzáférési engedélyek konfigurálására szolgál. Később az ügyfélalkalmazás hitelesíteni fogja az alkalmazás regisztrációját, és ennek eredményeképpen a konfigurált hozzáférési engedélyek lesznek elérhetők az API-khoz.

>[!TIP]
> Az előfizetés tulajdonosaként/rendszergazdáként érdemes lehet új alkalmazás-regisztrációt beállítani minden új Azure Digital Twins-példányhoz, *vagy* csak egyszer megtenni, hogy egyetlen alkalmazás-regisztrációt hozzon létre, amely az előfizetés összes Azure digitális Twins-példánya között meg lesz osztva.

### <a name="create-the-registration"></a>A regisztráció létrehozása