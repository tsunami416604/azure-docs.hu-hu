---
title: Az eszközök internetes hálózatát (IoT) az Azure-ban Secure |} A Microsoft Docs
description: " Azure eszközök internetes hálózatán (IoT) szolgáltatások a képességek széles választékát nyújtják. Ez a cikk segítségével megismerheti, hogyan védheti meg az IoT-megoldások az Azure-ban. "
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
ms.openlocfilehash: 41e422d0808cafb45b182c5f0a6bb7176a35516f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758620"
---
# <a name="internet-of-things-security-overview"></a>IOT-biztonság – áttekintés
Azure eszközök internetes hálózatán (IoT) szolgáltatások a képességek széles választékát nyújtják. Ezekkel a vállalati szintű szolgáltatásokkal a következőket teheti:

* Adatok gyűjtése eszközökről
* Streamek elemzése mozgás közben
* Nagy adatkészletek tárolása és lekérdezése
* Valós idejű és előzményadatok megjelenítése
* Integrálás irodai háttérrendszerekkel

Kínált ezeknek képességeket, az Azure IoT-megoldás megoldásgyorsítók csomag össze több Azure-szolgáltatást kínál egyéni bővítményekkel előre konfigurált megoldásokat. Ezek az előre konfigurált megoldások az általános IoT -megoldásminták alapszintű megvalósításai, amelyek segítenek csökkenteni az IoT-megoldások szolgáltatásához szükséges időt. Használja az IoT szoftverfejlesztői készletekkel, testreszabhatja és kibővítheti ezeket a megoldásokat, a saját követelményeinek. Ezeket a megoldásokat példaként vagy sablonként is használhatja az új IoT-megoldások fejlesztésekor.

Az Azure IoT-megoldásgyorsítók azok hatékony megoldások az IoT-igényeinek. Azonban upmost fontos, hogy az IoT-megoldások a biztonságot szem a kezdetektől úgy tervezték. IoT-eszközök puszta száma miatt biztonsági incidensek gyorsan válhat egy széles körű esemény jelentős következményeit.

Megismerheti, hogyan védheti meg az IoT-megoldások érdekében van a következő információkat.

## <a name="security-architecture"></a>Biztonsági architektúra
A rendszer tervezésekor fontos, hogy a rendszer a potenciális fenyegetések, és megfelelő védelmet ennek megfelelően felvenni, mert a rendszer lett kialakítva, és lett tervezve. Fontos a kezdetektől a termék tervezhet a biztonságot szem, mivel egy támadó hogyan lehet tudni veszélyeztetheti a rendszer segítségével, győződjön meg arról, hogy megfelelő megoldások tudnivalók a rendszer az elejétől.

Című témakörben talál további IoT biztonsági architektúra olvasásával [Internet a dolgok biztonsági architektúra](/azure/iot-fundamentals/iot-security-architecture).

Ez a cikk ismerteti a következő témaköröket:

* [A fenyegetések modellezése biztonsági kezdődik](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Az IoT biztonsági](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Az Azure IoT-Referenciaarchitektúra modellezési fenyegetés](/azure/iot-fundamentals/iot-security-architecture#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Biztonság az alapoktól
Az IoT-és vállalkozások világszerte egyedi biztonsági, adatvédelmi és megfelelőségi kihívásokat jelent. Ellentétben a hagyományos kibertámadások technológia, ahol a ezeket a problémákat a szoftvert, és hogyan valósul köré szerveződik IoT vonatkozik, mi történik, ha az internetes és a fizikai világ lesz. Biztonságos kiépítés eszközök, ezek az eszközök és a felhőben és a biztonságos adatvédelem a felhőben feldolgozásra és tárolásra során közötti biztonságos kapcsolatot biztosító IoT-megoldások védelme szükséges. Azonban működik a funkció, olyan eszközök erőforrás korlátozott, központi telepítések földrajzi eloszlása és sok eszköz egy megoldáson belül.

Azt is megtudhatja, hogyan olvassa el a következő területeken biztonsági kezelésére [IOT-biztonság az alapoktól](/azure/iot-fundamentals/iot-security-ground-up).

A cikk ismerteti a következő témaköröket:

* [Biztonságos infrastruktúra létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [A Microsoft Azure – biztonságos IoT-infrastruktúrát a vállalkozása számára](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Ajánlott eljárások
A szigorú biztonsági jellegű stratégia biztonságossá tétele az IoT-infrastruktúrát igényel. Az adatok védelme a felhőben, a nyilvános interneten, eszközök, biztonságos kiépítés keresztül védi az adatok integritásának megőrzése, az átvitel során az egyes rétegek nagyobb biztonság az infrastruktúra átfogó épít fel.

Című témakörben talál további IOT-biztonság ajánlott eljárások olvasásával [biztonsági eljárások az IOT-](/azure/iot-fundamentals/iot-security-best-practices).

A cikk ismerteti a következő témaköröket:

* [IoT hardver gyártója/rendszerintegrátor](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT-megoldás fejlesztői](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT-megoldás deployer](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [IoT-megoldás operátor](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
