---
title: Azure API Management-terminológia | Microsoft Docs
description: Ez a cikk a API Managementra vonatkozó feltételek definícióit tartalmazza.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 5bc76d2526c5585071a240af36b8a31e3de5708f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024928"
---
# <a name="azure-api-management-terminology"></a>Azure API Management-terminológia

Ez a cikk a API Managementra (APIM) vonatkozó feltételek definícióit ismerteti.

## <a name="term-definitions"></a>Kifejezés-definíciók

* **Háttérbeli API** – egy http-szolgáltatás, amely megvalósítja az API-t és annak műveleteit. 
* **FRONTEND API** / **APIM API** – egy APIM API nem üzemeltet API-kat, és az API-k számára is létrehoz egy homlokzatot, hogy az igényeinek megfelelően testreszabja a homlokzatot anélkül, hogy a háttér API-t kellene megérintenie. További információ: [API importálása és közzététele](import-and-publish.md).
* **APIM termék** – egy termék tartalmaz egy vagy több API-t, valamint egy használati kvótát és a használati feltételeket. Több API-t is megadhat, és a fejlesztői portálon keresztül biztosíthatja őket a fejlesztőknek. További információ: [termékek létrehozása és közzététele](api-management-howto-add-products.md).
* **APIM API-művelet** – minden APIM API a fejlesztők számára elérhető műveletek készletét jelöli. Minden APIM API tartalmaz egy hivatkozást a háttérrendszer számára, amely megvalósítja az API-t, valamint a műveleteik leképezését a háttérrendszer által megvalósított műveletekre. További információ: az [API-válaszok modellezése](mock-api-responses.md).
* **Verzió** – előfordulhat, hogy egyes felhasználók számára új vagy más API-szolgáltatásokat szeretne közzétenni, míg mások a jelenleg használt API-val szeretnének ragaszkodni. További információ: [az API több verziójának közzététele](api-management-get-started-publish-versions.md).
* **Változat** – ha az API készen áll a fejlesztésre, és a fejlesztők megkezdik a használatát, akkor általában ügyelnie kell arra, hogy módosítsa az API-t, és egyidejűleg ne zavarja meg az API hívóit. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. További információ: [változatok használata](api-management-get-started-revise-api.md).
* **Fejlesztői portál** – ügyfelei (fejlesztői) a fejlesztői portál használatával férhetnek hozzá az API-khoz. A fejlesztői portál testreszabható. További információ: [a fejlesztői portál testreszabása](api-management-customize-styles.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Példány létrehozása](get-started-create-service-instance.md)

