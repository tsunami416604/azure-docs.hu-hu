---
title: Felhőbeli intelligens riasztás korrelációja Azure Security Centerban (incidensek) | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan használja a Fusion a Felhőbeli intelligens riasztási korrelációt a biztonsági incidensek létrehozásához Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295864"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Felhőbeli intelligens riasztás korrelációja Azure Security Centerban (incidensek)

Security Center folyamatosan elemzi a hibrid Felhőbeli számítási feladatokat a fejlett Analitika és a fenyegetések felderítése révén, hogy riasztást küldjön a rosszindulatú tevékenységekről.

Mivel a veszélyforrások lefedettsége növekszik, és a feltört növekedésnek még a legcsekélyebb jelét is fel kell ismernie, a biztonsági elemzők a különböző riasztások osztályozása és a tényleges támadás azonosítása érdekében kihívást jelenthetnek. Security Center segít az elemzőknek megbirkózni a riasztások kimerülésével és a támadások diagnosztizálásával, ha a különböző riasztásokat és az alacsony szintű megbízhatósági jeleket a biztonsági incidensekhez társítja.

A Fusion a technológia és az analitikus háttérrendszer, amely a Security Center incidensek kiépítését teszi lehetővé, így a különböző riasztások és a kontextusbeli jelek összekapcsolhatók egymással. A Fusion úgy működik, hogy az adott előfizetésben jelentett különböző jeleket tekinti át az erőforrásokon, és olyan elterjedt mintákat talál, amelyek a támadási folyamatokat és az olyan megosztott környezetfüggő információkat mutatják be, amelyek az egyesített válasz eljárást jelzik. ezekhez készültek.

A Fusion Analytics a biztonsági tartományok ismereteit kombinálja a AI-vel a riasztások elemzéséhez, valamint az új támadási minták felfedéséhez. 

Security Center kihasználja a ferde támadási mátrixot, hogy a riasztásokat a vélt szándékkal társítsa, ami segít a formalizálása biztonsági tartomány ismeretében. Emellett a támadás egyes lépéseihez összegyűjtött információk használatával Security Center kizárni a támadás lépéseit megtekintő tevékenységeket, de nem.  

Mivel a támadások gyakran fordulnak elő különböző bérlők között, Security Center az AI-algoritmusokat kombinálva elemezheti az egyes előfizetésekben jelentett támadási sorozatot más.

Az incidensek vizsgálata során az elemzőknek gyakran további kontextusra van szükségük a fenyegetés természetével és annak enyhítésével kapcsolatos döntéshez. Például akkor is, ha hálózati rendellenesség észlelhető, anélkül, hogy meg kellene értenie, hogy mi történik a hálózaton vagy a megadott erőforrás tekintetében, nehéz megérteni, hogy milyen műveleteket kell elvégeznie a következő lépésekben. A biztonsági incidensek lehetnek például összetevők, kapcsolódó események és információk. A biztonsági incidensek számára elérhető további információk az észlelt fenyegetés típusától és a környezet konfigurációjától függően változnak. 

![Biztonsági incidens részletei](./media/security-center-alerts-cloud-smart/security-incident.png)

A biztonsági incidensek jobb megismeréséhez lásd: a [biztonsági incidensek kezelése a Azure Security Centerban](https://docs.microsoft.com/azure/security-center/security-center-incident).

