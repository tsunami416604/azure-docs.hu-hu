---
title: A eszközök internetes hálózata (IoT) védelme az Azure-ban | Microsoft Docs
description: " Az Azure Internet of Things (IoT) szolgáltatásai széles körű képességeket kínálnak. Ebből a cikkből megtudhatja, hogyan védheti meg IoT-megoldásait az Azure-ban. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 670fcc5e92cdb9937026d91ee85b7e8e856a3025
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727036"
---
# <a name="internet-of-things-security-overview"></a>eszközök internetes hálózata biztonsági áttekintés
Az Azure Internet of Things (IoT) szolgáltatásai széles körű képességeket kínálnak. Ezekkel a vállalati szintű szolgáltatásokkal a következőket teheti:

* Adatok gyűjtése eszközökről
* Streamek elemzése mozgás közben
* Nagy adatkészletek tárolása és lekérdezése
* Valós idejű és előzményadatok megjelenítése
* Integrálás irodai háttérrendszerekkel

Ezeknek a képességeknek a biztosításához az Azure IoT megoldás-gyorsítók együtt több Azure-szolgáltatást is biztosítanak, amelyek egyéni bővítményekkel rendelkeznek előre konfigurált megoldásként. Ezek az előre konfigurált megoldások az általános IoT -megoldásminták alapszintű megvalósításai, amelyek segítenek csökkenteni az IoT-megoldások szolgáltatásához szükséges időt. A IoT szoftverfejlesztői készletekkel testreszabhatja és kiterjesztheti ezeket a megoldásokat, hogy megfeleljenek a saját követelményeinek. Ezeket a megoldásokat példaként vagy sablonként is használhatja az új IoT-megoldások fejlesztésekor.

Az Azure IoT megoldás-gyorsítók hatékony megoldásokat biztosítanak a IoT igényeihez. Azonban ez a legfontosabb szempont, hogy a IoT-megoldások az indítástól fogva biztonsági szempontból vannak kialakítva. A IoT-eszközök puszta száma miatt a biztonsági incidensek gyorsan, jelentős következményekkel járhatnak el széles körben.

A következő információk segítségével megismerheti, hogyan védheti meg IoT-megoldásait.

## <a name="security-architecture"></a>Biztonsági architektúra
A rendszer tervezésekor fontos megérteni a rendszer potenciális veszélyforrásait, és ennek megfelelően hozzá kell adnia a megfelelő védelmi lehetőségeket, ahogy a rendszert tervezték és felépíteni. Fontos, hogy megtervezze a terméket a kezdetektől a biztonsággal szem előtt tartva, mert megértette, hogy a támadók hogyan veszélyeztethetik a rendszereket, így biztosítható, hogy a megfelelő enyhítések a kezdetektől fogva megtörténjenek.

A IoT biztonsági architektúra megismeréséhez olvassa el [eszközök internetes hálózata biztonsági architektúrát](/azure/iot-fundamentals/iot-security-architecture).

Ez a cikk a következő témákat tárgyalja:

* [A biztonság a veszélyforrások modelljével kezdődik](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Biztonság a IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [A fenyegetés modellezése az Azure IoT-referenciájának architektúrája](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Biztonság az alapoktól
A IoT egyedi biztonsági, adatvédelmi és megfelelőségi kihívásokat jelent a vállalkozások számára világszerte. A hagyományos számítógépes technológiától eltérően, ahol ezek a problémák a szoftvereket és azok megvalósítását tárgyalják, IoT, hogy mi történik, ha a Cyber és a fizikai világ konvergál. A IoT-megoldások védelme megköveteli az eszközök biztonságos kihelyezését, az eszközök és a felhő közötti biztonságos csatlakozást, valamint a Felhőbeli adatvédelem védelmét a feldolgozás és a tárolás során. Az ilyen funkciókkal való munka azonban erőforrás-korlátozott eszközök, központi telepítések földrajzi eloszlása és számos eszköz a megoldáson belül.

Megtudhatja, hogyan kezelheti ezekben a területeken a biztonságot, ha [az alapoktól eszközök internetes hálózata biztonsági mentést](/azure/iot-fundamentals/iot-security-ground-up)végez.

A cikk a következő témákat tárgyalja:

* [Biztonságos infrastruktúra az alapoktól](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – biztonságos IoT infrastruktúra a vállalat számára](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Ajánlott eljárások
A IoT-infrastruktúra biztonságossá tételéhez szigorú, részletes biztonsági stratégiát kell megadnia. A felhőben tárolt adatok biztonsága, az adatok integritásának védelme a nyilvános interneten való átvitel során, az eszközök biztonságos kiépítéséhez az egyes rétegek nagyobb biztonságot garantálnak a teljes infrastruktúrában.

Az eszközök internetes hálózata biztonsági eljárásairól az [ajánlott biztonsági eljárások eszközök internetes hálózata](/azure/iot-fundamentals/iot-security-best-practices)olvashatók.

A cikk a következő témákat tárgyalja:

* [IoT hardver gyártója/integrátora](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT-megoldás fejlesztője](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT-megoldás telepítője](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [IoT-megoldás operátora](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
