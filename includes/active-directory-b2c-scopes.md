---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183379"
---
#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Válassza **az Alkalmazások**lehetőséget.
1. Válassza ki a *webapi1* alkalmazást a **Tulajdonságok** lap megnyitásához.
1. Válassza **a Közzétett hatókörök lehetőséget.** A közzétett hatókörök segítségével bizonyos engedélyeket adhat egy ügyfélalkalmazásnak a webes API-hoz.
1. A **HATÓKÖR** `demo.read`mezőbe írja be `Read access to the web API`a LEHETŐSÉGET, a **LEÍRÁS**mezőbe pedig a mezőbe írja be a mezőbe.
1. A **HATÓKÖR** `demo.write`mezőbe írja be `Write access to the web API`a LEHETŐSÉGET, a **LEÍRÁS**mezőbe pedig a mezőbe írja be a mezőbe.
1. Kattintson a **Mentés** gombra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget.
1. Válassza ki a *webapi1* alkalmazást az **Áttekintő** lap megnyitásához.
1. A **Kezelés csoportban**válassza **az API-k felfedése**lehetőséget.
1. Az **Alkalmazásazonosító URI mellett**válassza a **Set** link lehetőséget.
1. Cserélje le az alapértelmezett értéket `api`(GUID azonosítót) a értékre, majd válassza a **Mentés**lehetőséget. Megjelenik a teljes URI, és `https://your-tenant-name.onmicrosoft.com/api`formátumban kell lennie. Amikor a webalkalmazás hozzáférési jogkivonatot kér az API-hoz, hozzá kell adnia ezt az URI-t az API-hoz megadott minden egyes hatókör előtagjaként.
1. Az **API által definiált hatókörök csoportban**válassza **a Hatókör hozzáadása**lehetőséget.
1. Adja meg a következő értékeket az API olvasási hozzáférését meghatározó hatókör létrehozásához, majd válassza a **Hatókör hozzáadása**lehetőséget:
    1. **Hatókör neve**:`demo.read`
    1. **Rendszergazdai hozzájárulás megjelenítendő neve**:`Read access to demo API`
    1. **Rendszergazdai hozzájárulás leírása:**`Allows read access to the demo API`
1. Válassza **a Hatókör hozzáadása**lehetőséget, adja meg a következő értékeket az API-hoz való írási hozzáférést definiáló hatókör hozzáadásához, majd válassza a Hatókör **hozzáadása**lehetőséget:
    1. **Hatókör neve**:`demo.write`
    1. **Rendszergazdai hozzájárulás megjelenítendő neve**:`Write access to demo API`
    1. **Rendszergazdai hozzájárulás leírása:**`Allows write access to the demo API`