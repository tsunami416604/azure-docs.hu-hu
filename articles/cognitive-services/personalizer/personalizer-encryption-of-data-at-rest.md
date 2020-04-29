---
title: A REST-adatok személyre szabására szolgáló szolgáltatás titkosítása
titleSuffix: Azure Cognitive Services
description: Személyre szabhatja az inaktív adatok titkosítását.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071088"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>A REST-adatok személyre szabására szolgáló szolgáltatás titkosítása

A személyre szabott szolgáltatás automatikusan titkosítja az adatait, amikor azt a felhőben megőrzi. A személyre szabott szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a E0 díjszabási szinten érhetők el. Az ügyfél által felügyelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [személyre szabott szolgáltatás ügyfél által felügyelt kulcs kérése űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK használatát a személyre szabott szolgáltatással, létre kell hoznia egy új személyre szabott erőforrást, és ki kell választania a E0 elemet az árképzési szinten. Miután létrehozta a személyre szabott erőforrást a E0 díjszabási szintjével, a felügyelt identitás beállításához Azure Key Vaultt használhat.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>További lépések

* [Személyre szabott szolgáltatás ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
