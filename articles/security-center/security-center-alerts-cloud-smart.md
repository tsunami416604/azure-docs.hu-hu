---
title: Incidensek Azure Security Center – riasztások intelligens korrelációja
description: Ez a témakör azt ismerteti, hogyan használja a Fusion a felhőalapú intelligens riasztások korrelációját a Azure Security Center biztonsági incidensek létrehozásához.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 742d7122c60b383e8ab7aa73f73b11b47843a9d5
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613822"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Felhőbeli intelligens riasztás korrelációja Azure Security Centerban (incidensek)

Azure Security Center folyamatosan elemzi a hibrid Felhőbeli számítási feladatokat a fejlett Analitika és a veszélyforrások felderítésével, hogy riasztást küldjön a rosszindulatú tevékenységekről.

A veszélyforrások lefedettségének szélessége növekszik. Fontos, hogy a rendszer még a legcsekélyebb kompromisszumot is felderítse, és kihívást jelenthet a biztonsági elemzők számára a különböző riasztások osztályozása és a tényleges támadás azonosítása érdekében. Security Center segít az elemzőknek megbirkózni ezzel a riasztással. Segít diagnosztizálni a támadásokat, és a különböző riasztásokat és az alacsony megbízhatósági jeleket a biztonsági incidensekre kell összeegyeztetni.

A Fusion Analytics a technológia és az analitikus háttérrendszer, amely a Security Center incidensek kiépítésére szolgál, lehetővé téve a különböző riasztások és környezetfüggő jelek összekapcsolását. A Fusion az adott előfizetésben jelentett különböző jeleket tekinti át az erőforrásokon. A Fusion megkeresi azokat a mintákat, amelyek a támadási folyamatokat vagy a közös kontextussal kapcsolatos jeleket mutatják, és azt jelzi, hogy egyesített válasz eljárást kell használni számukra.

A Fusion Analytics a biztonsági tartományok ismereteit ötvözi a AI-vel a riasztások elemzéséhez, és felfedi az új támadási mintákat. 

Security Center kihasználja a ferde támadási mátrixot, hogy a riasztásokat a vélt szándékkal társítsa, ami segít a formalizálása biztonsági tartomány ismeretében. Emellett a támadás egyes lépéseihez összegyűjtött információk használatával Security Center kizárni a támadás lépéseit megtekintő tevékenységeket, de valójában nem.

Mivel a támadások gyakran fordulnak elő a különböző bérlők között, a Security Center az egyes előfizetésekben jelentett támadási folyamatok elemzéséhez az AI-algoritmusokat is kombinálhatja. Ezzel a technikával azonosíthatja a támadási folyamatokat az elterjedt riasztási mintázatként, ahelyett, hogy az egymáshoz kapcsolható.

Az incidensek vizsgálata során az elemzőknek gyakran további kontextusra van szükségük a fenyegetés természetével és annak enyhítésével kapcsolatos döntéshez. Például akkor is, ha hálózati rendellenesség észlelhető, anélkül, hogy meg kellene értenie, hogy mi történik a hálózaton vagy a megadott erőforrás tekintetében, nehéz megérteni, hogy milyen műveleteket kell végrehajtani a következő lépésekben. A biztonsági incidensek lehetnek például összetevők, kapcsolódó események és információk. A biztonsági incidensek számára elérhető további információk az észlelt fenyegetés típusától és a környezet konfigurációjától függően változnak. 

> [!TIP]
> A Fusion Analytics által előállítható biztonsági incidensekről szóló riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-fusion)tekintheti meg.

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Képernyőkép a biztonsági incidensek által észlelt jelentésről":::


A biztonsági incidensek jobb megismeréséhez lásd: a [biztonsági incidensek kezelése a Azure Security Centerban](security-center-incident.md).

