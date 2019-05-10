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
ms.openlocfilehash: 86f455f25bb41833774bb7ad7d81688aad812b5a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236818"
---
# <a name="cli-example-create-a-transform"></a>CLI-példa: Hozzon létre egy-egy átalakítási

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre átalakítást. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek). Mindig ellenőrizze, hogy létezik-e már adott nevű és „receptű” Átalakítás. Ha igen, akkor használja újra azt.

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>További lépések

[Media Services áttekintése](media-services-overview.md)
