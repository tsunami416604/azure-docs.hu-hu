---
title: A REST-adatok személyre szabására szolgáló szolgáltatás titkosítása
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk a személyre szabott adatok titkosítását, valamint a CMK engedélyezését és kezelését ismerteti.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a19f0a204bec1c0a43a84d93c2dc4b70ef6ecbe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069908"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>A REST-adatok személyre szabására szolgáló szolgáltatás titkosítása

A személyre szabott szolgáltatás automatikusan titkosítja az adatait, amikor azt a felhőben megőrzi. A személyre szabott szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a E0 díjszabási szinten érhetők el. Ha az ügyfél által felügyelt kulcsok használatát szeretné kérni, töltse ki és küldje el a [személyre szabott szolgáltatás Customer-Managed kulcs kérése űrlapját](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK használatát a személyre szabott szolgáltatással, létre kell hoznia egy új személyre szabott erőforrást, és ki kell választania a E0 elemet az árképzési szinten. Miután létrehozta a személyre szabott erőforrást a E0 díjszabási szintjével, a felügyelt identitás beállításához Azure Key Vaultt használhat.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* [Személyre szabott szolgáltatás Customer-Managed Key kérelem űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
