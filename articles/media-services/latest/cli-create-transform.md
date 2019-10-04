---
title: Azure CLI-példaszkript – Átalakítás létrehozása | Microsoft Docs
description: Ezzel az Azure CLI-szkripttel létrehozhat egy Átalakítást.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128789"
---
# <a name="cli-example-create-a-transform"></a>CLI-példa: Átalakítás létrehozása

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre átalakítást. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek). Mindig ellenőrizze, hogy létezik-e már adott nevű és „receptű” Átalakítás. Ha igen, akkor használja újra azt.

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> A [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)-hez csak egy egyéni szabványos kódoló által előre beállított JSON-fájl elérési útját adhatja meg, lásd: [kódolás egyéni átalakítással](custom-preset-cli-howto.md) – példa.
>
> A [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)használatakor nem adhat át fájlnevet.

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>További lépések

[az AMS Transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
