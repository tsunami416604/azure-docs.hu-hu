---
title: Tartalom feltöltése egy Azure Media Services eszközre az Azure CLI használatával
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre Media Services-objektumot, melybe tartalmat tud feltölteni.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b362ba30baf13211dc41a6043dcedd05bd506ea
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585414"
---
# <a name="create-an-asset"></a>Adategység létrehozása

Ez a cikk bemutatja, hogyan hozhat létre Media Services adategységet.  Egy adategységet fog használni a médiatartalom kódolásához és adatfolyamként való tárolásához.  Ha többet szeretne megtudni Media Services eszközökről, olvassa el az adategységeket [Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Előfeltételek

Egy eszköz létrehozásához kövesse az [Media Services fiók létrehozása](./create-account-howto.md) a szükséges Media Services fiók és erőforráscsoport létrehozásához című témakör lépéseit.

## <a name="methods"></a>Metódusok

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[CLI-rendszerhéj](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

---

## <a name="next-steps"></a>További lépések

[Eszközök kezelése](manage-asset-concept.md)
