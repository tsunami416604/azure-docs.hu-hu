---
title: Cognitive Services erőforrás létrehozása az Azure felügyeleti ügyféloldali kódtár használatával
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services-erőforrások létrehozása és kezelése az Azure felügyeleti ügyféloldali kódtár használatával.
services: cognitive-services
keywords: kognitív szolgáltatások, kognitív intelligencia, kognitív megoldások, AI-szolgáltatások
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 1fd748a0184c1718ac9450aaca3e2db1a185051a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368950"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Gyors útmutató: Cognitive Services-erőforrás létrehozása az Azure felügyeleti ügyféloldali kódtár használatával

Ezzel a rövid útmutatóval Azure Cognitive Services-erőforrásokat hozhat létre és kezelhet az Azure felügyeleti ügyféloldali kódtár használatával.

Az Azure Cognitive Services a REST API-kkal rendelkező felhőalapú szolgáltatások és az ügyféloldali kódtár SDK-k, amelyek segítségével a fejlesztők kognitív intelligenciát hozhatnak létre az alkalmazásokban anélkül, hogy közvetlen mesterséges intelligencia (AI) vagy adattudományi ismereteket vagy ismereteiket kellene létrehozniuk. Az Azure Cognitive Services lehetővé teszi, hogy a fejlesztők könnyen hozzá tudják adni a kognitív funkciókat az alkalmazásaikban olyan kognitív megoldásokkal, amelyek megtekinthetik, meghallgatják, megértették, megértik, sőt, akár

Az egyes AI-szolgáltatásokat az Azure-előfizetésében létrehozott Azure- [erőforrások](../azure-resource-manager/management/manage-resources-portal.md) jelölik. Miután létrehozta az erőforrást, használhatja a generált kulcsokat és végpontokat az alkalmazások hitelesítéséhez.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end