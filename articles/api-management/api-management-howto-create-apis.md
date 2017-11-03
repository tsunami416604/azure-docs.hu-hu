---
title: "API-k létrehozása az Azure API Management"
description: "Megtudhatja, hogyan hozza létre és az Azure API Management API-k konfigurálja."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 14c20da4-f29f-4b28-bec7-3d4c50b734da
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 79630fb8998d47aab8eed38daf7d5fcef3d870d7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-apis-in-azure-api-management"></a>API-k létrehozása az Azure API Management
Az API Management API ügyfélalkalmazások meghívható műveletkészlet jelöli. Új API-k jönnek létre a közzétevő portálon, és majd a kívánt műveletek kerülnek. A műveletek kerülnek, az API-t egy termék hozzáadódik és tehetők közzé. Miután közzétette az API-k, az előfizetett, és a fejlesztők használják.

Ez az útmutató bemutatja az első lépés a folyamat: létrehozása és konfigurálása egy új API-t az API Management. Műveletek hozzáadását és közzététele a termék további információkért lásd: [műveletek felvétele az API-k] [ How to add operations to an API] és [létrehozása és közzététele egy termék][How to create and publish a product].

## <a name="create-new-api"></a>Egy új API-t létrehozni
API-k létrehozása és a közzétevő portálon konfigurálva. A közzétevő portál eléréséhez kattintson **Publisher portal** az Azure portálon az API Management szolgáltatás.

![Közzétevő portál][api-management-management-console]

> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Kattintson a **API-k** a a **API Management** a bal oldali menüben, majd **API hozzáadása**.

![API-t létrehozni][api-management-create-api]

Használja a **adja hozzá az új API** ablak konfigurálása az új API-t.

![Új API hozzáadása][api-management-add-new-api]

A következő mezőket az új API-t konfigurálására szolgálnak.

* **Webes API neve** az API-t egyedinek és leírónak elnevezi. A fejlesztői és publisher portálon megjelenik.
* **Webszolgáltatás URL-címe** végrehajtási az API-t a HTTP-szolgáltatás hivatkozik. Az API management erre a címre kérelmeket továbbítja.
* **Webes API URL-címe utótag** az alap URL-címet a API management szolgáltatás a rendszer hozzáfűzi. Az alap URL-cím esetében gyakori, minden API-kezelés szolgáltatás példánya által üzemeltetett API. API Management az API-k által az utótag különbözteti meg, és ezért a utótag minden API-hoz. a megadott közzétevő egyedinek kell lennie.
* **Webes API URL-séma** meghatározza, hogy mely protokollokkal az API eléréséhez. HTTPs alapértelmezés szerint van megadva.
* Opcionálisan adja hozzá az új API termékre, kattintson a **(nem kötelező) termékek** legördülő listán, és válassza ki a termék. Ezt a lépést többször hozzáadni az API-t több termék is kell ismételni.

Kattintson a kívánt értékeket konfigurálása után **mentése**. Az új API létrehozása után az API-t az összefoglalás lapon megjelenik a közzétevő portálon.

![API összefoglaló][api-management-api-summary]

## <a name="configure-api-settings"></a>Konfigurálhatja az API-beállítások
Használhatja a **beállítások** lapon győződjön meg arról, és az API konfigurációjának a szerkesztésével. **Webes API neve**, **webszolgáltatás URL-címe**, és **webes API URL-címe utótag** vannak először állítja az API-t jön létre, és módosíthatja itt. **Leírás** biztosít az opcionális leírást, és **webes API URL-séma** meghatározza, hogy mely protokollokkal az API eléréséhez.

![Az API-beállítások][api-management-api-settings]

Az API-t végrehajtási háttérszolgáltatást átjáró hitelesítés konfigurálásához jelölje ki a **biztonsági** fülre. A **hitelesítő adatokkal rendelkező** legördülő konfigurálásához használható **HTTP basic** vagy **ügyféltanúsítványok** hitelesítés. Egyszerű HTTP-hitelesítést használ, egyszerűen adja meg a kívánt hitelesítő adatokat. Ügyféltanúsítvány-alapú hitelesítés használatával kapcsolatos információkért lásd: [biztonságossá tétele a háttér-szolgáltatásaihoz ügyfél használatával az Azure API Management tanúsítványhitelesítés][How to secure back-end services using client certificate authentication in Azure API Management].

A **biztonsági** lapon is használható konfigurálása **felhasználói engedélyezési** az OAuth 2.0 verziót használja. További információkért lásd: [hogyan szeretné engedélyekkel felruházni az OAuth 2.0 verziót használja az Azure API Management fejlesztői fiókok][How to authorize developer accounts using OAuth 2.0 in Azure API Management].

![Egyszerű hitelesítési beállítások][api-management-api-settings-credentials]

Kattintson a **mentése** az API-beállítások gombra.

## <a name="next-steps"></a>Következő lépések
Miután az API-k jön létre, és a konfigurált beállítások a következő lépések hozzáadása a műveletek az API-t, adja hozzá az API-t egy termék, és közzéteszi az, hogy elérhető a fejlesztők számára. További információkért tekintse meg a következő cikkekben talál.

* [Műveletek az API-k hozzáadása][How to add operations to an API]
* [Hogyan hozhat létre, és a termék közzététele][How to create and publish a product]

[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[How to secure back-end services using client certificate authentication in Azure API Management]: api-management-howto-mutual-certificates.md
[How to authorize developer accounts using OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md
