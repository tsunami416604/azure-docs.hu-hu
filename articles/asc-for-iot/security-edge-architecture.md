---
title: A IoT Edge Preview IoT biztonsági moduljának Azure Security Center ismertetése | Microsoft Docs
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
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596855"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge biztonsági modul

> [!IMPORTANT]
> A IoT Edge IoT szolgáltatásának Azure Security Center jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

  További információért lásd: [Azure Security Center konfigurálása a IoT](how-to-agent-configuration.md) -ügynökhöz.

A IoT Edge IoT biztonsági moduljának Azure Security Center a IoT Edge alatt a privilegizált módban futnak.
A Kiemelt üzemmód szükséges ahhoz, hogy a modul figyelje az operációs rendszert és az egyéb IoT Edge modulokat.

## <a name="module-supported-platforms"></a>Modul által támogatott platformok

A IoT Edge IoT biztonsági moduljának Azure Security Center jelenleg csak Linux rendszeren érhető el. 

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, milyen architektúrát és képességeket Azure Security Center a IoT Edge IoT biztonsági moduljának.

A IoT-telepítés Azure Security Centerának folytatásához használja a következő cikkeket:

- [IoT Edge biztonsági moduljának](how-to-deploy-edge.md) telepítése
- Útmutató [a biztonsági modul konfigurálásához](how-to-agent-configuration.md)
- A IoT [szolgáltatás](service-prerequisites.md) előfeltételeinek áttekintése Azure Security Center
- Megtudhatja, hogyan [engedélyezheti Azure Security Center a IoT szolgáltatáshoz a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Azure Security Center IoT – gyakori kérdések](resources-frequently-asked-questions.md)