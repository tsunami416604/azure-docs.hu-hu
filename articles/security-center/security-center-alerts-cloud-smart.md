---
title: Az Azure Security Center-incidensek – a riasztások intelligens korrelációi
description: Ez a témakör bemutatja, hogyan használja a fusion a felhőalapú intelligens riasztáskorrelációt az Azure Security Center biztonsági incidensek létrehozásához.
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
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686486"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Felhőalapú intelligens riasztások korrelációja az Azure Security Centerben (incidensek)

Az Azure Security Center folyamatosan elemzi a hibrid felhőbeli számítási feladatokat fejlett elemzési és fenyegetési intelligencia használatával, hogy figyelmeztesse Önt a rosszindulatú tevékenységekre.

A fenyegetésterjedés terjedelme egyre nő. Fontos, hogy még a legkisebb kompromisszumot is észlelni kell, és a biztonsági elemzők számára kihívást jelenthet a különböző riasztások osztályozása és a tényleges támadás azonosítása. A Security Center segít az elemzőknek megbirkózni ezzel a riasztási fáradtsággal. Segít diagnosztizálni a támadásokat, ahogy azok bekövetkeznek, a különböző riasztások és az alacsony hűségű jelek biztonsági incidensekre való összevetésével.

A fusion analytics az a technológia és analitikus háttérrendszer, amely a Security Center-incidenseket működteti, lehetővé téve a különböző riasztások és kontextuális jelek összevetését. Fusion megvizsgálja a különböző jelentett jeleket egy előfizetés az erőforrások között. A Fusion olyan mintákat talál, amelyek a támadás előrehaladását vagy a megosztott környezetfüggő információkat tartalmazó jeleket tárnak fel, jelezve, hogy egységes válaszeljárást kell alkalmazni hozzájuk.

A fusion analytics egyesíti a biztonsági tartománytudást a mi-vel a riasztások elemzéséhez, az új támadási minták észleléséhez. 

A Security Center a MITRE Attack Matrix segítségével társítja a riasztásokat az észlelt szándékkal, és ezzel formálissá teszi a biztonsági tartományokkal kapcsolatos ismereteket. Ezenkívül a támadás minden egyes lépéséhez összegyűjtött információk használatával a Security Center kizárhatja a támadás lépéseinek tűnő tevékenységet, de valójában nem az.

Mivel a támadások gyakran különböző bérlők között történnek, a Security Center kombinálhatja az AI-algoritmusokat az egyes előfizetéseken jelentett támadási sorozatok elemzéséhez. Ez a módszer azonosítja a támadási szekvenciák, mint elterjedt riasztási minták, ahelyett, hogy véletlenül egymáshoz kapcsolódó.

Egy incidens kivizsgálása során az elemzőknek gyakran további kontextusra van szükségük ahhoz, hogy ítéletet hozzanak a fenyegetés jellegéről és enyhítéséről. Például még akkor is, ha egy hálózati anomália észlelése, anélkül, hogy megértése, mi történik a hálózaton, vagy tekintettel a célzott erőforrás, nehéz megérteni, hogy milyen műveleteket kell tennie a következő. A biztonsági incidensek segíthetnek összetevőket, kapcsolódó eseményeket és információkat tartalmazhatnak. A biztonsági incidensekhez rendelkezésre álló további információk az észlelt veszély típusától és a környezet konfigurációjától függően változnak. 

![Képernyőkép a biztonsági incidens észlelési jelentéséről](./media/security-center-alerts-cloud-smart/security-incident.png)

A biztonsági incidensek jobb megértéséhez olvassa el [A biztonsági incidensek kezelése az Azure Security Centerben.](https://docs.microsoft.com/azure/security-center/security-center-incident)

