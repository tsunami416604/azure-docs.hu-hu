---
title: Tartalommoderátor inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: Tartalommoderátor inaktív adatok titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372160"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Tartalommoderátor inaktív adatok titkosítása

A tartalommoderátor automatikusan titkosítja az adatokat, ha azok megmaradnak a felhőben, így segítve a szervezeti biztonsági és megfelelőségi célok elérését.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az E0 tarifacsomagon érhetők el. Az ügyfél által kezelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Tartalommoderátor ügyféláltal kezelt kulcskérelem-űrlapát.](https://aka.ms/cogsvc-cmk) Körülbelül 3-5 munkanapba telik, hogy meghallgassa a kérés állapotát. Az igénytől függően előfordulhat, hogy várólistába kerül, és jóváhagyják, amint rendelkezésre áll a hely. Miután jóváhagyta a CMK használatát a tartalommoderátor szolgáltatással, létre kell hoznia egy új tartalommoderátor-erőforrást, és az E0 lehetőséget kell választania tarifacsomagként. Miután létrehozta a tartalommoderátor-erőforrást az E0 tarifacsomaggal, az Azure Key Vault segítségével beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Adattitkosítás engedélyezése a tartalommoderátor-csoport számára

Ha engedélyezni szeretné az adattitkosítást a tartalommoderátor-ellenőrző csoport számára, olvassa el a [Rövid útmutató: Próbálja ki a tartalommoderátort az interneten.](quick-start.md#create-a-review-team)  

> [!NOTE]
> Meg kell adnia egy _erőforrás-azonosítót_ a Content Moderator E0 tarifacsomaghoz.


## <a name="next-steps"></a>További lépések

* [Tartalommoderátor ügyfél által kezelt kulcskérelem-űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
