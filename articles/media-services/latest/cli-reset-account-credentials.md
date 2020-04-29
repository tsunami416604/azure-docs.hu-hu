---
title: Azure CLI-példaszkript – Fiók hitelesítő adatainak alaphelyzetbe állítása | Microsoft Docs
description: Ezzel az Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382987"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI-példa: a fiók hitelesítő adatainak alaphelyzetbe állítása

A cikkben bemutatott Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>További lépések

* [az AMS](/cli/azure/ams)
* [Hitelesítő adatok visszaállítása](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
