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
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91262450"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Gyors útmutató: Cognitive Services-erőforrás létrehozása az Azure felügyeleti ügyféloldali kódtár használatával

Ezzel a rövid útmutatóval Azure Cognitive Services-erőforrásokat hozhat létre és kezelhet az Azure felügyeleti ügyféloldali kódtár használatával.

Az Azure Cognitive Services a REST API-kkal rendelkező felhőalapú szolgáltatások és az ügyféloldali kódtár SDK-k, amelyek segítségével a fejlesztők kognitív intelligenciát hozhatnak létre az alkalmazásokban anélkül, hogy közvetlen mesterséges intelligencia (AI) vagy adattudományi ismereteket vagy ismereteiket kellene létrehozniuk. Az Azure Cognitive Services lehetővé teszi, hogy a fejlesztők könnyen hozzá tudják adni a kognitív funkciókat az alkalmazásaikban olyan kognitív megoldásokkal, amelyek megtekinthetik, meghallgatják, megértették, megértik, sőt, akár

Az egyes AI-szolgáltatásokat az Azure-előfizetésében létrehozott Azure- [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) jelölik. Miután létrehozta az erőforrást, használhatja a generált kulcsokat és végpontokat az alkalmazások hitelesítéséhez.

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
