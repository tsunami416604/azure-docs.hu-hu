---
title: Az Azure Device kiépítési szolgáltatás adattitkosítása az ügyfelek által felügyelt kulcsok használatával | Microsoft Docs
description: Az ügyfél által felügyelt kulcsok használata a Device kiépítési szolgáltatáshoz – az inaktív adatok titkosítása
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77675142"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Az ügyfél által felügyelt kulcsok használata a Device kiépítési szolgáltatáshoz – az inaktív adatok titkosítása

## <a name="overview"></a>Áttekintés

A Device kiépítési szolgáltatás (DPS) támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven a saját kulcs használata (BYOK). A DPS inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé. Alapértelmezés szerint a DPS a Microsoft által felügyelt kulcsokat használja az adattitkosításhoz. A CMK-támogatással az ügyfelek mostantól a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)használatával titkosítják az inaktív adatok titkosítását az ügyfelek által felügyelt kulcs titkosítási kulccsal.

Ehhez a képességhez új DPS-t kell létrehozni a következő régiók egyikében: USA keleti régiója, USA 2. nyugati régiója vagy az USA déli középső régiója. Ha szeretné kipróbálni ezt a funkciót, lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg vállalata nevét és előfizetési AZONOSÍTÓját, amikor kapcsolatba lép a Microsoft ügyfélszolgálatával.

## <a name="next-steps"></a>További lépések

* [További információ az eszköz kiépítési szolgáltatásáról](https://docs.microsoft.com/azure/iot-dps/)

* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)