---
title: Azure Media Services-fiók létrehozása a parancssori felület 2.0 |} Microsoft Docs
description: Hajtsa végre az Azure Media Services-fiók létrehozása a gyors üzembe helyezési lépésein.
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
ms.openlocfilehash: a9660ac61bab9f8b9eb9563aab4cc584786b25ae
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

Indítsa el a titkosítása, kódolását, elemzése, kezelése és adatfolyam-hordozó tartalmának az Azure-ban, szüksége Media Services-fiók létrehozásához. A Media Services-fiók létrehozásával egy időben létrejön egy kapcsolódó tárfiók is (illetve egy már meglévőt is használhat) ugyanabban a földrajzi régióban, ahol a Media Services-fiók is található.

Ez a témakör CLI 2.0 használatával új Azure Media Services-fiók létrehozásának lépéseit ismerteti.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be a [Azure-portálon](http://portal.azure.com) , majd indítsa el **CloudShell** végrehajtani parancssori felület parancsait, ahogy az az alábbi lépéseket.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-ás vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

A következő parancsot adja meg a Media Services-fiókhoz használni kívánt Azure előfizetés-azonosító. Az előfizetést, amely rendelkezik hozzáféréssel való listáját láthatja [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy fájlt](stream-files-dotnet-quickstart.md)
