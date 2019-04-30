---
title: Az Azure API Management terminológiája |} A Microsoft Docs
description: Ez a cikk a feltételeket, amelyeket az API Management jellemző definíciókat megtalálható.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074054"
---
# <a name="terminology"></a>Terminológia

Ez a cikk a feltételeket, amelyeket az API Management (APIM) jellemző definíciókat megtalálható.

## <a name="term-definitions"></a>Kifejezés definíciók

* **Háttérrendszeri API-t** – egy HTTP-szolgáltatás, amely megvalósítja az API-t és a műveleteket. 
* **Előtérbeli API**/**APIM API** – az APIM API nem működik az API-k, homlokzatoknál hoz létre az API-k, annak érdekében, hogy a adapterréteget igény szerint testreszabhatja vissza a módosítása nélkül záró API. További információkért lásd: [importálása és közzététele az API-k](import-and-publish.md).
* **APIM-termék** – a termék egy vagy több API-k, valamint a használati kvótát és a használati feltételeket tartalmazza. API-k számát, és a fejlesztők számára a fejlesztői portálon elérhetővé teheti őket. További információkért lásd: [létrehozása és a egy termék közzététele](api-management-howto-add-products.md).
* **APIM API-művelet** – minden APIM API fejlesztők számára elérhető műveletek készletét jelenti. Minden egyes APIM API tartalmaz egy hivatkozást a háttér-szolgáltatás, amely megvalósítja az API-t, a műveletei pedig leképezik a háttérszolgáltatás által megvalósított műveleteket. További információkért lásd: [utánzása API-válaszok](mock-api-responses.md).
* **Verzió** – néha szeretne új közzététele, vagy különböző API-funkció az egyes felhasználók, míg mások szeretné az API-t, amely számukra jelenleg működik jól. További információkért lásd: [az API több verziójának közzététele](api-management-get-started-publish-versions.md).
* **Változat** – Ha az API-ját, készen áll arra, go, és elindul, és a fejlesztők, általában ügyelni kell arra, hogy API-hoz, és akadályozza az API hívóit, nem egy időben módosításokat végez. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. További információkért lásd: [változatok használata a](api-management-get-started-revise-api.md).
* **Fejlesztői portál** – az ügyfelek (fejlesztőknek) kell használnia a fejlesztői portál az API-k eléréséhez. A fejlesztői portál testre szabható. További információkért lásd: [a fejlesztői portál testreszabása](api-management-customize-styles.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy példányt](get-started-create-service-instance.md)

