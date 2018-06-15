---
title: Azure CLI-példaszkript – Azure Media Services-objektum létrehozása | Microsoft Docs
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre Media Services-objektumot, melybe tartalmat tud feltölteni.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: 41dfafe7ce8287d1bacac672209824c1c32067e2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161295"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI-példa: Azure Media Services-fiók létrehozása

A jelen cikkben lévő Azure CLI-példaszkript bemutatja, hogyan hozhat létre Media Services-objektumot, melybe tartalmat tud feltölteni.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
