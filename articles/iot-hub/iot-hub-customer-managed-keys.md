---
title: Az Azure IoT Hub az adatok titkosítását az ügyfelek által felügyelt kulcsok használatával | Microsoft Docs
description: Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976574"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal IoT Hub

IoT Hub támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven saját kulcs használata (BYOK). Az Azure IoT Hub inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé az adatközpontokban, és a szolgáltatáshoz való hozzáférés során visszafejti azokat. Alapértelmezés szerint a IoT Hub a Microsoft által felügyelt kulcsokat használja az adatok titkosításához a nyugalmi állapotban. A CMK-mel egy másik titkosítási réteget is megadhat az alapértelmezett titkosításhoz, és a [Azure Key Vaulton](https://azure.microsoft.com/services/key-vault/)keresztül felügyelhető kulcs titkosítási kulccsal titkosíthatja az inaktív adatok mennyiségét. Ez rugalmasságot biztosít a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Ha a BYOK konfigurálva van a IoT Hubhoz, kettős titkosítást is biztosítunk, amely egy második védelmi réteget biztosít, miközben lehetővé teszi a titkosítási kulcs vezérlését a Azure Key Vaulton keresztül.

Ehhez a funkcióhoz új IoT Hub létrehozása szükséges (alapszintű vagy standard csomag). Ha szeretné kipróbálni ezt a funkciót, lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg vállalata nevét és előfizetési AZONOSÍTÓját, amikor kapcsolatba lép a Microsoft ügyfélszolgálatával.


## <a name="next-steps"></a>További lépések

* [További információ a IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [További információ a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
