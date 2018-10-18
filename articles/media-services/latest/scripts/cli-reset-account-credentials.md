---
title: Azure CLI-példaszkript – Fiók hitelesítő adatainak alaphelyzetbe állítása | Microsoft Docs
description: Ezzel az Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.
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
ms.openlocfilehash: f97cf87288f5efbeef11a27e98ab3fc2caf27b40
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376413"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI-példa: Fiók hitelesítő adatainak alaphelyzet állítása

A cikkben bemutatott Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
