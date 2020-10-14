---
title: Metrikai tanácsadó szolgáltatás titkosítása
titleSuffix: Azure Cognitive Services
description: A metrikai tanácsadó szolgáltatás inaktív adatok titkosítása.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 9a7a914acd1358243c1e8a29f59dadf4fac46957
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046927"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Metrikai tanácsadó szolgáltatás titkosítása inaktív adatok esetén

A metrikai tanácsadó szolgáltatás automatikusan titkosítja az adatokat, amikor azt a felhőben megőrzi. A metrikai tanácsadó szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a E0 díjszabási szinten érhetők el. Ha az ügyfél által felügyelt kulcsok használatára van szüksége, töltse ki és küldje el a [metrikák Advisor szolgáltatás Customer-Managed kulcs kérése űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK használatát a metrikák Advisor szolgáltatással, létre kell hoznia egy új metrikai Advisor-erőforrást, és ki kell választania a E0-t az árképzési szinten. Miután létrehozta a metrikák Advisor-erőforrását a E0 díjszabási szintjével, a felügyelt identitás beállításához Azure Key Vault is használhatja.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* [Metrikai tanácsadó szolgáltatás Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
