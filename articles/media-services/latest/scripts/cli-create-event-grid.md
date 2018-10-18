---
title: Azure CLI-példaszkript – Azure Event Grid-előfizetés létrehozása | Microsoft Docs
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre fiókszintű Event Grid-előfizetést a feladatok állapotváltozásaihoz.
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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: eae9947b7dcbd6f2c52fb0d4abc65375aed7766e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378788"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI-példa: Azure Event Grid-előfizetés létrehozása 

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre fiókszintű Event Grid-előfizetést a feladatok állapotváltozásaihoz.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
