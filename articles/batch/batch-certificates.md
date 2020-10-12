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
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146385"
---
# <a name="using-certificates-with-batch"></a>Tanúsítványok használata a Batch szolgáltatással

Jelenleg a tanúsítványok a Batch szolgáltatással való használatának fő oka, ha olyan készletekben futtatott alkalmazásokkal rendelkezik, amelyeknek egy végponttal kell hitelesíteniük magukat. 

Ha még nem rendelkezik tanúsítvánnyal, létrehozhat egy önaláírt tanúsítványt a `makecert` parancssori eszköz használatával.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Tanúsítványok manuális feltöltése a Azure Portal

1. A Batch-fiókból, amelyhez tanúsítványt szeretne feltölteni, válassza a **tanúsítványok** lehetőséget, majd válassza a **Hozzáadás**lehetőséget. 

2. Töltse fel a tanúsítványt. pfx vagy. cer kiterjesztéssel. 

A feltöltés után a tanúsítvány hozzá lesz adva a tanúsítványok listájához, és ellenőrizheti az ujjlenyomatot.

Miután létrehoz egy batch-készletet, megnyithatja a készletben lévő tanúsítványokat, és hozzárendelheti a készlethez feltöltött tanúsítványt.

## <a name="next-steps"></a>Következő lépések

A Batch tanúsítvány-API-val rendelkezik, [az batch Certificate Create](/cli/azure/batch/certificate)

További információ a Key Vault használatáról: [biztonságos hozzáférés Key Vault a Batch](credential-access-key-vault.md)szolgáltatással.
