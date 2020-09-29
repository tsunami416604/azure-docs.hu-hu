---
title: Azure Defender IoT-hez
description: Tudnivalók a IoT készült Azure Defender szolgáltatásról
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448350"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Bevezetés az Azure Defender for IoT használatába

Egyesítheti a biztonsági felügyeletet, és engedélyezheti a teljes körű fenyegetések észlelését és elemzését a hibrid Felhőbeli számítási feladatokban és az Azure IoT-megoldásban.

A IoT készült Azure Defender a következőket biztosítja:

- Az eszközök **felderítése és hálózati leképezése**, beleértve az eszköz gyártóját, az eszköz típusát, valamint azt, hogy az eszközök hogyan kommunikáljanak a hálózaton
- **Sebezhetőségek kezelése**, beleértve a CVEs, a nyitott portokra és a jogosulatlan internetkapcsolatokra vonatkozó információkat
- **Folyamatos fenyegetés-figyelés**, valós idejű riasztások, amelyek rendellenes vagy jogosulatlan tevékenységeket jeleznek, például a támadásokat vagy a kártevőket

A részletek a [dedikált dokumentációban](https://docs.microsoft.com/azure/asc-for-iot/overview)olvashatók.

## <a name="availability"></a>Rendelkezésre állás
|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Azure Defender](security-center-pricing.md) szükséges|
|Szükséges szerepkörök és engedélyek:|Írási engedélyek a gép NSG|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Milyen eszközöket használhatnak az Azure Defender a IoT biztonsága érdekében?
A Security Center, az Azure Defender és az CyberX ügynök nélküli technológia funkcióinak kombinálásával a következőket biztosíthatja:

- IoT Hubon keresztül csatlakoztatott **zöldmezős eszközök** . Ez lehetővé teszi a szervezetek számára, hogy felgyorsítsák a IoT-kezdeményezéseket a modern, az internetes natív eszközök és a hagyományos ICS/SCADA eszközök biztonságossá tételével egyetlen egységes megoldással.
    - Új eszközök beléptetése és biztonsági szabályzatok alkalmazása a számítási feladatokban (Leaf-eszközök, Microsoft Edge-eszközök, IoT Hub) a biztonsági szabványok és a jobb biztonsági helyzetek megfelelőségének biztosítása érdekében.

- Az operatív technológiai (OT) környezetekben használt nem **felügyelt rozsdaövezetek rehabilitálása-eszközök** , mint például a gyártás, az Épületfelügyeleti rendszerek (BMS), az élettudományok, az energia és a víz segédprogramok, az olaj-& gáz és a logisztika. 
    - A nem felügyelt eszközök elleni védelem érdekében az Azure Defender for IoT egy helyszíni érzékelőt használ a passzív, nem invazív hálózati forgalom elemzéséhez (NTA), amely zéró teljesítményre hatással van az OT-környezetekre. 
    - Emellett részletesen megismerheti a speciális IoT/OT-protokollokat – a szabadalmaztatott, IoT/OT-Aware viselkedés-elemzéssel és gépi tanulással együtt –, így nincs szükség szabályok vagy aláírások konfigurálására. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender IoT-integráció az Azure Sentinelrel
Az egységesített IT/biztonsági monitorozás és irányítás lehetővé tételéhez az Azure Defender for IoT natív módon integrálható az [Azure Sentinel](../sentinel/overview.md)szolgáltatással.

A SecOps csapatok a következőket tehetik:

- Az IoT/OT-fenyegetések gyors észlelése és elhárítása a Sentinel-specifikus SZÁRNYALó forgatókönyvekkel
- Az 52-es szakasz, a Microsoft belső IoT/OT fenyegető intelligencia csapata által biztosított, folyamatosan frissített IoT/OT-specifikus fenyegetések felderítése
- Az Azure Defender integrálása meglévő SOC-munkafolyamatokkal és harmadik féltől származó biztonsági eszközökkel, például a splunk, az IBM QRadar és a ServiceNow-mel, IoT


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan IoT az Azure Defender a Azure Security Centerban. További információkért lásd:

- [Az Azure Security Center for IoT bemutatása](../asc-for-iot/overview.md)
