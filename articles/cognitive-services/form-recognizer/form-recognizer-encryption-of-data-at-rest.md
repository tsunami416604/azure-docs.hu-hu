---
title: Űrlap-felismerő szolgáltatás titkosítása inaktív adatok esetén
titleSuffix: Azure Cognitive Services
description: Az inaktív adatok titkosítása az űrlap-felismerővel.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310716"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Űrlap-felismerő titkosítása inaktív adatok esetén

Az Azure űrlap-felismerő automatikusan titkosítja az adatait, amikor a felhőben megmarad. Az űrlap-felismerő titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az 2020. május 11. után létrehozott erőforrások. A CMK és az űrlap-felismerő használatához létre kell hoznia egy új űrlap-felismerő erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>További lépések

* [Űrlap-felismerő ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


