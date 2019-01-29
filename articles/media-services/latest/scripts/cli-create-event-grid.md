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
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098905"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI-példa: Az Azure Event Grid-előfizetés létrehozása 

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre fiókszintű Event Grid-előfizetést a feladatok állapotváltozásaihoz.

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
