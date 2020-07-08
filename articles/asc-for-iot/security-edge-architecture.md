---
title: Biztonsági modul a IoT Edge
description: Ismerje meg IoT Edge IoT biztonsági moduljának architektúráját és képességeit Azure Security Center.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310628"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge biztonsági modul

A [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) hatékony képességeket biztosít az üzleti munkafolyamatok kezeléséhez és az Edge-ben való végrehajtásához.
A IoT-környezetekben IoT Edge játszik, különösen vonzó a kártékony szereplők számára.

A IoT biztonsági modul Azure Security Center átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A IoT modul Azure Security Center a nyers biztonsági adatokat gyűjti, összesíti és elemzi az operációs rendszertől és a tároló rendszertől a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.

A IoT-eszközökhöz készült IoT biztonsági ügynökökhöz Azure Security Center hasonlóan a IoT Edge modulhoz tartozó Azure Security Center nagyméretűen testreszabható a modul Twin-en keresztül.
További információért lásd: [az ügynök konfigurálása](how-to-agent-configuration.md) .

A IoT Edge IoT biztonsági moduljának Azure Security Center a következő funkciókat kínálja:

- Az alapul szolgáló operációs rendszer (Linux) és a IoT Edge tároló rendszerek nyers biztonsági eseményeit gyűjti.

  A rendelkezésre álló biztonsági adatgyűjtők megismeréséhez tekintse meg [Azure Security Center a IoT-ügynök konfigurációját](how-to-agent-configuration.md) ismertető témakört.

- IoT Edge üzembe helyezési jegyzékek elemzése.

- Az [IoT Edge hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)-on keresztül küldött üzenetekben összesíti a nyers biztonsági eseményeket.

- Távolítsa el a konfigurációt a Twin biztonsági modul használatával.

  További információért lásd: [Azure Security Center konfigurálása a IoT-ügynökhöz](how-to-agent-configuration.md) .

A IoT Edge IoT biztonsági moduljának Azure Security Center a IoT Edge alatt a privilegizált módban futnak.
A Kiemelt üzemmód szükséges ahhoz, hogy a modul figyelje az operációs rendszert és az egyéb IoT Edge modulokat.

## <a name="module-supported-platforms"></a>Modul által támogatott platformok

A IoT Edge IoT biztonsági moduljának Azure Security Center jelenleg csak Linux rendszeren érhető el.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, milyen architektúrát és képességeket Azure Security Center a IoT Edge IoT biztonsági moduljának.

A IoT-telepítés Azure Security Centerának folytatásához használja a következő cikkeket:

- [IoT Edge biztonsági moduljának](how-to-deploy-edge.md) telepítése
- Útmutató [a biztonsági modul konfigurálásához](how-to-agent-configuration.md)
- A IoT [szolgáltatás előfeltételeinek](service-prerequisites.md) áttekintése Azure Security Center
- Megtudhatja, hogyan [engedélyezheti Azure Security Center a IoT szolgáltatáshoz a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Azure Security Center IoT – gyakori kérdések](resources-frequently-asked-questions.md)
