---
title: Tanúsítványok használata – Azure Batch
description: Tanúsítványok használata az alkalmazások hitelesítésének engedélyezéséhez
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764337"
---
# <a name="using-certificates-with-batch"></a>Tanúsítványok használata a Batch használatával

Jelenleg a tanúsítványok a Batch szolgáltatással való használatának fő oka, ha olyan készletekben futtatott alkalmazásokkal rendelkezik, amelyeknek egy végponttal kell hitelesíteniük magukat. 

Ha még nem rendelkezik tanúsítvánnyal, létrehozhat egy önaláírt tanúsítványt a `makecert` parancssori eszköz használatával.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Tanúsítványok manuális feltöltése a Azure Portal

1. A Batch-fiókból, amelyhez tanúsítványt szeretne feltölteni, válassza a **tanúsítványok** lehetőséget, majd válassza a **Hozzáadás**lehetőséget. 

2. Töltse fel a tanúsítványt. pfx vagy. cer kiterjesztéssel. 

A feltöltés után a tanúsítvány hozzá lesz adva a tanúsítványok listájához, és ellenőrizheti az ujjlenyomatot.

Miután létrehoz egy batch-készletet, megnyithatja a készletben lévő tanúsítványokat, és hozzárendelheti a készlethez feltöltött tanúsítványt.

## <a name="next-steps"></a>További lépések

A Batch tanúsítvány-API-val rendelkezik, [az batch Certificate Create](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

További információ a Key Vault használatáról: [biztonságos hozzáférés Key Vault a Batch](credential-access-key-vault.md)szolgáltatással.
