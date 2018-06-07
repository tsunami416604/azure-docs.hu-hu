---
title: Az eszközök internetes hálózata (IoT) az Azure-ban biztonságos |} Microsoft Docs
description: " Azure internetes hálózata (IoT) szolgáltatások széles skálájával képességeket kínálnak. Ez a cikk segít megérteni a biztonságossá tétele az IoT-megoldások Azure-ban. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d5c1cb22fdfe59bd8409f9595b2fa4c3a0df771e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641237"
---
# <a name="internet-of-things-security-overview"></a>Az eszközök internetes hálózatát biztonsági áttekintése
Azure internetes hálózata (IoT) szolgáltatások széles skálájával képességeket kínálnak. Ezekkel a vállalati szintű szolgáltatásokkal a következőket teheti:

* Adatok gyűjtése eszközökről
* Streamek elemzése mozgás közben
* Nagy adatkészletek tárolása és lekérdezése
* Valós idejű és előzményadatok megjelenítése
* Integrálás irodai háttérrendszerekkel

Ezen képességek, Azure IoT-megoldás gyorsítók csomag együtt több Azure szolgáltatások nyújtásához az egyéni kiterjesztéseket tartalmazó mint előkonfigurált megoldásokat. Ezek az előre konfigurált megoldások az általános IoT -megoldásminták alapszintű megvalósításai, amelyek segítenek csökkenteni az IoT-megoldások szolgáltatásához szükséges időt. Az IoT szoftverfejlesztői készletek használ, testre szabhatja és ezek a megoldások a saját követelményeinek megfelelően bővítheti. Ezeket a megoldásokat példaként vagy sablonként is használhatja az új IoT-megoldások fejlesztésekor.

Az Azure IoT-megoldás gyorsítók az IoT-igényeinek hatékony megoldások. Célszerű upmost fontos, hogy az IoT-megoldások a biztonságot szem előtt tartva a kezdetektől készültek. Az IoT-eszközök puszta száma miatt a biztonsági incidens vehet a széles körű esemény jelentős következményekkel jár.

A következő információkat kell megtudhatja, hogy az IoT-megoldások biztonságossá tétele érdekében.

## <a name="security-architecture"></a>Biztonsági architektúra
A rendszer tervezésekor fontos megérteni, hogy a rendszer potenciális fenyegetések, majd a megfelelő védelmekkel ennek megfelelően adja meg, a rendszer tervezett és tervezett. Fontos tervezési kezdetétől a termék a biztonságot szem előtt tartva, mert ismertetése, hogy egy támadó lehet a rendszerbe segítségével győződjön meg arról, hogy megfelelő megoldást legyenek érvényben az elejétől.

Megismerheti az IoT-biztonsági architektúrája olvasásával [Internet a dolgok biztonsági architektúrája](../iot-accelerators/iot-security-architecture.md).

Ez a cikk ismerteti, amelyek a következő témaköröket:

* [A fenyegetések modellezése biztonsági kezdődik](../iot-accelerators/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Az IoT biztonsági](../iot-accelerators/iot-security-architecture.md#security-in-iot)
* [A Azure IoT-Referenciaarchitektúra modellezési fenyegetés](../iot-accelerators/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Biztonság az alapoktól
Az IoT működő vállalkozásoknak világszerte egyedi biztonsági, adatvédelmi és megfelelőségi kihívást jelent. Hagyományos számítógépes technológia, ahol a problémák szoftver, és hogyan megvalósított köré szerveződik, eltérően IoT vonatkozik, mi történik, ha a számítógépes és a fizikai világot átszervezését. Az IoT-megoldások védelmét biztosítja az eszközöket, ezek az eszközök és a felhőben és a feldolgozás és a tárolás során a felhőben biztonságos adatvédelem közötti biztonságos kapcsolat biztonságos kiépítése igényel. Eszközök fejlesztésének ilyen funkció, azonban a következők: erőforrás-korlátozott eszközök, központi telepítések földrajzi eloszlása és megoldást belül számos eszközön.

Megismerheti a biztonsági évi kezelésére olvasásával [az eszközök internetes hálózatát biztonsági másolatot az alapoktól](../iot-accelerators/securing-iot-ground-up.md).

A cikk ismerteti a következő témaköröket:

* [Biztonságos infrastruktúra alapoktól](../iot-accelerators/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [A Microsoft Azure – a vállalati biztonságos IoT-infrastruktúra](../iot-accelerators/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Ajánlott eljárások
Az IoT-infrastruktúra védelmének biztosítása megköveteli a szigorú jellegű biztonsági stratégia. Az adatok védelme az a felhőben, az átvitel alatt adatintegritás védelme a nyilvános interneten keresztül, a biztonságos kiépítésére az eszközök, minden egyes réteg épít, nagyobb biztonság a teljes infrastruktúrában.

Megismerheti az eszközök internetes hálózatát biztonsági gyakorlati tanácsok olvasásával [az eszközök internetes hálózatát ajánlott biztonsági eljárások](../iot-accelerators/iot-security-best-practices.md).

A cikk ismerteti a következő témaköröket:

* [Az IoT hardver gyártója/integráló](../iot-accelerators/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Az IoT-megoldás fejlesztői](../iot-accelerators/iot-security-best-practices.md#iot-solution-developer)
* [Az IoT-megoldás deployer](../iot-accelerators/iot-security-best-practices.md#iot-solution-deployer)
* [Az IoT-megoldás operátor](../iot-accelerators/iot-security-best-practices.md#iot-solution-operator)
