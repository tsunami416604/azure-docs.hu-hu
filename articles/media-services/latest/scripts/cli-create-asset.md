---
title: Azure CLI-példaszkript – Azure Media Services-objektum létrehozása | Microsoft Docs
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre Media Services-objektumot, melybe tartalmat tud feltölteni.
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
ms.date: 10/09/2018
ms.author: juliako
ms.openlocfilehash: d2fe1718999bcdc33fcde6891fa891373a9ea5cb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069352"
---
# <a name="cli-example-create-an-asset"></a>CLI-példa: Objektum létrehozása

A jelen cikkben lévő Azure CLI-példaszkript bemutatja, hogyan hozhat létre Media Services-objektumot, melybe tartalmat tud feltölteni.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
