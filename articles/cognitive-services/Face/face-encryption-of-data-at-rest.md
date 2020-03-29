---
title: Az adatok arcszolgáltatás-titkosítása
titleSuffix: Azure Cognitive Services
description: Az adatok szolgáltatástitkosításának face-in-t.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372215"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Az adatok arcszolgáltatás-titkosítása

A Face szolgáltatás automatikusan titkosítja az adatokat, amikor a felhőben megőrzött. A Face szolgáltatás titkosítása védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az E0 tarifacsomagon érhetők el. Az ügyfél által kezelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Face Service ügyfélszolgálati ügyféláltal felügyelt kulcskérelem-űrlapot.](https://aka.ms/cogsvc-cmk) Körülbelül 3-5 munkanapba telik, hogy meghallgassa a kérés állapotát. Az igénytől függően előfordulhat, hogy várólistába kerül, és jóváhagyják, amint rendelkezésre áll a hely. Miután jóváhagyták a CMK és a Face szolgáltatás használatát, létre kell hoznia egy új Face erőforrást, és az E0-t kell választania tarifacsomagként. Miután a Face-erőforrás az E0 tarifacsomag létrehozása, az Azure Key Vault segítségével beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>További lépések

* [A Face Service ügyfélszolgálatáltal felügyelt kulcskérelem-űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


