---
title: Az Azure IoT Hub az adatok titkosítását az ügyfelek által felügyelt kulcsok használatával | Microsoft Docs
description: Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370576"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub

IoT Hub támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven saját kulcs használata (BYOK), az Azure IoT Hub támogatása. Az Azure IoT Hub inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé. Alapértelmezés szerint a IoT Hub a Microsoft által felügyelt kulcsokat használja az adattitkosításhoz. A CMK-támogatással az ügyfelek mostantól a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)használatával titkosítják az inaktív adatok titkosítását az ügyfelek által felügyelt kulcs titkosítási kulccsal.

Ehhez a funkcióhoz új IoT Hub (alapszintű vagy standard szintű csomag) létrehozására van szükség az alábbi régiók egyikében: USA keleti régiója, USA 2. nyugati régiója, USA déli középső régiója vagy US Gov. Ha szeretné kipróbálni ezt a funkciót, lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg vállalata nevét és előfizetési AZONOSÍTÓját, amikor kapcsolatba lép a Microsoft ügyfélszolgálatával.

## <a name="next-steps"></a>Következő lépések

* [További információ a IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
