---
title: Az Azure IoT Hub adattitkosítása inaktív ügyfél által felügyelt kulcsokon keresztül| Microsoft dokumentumok
description: Az adatok titkosítása az IoT Hub ügyfél által felügyelt kulcsaival
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370576"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Az adatok titkosítása az IoT Hub ügyfél által felügyelt kulcsaival

Az IoT Hub támogatja az adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven a saját kulcs (BYOK) használatával, az Azure IoT Hub támogatása. Az Azure IoT Hub biztosítja az adatok titkosítását az inaktív és az átvitel során. Alapértelmezés szerint az IoT Hub microsoft által felügyelt kulcsokat használ az adatok titkosításához. A CMK-támogatással az ügyfelek mostantól az Ügyfelek key encryption kulccsal titkosítják az [inaktív](https://azure.microsoft.com/services/key-vault/)adatokat az Azure Key Vault használatával.

Ehhez a képességhez új IoT-központ (alap- vagy standard szint) létrehozása szükséges a következő régiók egyikében: USA keleti régió, USA nyugati régiója 2, USA déli középső régiója vagy usa gov. A funkció kipróbálásához lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg cégnevét és előfizetési azonosítóját, amikor kapcsolatba lép a Microsoft támogatási szolgálatával.

## <a name="next-steps"></a>További lépések

* [További információ az IoT Hubról](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [További információ az Azure Key Vaultról](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
