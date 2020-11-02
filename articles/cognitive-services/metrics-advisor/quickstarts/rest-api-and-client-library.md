---
title: Metrikai tanácsadó ügyféloldali kódtárak REST API
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval összekapcsolhatók az alkalmazások a metrikai Advisor API-hoz az Azure Cognitive Services-ból.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186844"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Gyors útmutató: az ügyfél-kódtárak vagy a REST API-k használata a megoldás testreszabásához

Ismerkedjen meg a metrikák Advisor REST API vagy az ügyféloldali kódtárakkal. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

A metrikai tanácsadó használata a következő műveletek végrehajtásához:

* Adatcsatorna hozzáadása adatforrásból
* Betöltési állapot keresése
* Észlelés és riasztások konfigurálása 
* Az anomáliák észlelési eredményeinek lekérdezése
* Rendellenességek diagnosztizálása


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

- [A webes portál használata](web-portal.md)
- [Adatcsatornák előkészítése](../how-tos/onboard-your-data.md)
    - [Adatcsatornák kezelése](../how-tos/manage-data-feeds.md)
    - [Különböző adatforrások beállításai](../data-feeds-from-different-sources.md)
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](../how-tos/configure-metrics.md)
- [Anomáliadetektálás beállítása visszajelzés használatával](../how-tos/anomaly-feedback.md)