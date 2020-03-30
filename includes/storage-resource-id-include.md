---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249916"
---
Az Azure AD-erőforrás-azonosító azt a közönséget jelzi, amelyhez egy jogkivonatot ki lehet használni egy Azure-erőforráshoz való hozzáférés biztosításához. Az Azure Storage esetében az erőforrás-azonosító egyetlen tárfiókra jellemző lehet, vagy bármely tárfiókra vonatkozhat. Az alábbi táblázat az erőforrás-azonosítóhoz megadható értékeket ismerteti:

|Erőforrás-azonosító  |Leírás  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Egy adott tárfiók szolgáltatásvégpontja. Ezzel az értékkel jogkivonatot szerezhet be az adott Azure Storage-fiókra és szolgáltatásra vonatkozó kérelmek engedélyezéséhez. Cserélje le a zárójelben lévő értéket a tárfiók nevére.      |
|`https://storage.azure.com/`     | Segítségével beszerezhet egy jogkivonatot bármely Azure Storage-fiók kérelmek engedélyezéséhez.        |
