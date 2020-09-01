---
title: Azure CLI-példaszkript – Objektum közzététele | Microsoft Docs
description: Ez a cikk bemutatja, hogyan tehet közzé egy eszközt az Azure CLI-szkript használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 44fbbbdc3662c53736071ce039b1576bf79e5c0d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269760"
---
# <a name="cli-example-publish-an-asset"></a>CLI-példa: Objektum közzététele

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A jelen cikkben lévő Azure CLI-példaszkript bemutatja, hogyan hozhat létre streamelési lokátort, és hogyan kérhet le streamelési URL-címeket. 

## <a name="prerequisites"></a>Előfeltételek 

[Hozzon létre egy Media Services fiókot](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>További lépések

[Media Services áttekintése](media-services-overview.md)
