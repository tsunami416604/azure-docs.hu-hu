---
title: Azure CLI-példaszkript – Feladat létrehozása és elküldése | Microsoft Docs
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan tud feladatot küldeni egy egyszerű kódoló Átalakításnak HTTPS URL-cím használatával.
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
ms.openlocfilehash: 0b11d095e1c4a5aa75936c0437cbeb5c65550937
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094059"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI-példa: Feladat létrehozása és elküldése

A jelen cikkben lévő Azure CLI-szkript bemutatja, hogyan tud feladatot létrehozni és elküldeni egy egyszerű kódoló Átalakításnak HTTPS URL-cím használatával.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-jobs/Create-Jobs.sh "Create and submit jobs")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
