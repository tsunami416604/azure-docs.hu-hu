---
title: Az inaktív adatok személyre szabása szolgáltatástitkosítás
titleSuffix: Azure Cognitive Services
description: Az inaktív adatok személyre szabása szolgáltatástitkosítás.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071088"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Az inaktív adatok személyre szabása szolgáltatástitkosítás

A Personalizer szolgáltatás automatikusan titkosítja az adatokat, amikor a felhőben megőrzött. A Personalizer szolgáltatás titkosítása védi az adatokat, és segít önnek a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az E0 tarifacsomagon érhetők el. Az ügyfél által felügyelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Personalizer Service ügyfélszolgálatáltal felügyelt kulcskérelem-űrlapát.](https://aka.ms/cogsvc-cmk) Körülbelül 3-5 munkanapba telik, hogy meghallgassa a kérés állapotát. Az igénytől függően előfordulhat, hogy várólistába kerül, és jóváhagyják, amint rendelkezésre áll a hely. Miután jóváhagyta a CMK használatát a Personalizer szolgáltatással, létre kell hoznia egy új Personalizer erőforrást, és az E0-t kell választania tarifacsomagként. Miután létrehozta a Personalizer erőforrást az E0 tarifacsomaggal, az Azure Key Vault segítségével beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>További lépések

* [A Personalizer Service ügyfélszolgálatáltal kezelt kulcskérelem-űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
