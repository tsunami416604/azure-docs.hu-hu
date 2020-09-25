---
title: Űrlap-felismerő szolgáltatás titkosítása inaktív adatok esetén
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk a inaktív adatok titkosítását ismerteti az űrlap-felismerő esetében, valamint a CMK engedélyezésének és kezelésének módját.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: c959231826125349ecd6a62afe529248f7ac2eec
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326796"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Űrlap-felismerő titkosítása inaktív adatok esetén

Az Azure űrlap-felismerő automatikusan titkosítja az adatait a felhőben való megőrzés során. Az űrlap-felismerő titkosításával megvédheti adatait, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségeknek.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az 2020. május 11. után létrehozott erőforrások. A CMK és az űrlap-felismerő használatához létre kell hoznia egy új űrlap-felismerő erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* [Űrlap-felismerő ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)