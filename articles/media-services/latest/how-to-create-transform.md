---
title: Azure CLI-példaszkript – Átalakítás létrehozása | Microsoft Docs
description: Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek). A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre átalakítást.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ffab4eaf63844057a3872730f91634cb2df5669
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918135"
---
# <a name="create-a-transform"></a>Átalakítás létrehozása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre átalakítást. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek). Mindig ellenőrizze, hogy létezik-e már adott nevű és „receptű” Átalakítás. Ha igen, akkor használja újra azt.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services fiókot](./create-account-howto.md).

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> A [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)-hez csak egy egyéni szabványos kódoló által előre beállított JSON-fájl elérési útját adhatja meg, lásd: [kódolás egyéni átalakítással](custom-preset-cli-howto.md) – példa.
>
> A [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)használatakor nem adhat át fájlnevet.

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-transform-rest.md)]

---

## <a name="next-steps"></a>Következő lépések

[További információ az átalakításokról és a feladatokról](transforms-jobs-concept.md)
