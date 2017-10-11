---
title: "Az API-k importálnia kell az Azure API Management |} Microsoft Docs"
description: "Útmutató: Azure API Management az API-k és műveletei importálja."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: c851b88fc1067e65044266d07775717c028e75d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Egy API-t az Azure API Management műveletek definíciójának importálása
Az API Management hozhatók létre új API-k, és a műveletek manuálisan hozzáadni, vagy az API-t és a műveletek egy lépésben importálhatók.

API-k és a műveletek importálhatók a következő formátum használatával.

* WADL
* Swagger

Ez az útmutató azt mutatja be hogyan hozzon létre egy új API-t, és importálni egy lépésben műveletei. Létre manuálisan az API-k és műveletek hozzáadásával kapcsolatos tudnivalókat lásd: [API-k létrehozása] [ How to create APIs] és [műveletek felvétele az API-k][How to add operations to an API].

## <a name="import-api"> </a>API importálása
API-k létrehozása és a közzétevő portálon konfigurálva. A közzétevő portál eléréséhez kattintson **Publisher portal** az Azure portálon az API Management szolgáltatás. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.

![Közzétevő portál][api-management-management-console]

Kattintson a **API-k** a a **API Management** a bal oldali menüben, majd **API importálása**.

![Importálási API][api-management-import-apis]

A **importálási API** ablak esetében a három módszerrel adhat meg az API-specifikációnak megfelelő három lappal.

* **A vágólapról** lehetővé teszi az API-specifikációnak illessze be a kijelölt szövegmezőben.
* **A fájl** lehetővé teszi, hogy keresse meg és jelölje ki a fájlt, amely tartalmazza az API-specifikációnak.
* **URL-címről** lehetővé teszi az API-specifikációnak URL-címét.

![Importálási API formátum][api-management-import-api-clipboard]

Miután megadta az API-specifikációnak, a jobb oldalon a megfelelő választógomb segítségével jelzi a meghatározás formátuma. A következő formátum támogatott.

* WADL
* Swagger

Ezután adja meg a **webes API URL-címe utótag**. A rendszer hozzáfűzi az alap URL-címet a API management szolgáltatás. Az alap URL-cím esetében gyakori, az API Management szolgáltatás minden példányának futó minden API. API Management az API-k által az utótag különbözteti meg, és ezért a utótag minden API-nak bizonyos API management szolgáltatás példányainak egyedinek kell lennie.

Amennyiben az összes érték lett megadva, kattintson a **mentése** az API-t és a kapcsolódó műveleteket létrehozásához. 

> [!NOTE]
> Tekintse meg a az oktatóanyagban Swagger formátumú API egyszerű számológép importálására [kezelése az Azure API Management az első API](api-management-get-started.md).
> 
> 

## <a name="export-api"></a> Exportálása az API-k
Is importálja az új API-k, exportálhatja az API-k meghatározását a közzétevő portálról. Ehhez kattintson **exportálása API** a a **összefoglaló lapon** , a **API**.

![Exportálás API][api-management-export-api]

WADL vagy a Swagger API-k lehet exportálni. Válassza ki a kívánt formátumot, kattintson **mentése**, és válassza ki a helyet, ahová menteni a fájlt.

![API-t az exportálási formátumát][api-management-export-api-format]

## <a name="next-steps"> </a>Következő lépések
Után az API-k jön létre, és a műveletek importált, megtekintheti, és konfigurálja az esetleges egyéb beállításokat, az API-t vegye fel a termék, és közzéteszi az, hogy elérhető a fejlesztők számára. További információkért tekintse meg az alábbi útmutatókban.

* [Az API-beállítások konfigurálása][How to configure API settings]
* [Hogyan hozhat létre, és a termék közzététele][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings
