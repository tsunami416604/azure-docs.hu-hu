---
title: Azure CLI-példaszkript – Objektum közzététele | Microsoft Docs
description: Az itt bemutatott Azure CLI-szkripttel közzétehet egy objektumot.
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
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: ced55a41ce5be31dfa036894188f385ae60f8962
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204154"
---
# <a name="cli-example-publish-an-asset"></a>CLI-példa: Az objektum közzététele

A jelen cikkben lévő Azure CLI-példaszkript bemutatja, hogyan hozhat létre streamelési lokátort, és hogyan kérhet le streamelési URL-címeket. 

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>További lépések

[Media Services áttekintése](media-services-overview.md)
