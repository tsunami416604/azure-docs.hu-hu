---
title: Az inaktív adatok arc-szolgáltatásbeli titkosítása
titleSuffix: Azure Cognitive Services
description: Az inaktív adatok arc-szolgáltatásbeli titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201947"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Az inaktív adatok arc-szolgáltatásbeli titkosítása

A Face szolgáltatás automatikusan titkosítja az adatait, amikor azt a felhőben megőrzi. Az arc-szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a E0 díjszabási szinten érhetők el. Ha az ügyfél által felügyelt kulcsok használatára van szüksége, töltse ki és küldje el az [ügyfél által felügyelt kulcs kérése űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK és a Face szolgáltatás használatát, létre kell hoznia egy új arc-erőforrást, és a E0 kell választania az árképzési szinten. Miután létrehozta az arc-erőforrást a E0 díjszabási szintjével, a felügyelt identitás beállításához Azure Key Vault is használhatja.

### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok jelenleg ezekben a régiókban érhetők el:

* USA déli középső régiója
* USA nyugati régiója, 2.
* USA keleti régiója
* USA-beli államigazgatás – Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Következő lépések

* [Face Service – ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


