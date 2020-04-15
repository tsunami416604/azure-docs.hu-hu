---
title: Az IoT Edge biztonsági modulja
description: Ismerje meg az Azure Security Center for IoT biztonsági modul architektúráját és képességeit az IoT Edge-hez.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310628"
---
# <a name="azure-iot-edge-security-module"></a>Az Azure IoT Edge biztonsági modulja

[Az Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) hatékony lehetőségeket biztosít az üzleti munkafolyamatok kezeléséhez és végrehajtásához a peremhálózaton.
Az IoT Edge kulcsfontosságú szerepe az IoT-környezetekben különösen vonzóvá teszi a rosszindulatú szereplők számára.

Az Azure Security Center for IoT biztonsági modul átfogó biztonsági megoldást nyújt az IoT Edge-eszközökhöz.
Az Azure Security Center for IoT-modul az operációs rendszerből és a tárolórendszerből származó nyers biztonsági adatokat művelete alapján megvalósítható biztonsági javaslatokés riasztások alapján gyűjti, összesíti és elemzi.

Az IoT-eszközökhöz készült Azure Security Center for IoT biztonsági ügynökökhöz hasonlóan az Azure Security Center for IoT Edge modul nagymértékben testreszabható a modul ikermoduljával.
További információ az [ügynök konfigurálása](how-to-agent-configuration.md) című témakörben olvashat bővebben.

Az IoT Edge-hez készült Azure Security Center for IoT biztonsági modul a következő funkciókat kínálja:

- Nyers biztonsági események gyűjtése az alapul szolgáló operációs rendszer (Linux) és az IoT Edge tárolórendszerek.

  Tekintse meg [az Azure Security Center for IoT ügynök konfigurációját,](how-to-agent-configuration.md) ha többet szeretne megtudni az elérhető biztonsági adatgyűjtőkről.

- IoT Edge-telepítés jegyzékfájljainak elemzése.

- A nyers biztonsági eseményeket az [IoT Edge Hubon](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)keresztül küldött üzenetekbe összesíti.

- A konfiguráció eltávolítása az ikerbiztonsági modul használatával.

  További információ az [Azure Security Center ioT-ügynökhöz beállítása](how-to-agent-configuration.md) című témakörben.

Az IoT Edge-hez készült Azure Security Center for IoT Security Module egy kiváltságos módban fut ioT Edge alatt.
Kiemelt mód szükséges ahhoz, hogy a modul figyelhesse az operációs rendszert és más IoT Edge-modulokat.

## <a name="module-supported-platforms"></a>Modul által támogatott platformok

Az IoT Edge-hez készült Azure Security Center for IoT Security Module jelenleg csak Linuxon érhető el.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Security Center for IoT biztonsági modul IoT Edge architektúrájáról és képességeiről szerzett tudomást.

Az Azure Security Center iot-telepítéshez való használatának megkezdéséhez használja az alábbi cikkeket:

- Biztonsági modul üzembe helyezése [az IoT Edge-hez](how-to-deploy-edge.md)
- A [biztonsági modul konfigurálása](how-to-agent-configuration.md)
- Tekintse át az Azure Security Center az [IoT-szolgáltatás előfeltételeit](service-prerequisites.md)
- Megtudhatja, hogyan engedélyezheti az [Azure Security Center for IoT-szolgáltatást az IoT Hubban](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról az [Azure Security Center for IoT –GYIK-ből](resources-frequently-asked-questions.md)
