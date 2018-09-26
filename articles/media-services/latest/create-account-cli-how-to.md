---
title: Az Azure Media Services-fiók létrehozása az Azure CLI-vel |} A Microsoft Docs
description: Ezen rövid útmutató lépéseivel egy Azure Media Services-fiókot hozhat létre.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: ca01f32709ce7c9fc49629415cd8697a9d9ba43a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091949"
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

Indítsa el a titkosítása, kódolás, elemzése, kezelése és médiafolyam az Azure-ban, szüksége a Media Services-fiók létrehozása. A Media Services-fiók létrehozásával egy időben létrejön egy kapcsolódó tárfiók is (illetve egy már meglévőt is használhat) ugyanabban a földrajzi régióban, ahol a Media Services-fiók is található.

Ez a témakör ismerteti az Azure CLI használatával új Azure Media Services-fiók létrehozásának lépései.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure Portalra](http://portal.azure.com), és indítsa el a **CloudShell** szolgáltatást a parancssori felületi parancsok végrehajtásához, ahogy az az alábbi lépésekben látható.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-ás vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
