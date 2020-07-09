---
title: Custom Vision inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: Custom Vision inaktív adatok titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310291"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision inaktív adatok titkosítása

Az Azure Custom Vision automatikusan titkosítja az adatait, amikor a felhőben megmaradt. Custom Vision titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az 2020. május 11. után létrehozott erőforrások. Ahhoz, hogy a CMK a Custom Vision használatával használhassa, létre kell hoznia egy új Custom Vision-erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok jelenleg ezekben a régiókban érhetők el:

* USA déli középső régiója
* USA nyugati régiója, 2.
* USA keleti régiója
* USA-beli államigazgatás – Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>További lépések

* A CMK támogató szolgáltatások teljes listájáért lásd: [ügyfél által felügyelt kulcsok Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
