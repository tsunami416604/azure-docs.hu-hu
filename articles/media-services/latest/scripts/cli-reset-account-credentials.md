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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 1c70441ea5b35a55ba39f934e74a6512d783fcf0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615387"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI-példa: Fiók hitelesítő adatainak alaphelyzet állítása

A cikkben bemutatott Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.

## <a name="prerequisites"></a>Előfeltételek 

- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.

- [A Media Services-fiók létrehozása](../create-account-cli-how-to.md).

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
