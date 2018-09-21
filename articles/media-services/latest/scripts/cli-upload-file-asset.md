---
title: Azure CLI-példaszkript – Fájl feltöltése egy tárolóba | Microsoft Docs
description: Ezzel az Azure CLI-szkripttel feltölthet egy helyi fájlt egy tárfiókbeli tárolóba.
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
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 61a5a59336878976a20a6ffd7b4e4d6d27061453
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35763345"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI-példa: Helyi fájl feltöltése egy tárolóba 

A cikkben bemutatott Azure CLI-szkripttel feltölthet egy helyi fájlt egy tárfiókbeli tárolóba.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
