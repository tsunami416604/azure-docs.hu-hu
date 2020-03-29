---
title: Az Azure API Management terminológia | Microsoft dokumentumok
description: Ez a cikk az API Managementre jellemző kifejezések definícióit adja meg.
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
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61074054"
---
# <a name="terminology"></a>Terminológia

Ez a cikk az API Management (APIM) kifejezések definícióit adja meg.

## <a name="term-definitions"></a>Kifejezésdefiníciók

* **Háttérrendszer API** – Egy HTTP-szolgáltatás, amely megvalósítja az API-t és annak műveleteit. 
* **Frontend API**/**APIM API** - Az APIM API nem üzemeltet API-kat, az API-k homlokzatokat hoz létre annak érdekében, hogy a homlokzatot az igényeinek megfelelően testre szabhassa anélkül, hogy megérintené a háttér-API-t. További információt az [API importálása és közzététele](import-and-publish.md)című témakörben talál.
* **APIM-termék** – egy termék egy vagy több API-t, valamint használati kvótát és használati feltételeket tartalmaz. Számos API-t is megadhat, és a fejlesztői portálon keresztül kínálhatja őket a fejlesztőknek. További információt a Termék létrehozása és közzététele című témakörben [talál.](api-management-howto-add-products.md)
* **APIM API-művelet** – Minden APIM API a fejlesztők számára elérhető műveletek készletét jelöli. Minden APIM API tartalmaz egy hivatkozást a háttérszolgáltatás, amely megvalósítja az API-t, és a műveletek leképezése a háttérszolgáltatás által végrehajtott műveleteket. További információ: [Mock API válaszok](mock-api-responses.md).
* **Verzió** – Néha új vagy különböző API-funkciókat szeretne közzétenni egyes felhasználók számára, míg mások ragaszkodni szeretnének a jelenleg nekik működő API-hoz. További információt az [API több verziójának közzététele](api-management-get-started-publish-versions.md)című témakörben talál.
* **Revision** - Ha az API készen áll a használatra, és elkezdi használni a fejlesztők, általában kell, hogy vigyázzon a módosításokat, hogy az API-t, és ezzel egyidejűleg nem zavarja a hívók az API-t. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. További információ: [A verziók használata](api-management-get-started-revise-api.md).
* **Fejlesztői portál** – Az ügyfeleknek (fejlesztőknek) a fejlesztői portált kell használniuk az API-k eléréséhez. A fejlesztői portál testreszabható. További információt [a Fejlesztői portál testreszabása című témakörben talál.](api-management-customize-styles.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Példány létrehozása](get-started-create-service-instance.md)

