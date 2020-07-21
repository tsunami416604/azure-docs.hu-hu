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
ms.openlocfilehash: b897de94ad8cdb628520f9386b076f762a5cc230
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537952"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Űrlap-felismerő titkosítása inaktív adatok esetén

Az Azure űrlap-felismerő automatikusan titkosítja az adatait, amikor a felhőben megmarad. Az űrlap-felismerő titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az 2020. május 11. után létrehozott erőforrások. A CMK és az űrlap-felismerő használatához létre kell hoznia egy új űrlap-felismerő erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* [Űrlap-felismerő ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


