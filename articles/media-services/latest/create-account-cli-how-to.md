---
title: Media Services-fiók létrehozása az Azure CLI használatával – Azure |} A Microsoft Docs
description: Ezen rövid útmutató lépéseivel egy Azure Media Services-fiókot hozhat létre.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: b01958dc320ff6c81bb27c85ff4a5e508eabb01b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209944"
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

Indítsa el a titkosítása, kódolás, elemzése, kezelése és médiafolyam az Azure-ban, szüksége a Media Services-fiók létrehozása. A időpontban hoz létre egy Media Services-fiók, emellett létrehoz egy társított storage-fiók (vagy használjon egy meglévőt).  

> [!NOTE]
> A Media Services-fiók és a társított tárfiókok az Azure-előfizetéshez kell lennie. Javasoljuk, hogy a Media Services-fiók ugyanazon a helyen a storage-fiókok használatához.

Ez a cikk ismerteti az Azure CLI használatával új Azure Media Services-fiók létrehozásának lépései.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

