---
title: Az Azure IoT biztonsági modul alapalapjainak fogalmi magyarázata – Azure RTOS
description: Ismerje meg az Azure RTOS IoT biztonsági moduljának fogalmait és munkafolyamatát ismertető alapismereteket.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: f4793aa27dfbb037416d6165ddc659bb864a6647
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514877"
---
# <a name="security-module-for-azure-rtos-preview"></a>Biztonsági modul az Azure RTOS (előzetes verzió)

Ebből a cikkből megismerheti az Azure RTOS biztonsági moduljának jobb megismerését, beleértve a funkciókat és az előnyöket, valamint a megfelelő konfigurációs és hivatkozási erőforrásokra mutató hivatkozásokat. 

## <a name="azure-rtos-iot-security-module"></a>Azure RTOS IoT biztonsági modul

Az Azure RTOS biztonsági modulja átfogó biztonsági megoldást nyújt az Azure RTOS-eszközökhöz a NetX Duo-ajánlat részeként. A NetX Duo ajánlatában az Azure RTOS a beépített Azure IoT biztonsági modullal rendelkezik, és az aktiválást követően a valós idejű operációs rendszer eszközeinek általános veszélyforrásait biztosítja. 

Az Azure RTOS biztonsági modulja a háttérben fut, és zökkenőmentes felhasználói élményt nyújt, miközben biztonsági üzeneteket küld a IoT Hubhoz tartozó ügyfelek egyedi kapcsolatainak használatával. Az Azure RTOS biztonsági modulja alapértelmezés szerint engedélyezve van.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Az Azure RTOS NetX Duo egy fejlett, nagyvállalati szintű TCP/IP hálózati verem, amely kifejezetten a mélyen beágyazott valós idejű és IoT alkalmazásokhoz készült. Az Azure RTOS NetX Duo egy kettős IPv4-és IPv6-alapú hálózati verem, amely a protokollok széles választékát biztosítja, beleértve a biztonságot és a felhőt is. További információ az [Azure RTOS NetX Duo](https://aka.ms/netxduo) megoldásairól.

A modul a következő funkciókat kínálja:

- **Kártékony hálózati tevékenységek észlelése**
- **Az eszköz viselkedési alapkonfigurációi az egyéni riasztások alapján**
- **Az eszköz biztonsági higiéniájának javítása**

## <a name="security-module-for-azure-rtos-architecture"></a>Biztonsági modul az Azure RTOS architektúrához

Az Azure RTOS biztonsági modulját az Azure IoT middleware-platformja inicializálja, és IoT Hub-ügyfeleket használ a biztonsági telemetria elküldésére a központba.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT biztonsági modul állapotának diagramja és információáramlás":::

Az Azure RTOS biztonsági modulja három gyűjtő használatával figyeli a következő eszköz-tevékenységeket és-információkat:
- Eszköz hálózati tevékenységének **TCP**, **UDP**és **ICM**
- Rendszerinformációk **Threadx** és **NetX Duo** verziókként
- Szívverési események

Minden gyűjtő egy prioritási csoporthoz van csatolva, és minden prioritási csoportnak saját intervalluma van, amely **alacsony**, **közepes**és **magas**lehetséges értékeket tartalmaz. Az intervallumok befolyásolják az adatok gyűjtésének és elküldéseinak időintervallumát.

Minden időintervallum konfigurálható, és a IoT-összekötők engedélyezhetők és letilthatók a megoldás további [testreszabása](how-to-azure-rtos-security-module.md)érdekében. 

## <a name="supported-security-alerts-and-recommendations"></a>Támogatott biztonsági riasztások és javaslatok

Az Azure RTOS biztonsági modulja támogatja a konkrét biztonsági riasztásokat és javaslatokat. A kezdeti konfiguráció befejezése után ellenőrizze [és szabja testre a szolgáltatás vonatkozó riasztási és javaslati értékeit](concept-rtos-security-alerts-recommendations.md) .

## <a name="ready-to-begin"></a>Készen áll a kezdésre?

Az Azure RTOS biztonsági modulja ingyenes letöltést biztosít a IoT-eszközökhöz. A IoT Cloud Service-hez készült Azure Security Center Azure-előfizetések esetén 30 napos próbaverzióval érhető el. [Töltse le most a biztonsági modult](https://github.com/azure-rtos/azure-iot-preview/releases) , és ismerkedjen meg az első lépésekkel. 

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg az Azure RTOS [előfeltételeinek és beállításának](quickstart-azure-rtos-security-module.md)biztonsági moduljának használatába.
- További információ az Azure RTOS biztonsági [riasztások és javaslatok támogatásával](concept-rtos-security-alerts-recommendations.md)kapcsolatos biztonsági modulról. 
- Használja az Azure RTOS [Reference API](azure-rtos-security-module-api.md)biztonsági modulját.

