---
title: "Az Azure API Management terminológiája |} Microsoft Docs"
description: "Ez a cikk az API Management szakkifejezéseit definíciók ad."
services: api-management
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 9391b65a5aade4c050ca964354bfea2d3a2338d8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="terminology"></a>Terminológia

Ez a cikk az adott API-felügyeleti (APIM) kifejezéssel definíciók ad.

## <a name="term-definitions"></a>Kifejezés definíciók

* **Háttér-API** -egy HTTP-szolgáltatás, amely megvalósítja az API-t és a műveleteket. 
* **Előtérbeli API**/**APIM API** -egy APIM API nem működik az API-k, homlokzatoknál hoz létre a biztonsági érintése nélkül a homlokzati igényei szerint testre az API-kat API végén. További információkért lásd: [importálása és tegye közzé az API-k](import-and-publish.md).
* **APIM termék** -a termék egy vagy több API-k, valamint a memóriahasználati kvóta és a használati feltételeket tartalmazza. Számos olyan API-k, és a fejlesztői portálon keresztül a fejlesztők számára biztosíthat számukra. További információkért lásd: [létrehozása és közzététele egy termék](api-management-howto-add-products.md).
* **APIM API-művelet** -fejlesztők számára elérhető műveletek készletét reprezentálja, minden APIM API. Minden egyes APIM API tartalmaz egy hivatkozást a háttér-szolgáltatás, amely megvalósítja az API-t, és a műveletek leképezés a háttér-szolgáltatás által megvalósított műveletekre. További információkért lásd: [Mock API válaszok](mock-api-responses.md).
* **Verzió** - néha közzé szeretné tenni az új vagy eltérő API szeretne egyes felhasználóknak szolgáltatásokat, míg mások szeretné, hogy az API-t, amely jelenleg működik a számukra odatapadjon. További információkért lásd: [közzététele az API-t több verziójának](api-management-get-started-publish-versions.md).
* **Változat** - amikor az API-Ugrás kész, és kezdődik, akkor általában a fejlesztők által használandó kell gondoskodunk a módosítások elvégzése az adott API, és nem zavarja a API hívóknak az egy időben. Akkor célszerű is, hogy a fejlesztők a végrehajtott változtatásokat ismernie. További információkért lásd: [használja a változatok](api-management-get-started-revise-api.md).
* **Fejlesztői portálján** -ügyfelei az alábbiakra (fejlesztők) az API-k eléréséhez használ a fejlesztői portálján. A fejlesztői portálján testre szabható. További információkért lásd: [testre szabhatja a fejlesztői portálján](api-management-customize-styles.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy példányt](get-started-create-service-instance.md)

