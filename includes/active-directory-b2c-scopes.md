---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183379"
---
#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Válassza az **alkalmazások**lehetőséget.
1. Válassza ki a *webapi1* alkalmazást a **Tulajdonságok** lap megnyitásához.
1. Válassza a **közzétett hatókörök**elemet. A közzétett hatókörökkel egy ügyfélalkalmazás bizonyos engedélyeket biztosíthat a webes API-nak.
1. A **hatókör**mezőbe írja be a `demo.read`, és a **Leírás**mezőbe írja be a `Read access to the web API`.
1. A **hatókör**mezőbe írja be a `demo.write`, és a **Leírás**mezőbe írja be a `Write access to the web API`.
1. Kattintson a **Mentés** gombra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget.
1. Válassza ki a *webapi1* alkalmazást az **áttekintő** oldal megnyitásához.
1. A **kezelés**területen válassza **az API közzététele**lehetőséget.
1. Az **alkalmazás-azonosító URI-ja**mellett válassza a **beállítás** hivatkozást.
1. Cserélje le az alapértelmezett értéket (GUID) `api`, majd válassza a **Mentés**lehetőséget. A teljes URI látható, és `https://your-tenant-name.onmicrosoft.com/api`formátumban kell lennie. Ha a webalkalmazás hozzáférési jogkivonatot kér az API-hoz, adja hozzá ezt az URI-t az API-hoz definiált minden hatókör előtagjaként.
1. Az **API által definiált hatókörök**területen válassza **a hatókör hozzáadása**elemet.
1. Adja meg a következő értékeket egy olyan hatókör létrehozásához, amely az API olvasási hozzáférését határozza meg, majd válassza a **hatókör hozzáadása**elemet:
    1. **Hatókör neve**: `demo.read`
    1. **Rendszergazdai beleegyező megjelenítendő név**: `Read access to demo API`
    1. **Rendszergazdai engedély leírása**: `Allows read access to the demo API`
1. Válassza a **hatókör hozzáadása**lehetőséget, és adja meg az alábbi értékeket egy olyan hatókör hozzáadásához, amely írási hozzáférést definiál az API-hoz, majd válassza a **hatókör hozzáadása**elemet:
    1. **Hatókör neve**: `demo.write`
    1. **Rendszergazdai beleegyező megjelenítendő név**: `Write access to demo API`
    1. **Rendszergazdai engedély leírása**: `Allows write access to the demo API`