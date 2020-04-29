---
title: Azure CLI-példaszkript – Átalakítás létrehozása | Microsoft Docs
description: Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek). A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre átalakítást.
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
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74888615"
---
# <a name="cli-example-create-a-transform"></a>CLI-példa: Átalakítás létrehozása

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre átalakítást. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek). Mindig ellenőrizze, hogy létezik-e már adott nevű és „receptű” Átalakítás. Ha igen, akkor használja újra azt.

## <a name="prerequisites"></a>Előfeltételek 

[Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> A [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)-hez csak egy egyéni szabványos kódoló által előre beállított JSON-fájl elérési útját adhatja meg, lásd: [kódolás egyéni átalakítással](custom-preset-cli-howto.md) – példa.
>
> A [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)használatakor nem adhat át fájlnevet.

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>További lépések

[az AMS Transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
