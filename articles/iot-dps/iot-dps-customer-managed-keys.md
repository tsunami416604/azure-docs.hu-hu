---
title: Az Azure-eszközkiépítési szolgáltatás adattitkosítása inaktív ügyfél által kezelt kulcsokon keresztül| Microsoft dokumentumok
description: Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal az eszközkiépítési szolgáltatáshoz
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675142"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal az eszközkiépítési szolgáltatáshoz

## <a name="overview"></a>Áttekintés

Az eszközkiépítési szolgáltatás (DPS) támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven a saját kulcs (BYOK) használatával. A DPS biztosítja az adatok titkosítását az inaktív és az átvitel során. Alapértelmezés szerint a DPS a Microsoft által felügyelt kulcsokat használja az adatok titkosításához. A CMK-támogatással az ügyfelek mostantól az Ügyfelek key encryption kulccsal titkosítják az [inaktív](https://azure.microsoft.com/services/key-vault/)adatokat az Azure Key Vault használatával.

Ehhez a képességhez új DPS-t kell létrehozni a következő régiók egyikében: USA keleti régió, USA nyugati régió 2 vagy USA déli középső régiója. A funkció kipróbálásához lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg cégnevét és előfizetési azonosítóját, amikor kapcsolatba lép a Microsoft támogatási szolgálatával.

## <a name="next-steps"></a>További lépések

* [További információ az eszközkiépítési szolgáltatásról](https://docs.microsoft.com/azure/iot-dps/)

* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)