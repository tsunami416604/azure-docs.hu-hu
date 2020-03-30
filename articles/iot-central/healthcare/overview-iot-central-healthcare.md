---
title: Egészségügyi megoldások készítése az Azure IoT Central szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egészségügyi megoldást az Azure IoT Central alkalmazássablonjaival.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021487"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Egészségügyi megoldások létrehozása az Azure IoT Centrallal 



Ismerje meg, hogyan hozhat létre egészségügyi megoldásokat az Azure IoT Central alkalmazássablonokkal.

## <a name="what-is-continuous-patient-monitoring-template"></a>Mi a folyamatos betegfelügyeleti sablon?

Az egészségügyi IoT-térben a folyamatos betegmonitorozás az egyik legfontosabb lehetővé teszi a leolvasások kockázatának csökkentését, a krónikus betegségek hatékonyabb kezelését és a betegek eredményeinek javítását. A folyamatos betegmonitorozás két fő kategóriába sorolható:

1. **Fekvőbeteg-monitorozás**: Orvosi viselhető eszközök és egyéb eszközök használata a kórházban, az ellátó csoportok figyelemmel kísérhetik a beteg életjeleit és egészségügyi feltételeit anélkül, hogy egy nővért kellene küldeniük, hogy naponta többször ellenőrizze a beteget. Az ápolási csapatok az értesítéseken keresztül megértik azt a pillanatot, amikor a betegnek kritikus figyelmet kell fordítania, és hatékonyan rangsorolja az idejét.
1. **Távoli betegmonitorozás**: Az orvosi viselhető eszközök és a betegek által jelentett eredmények (PROs) segítségével a kórházon kívüli betegek megfigyelésére csökkenthető a visszafogadás kockázata. A krónikus betegségben szenvedő betegekről és a rehabilitációs betegekről származó adatokgyűjthetők annak biztosítása érdekében, hogy a betegek betartják az ellátási terveket, és hogy a betegek romlására vonatkozó riasztások at az ellátócsoportok számára is felszínre hozhassák, mielőtt kritikussá válnak.

Ez az alkalmazássablon a folyamatos betegfigyelés mindkét kategóriájához használható megoldások készítéséhez. Ez a következő előnyöket nyújtja:

* Zökkenőmentesen csatlakoztassa a különböző típusú orvosi hordható egy IoT Central példány.
* Figyelje és kezelje az eszközöket, hogy azok kifogástalanok maradjanak.
* Hozzon létre egyéni szabályokat az eszközadatok körül a megfelelő riasztások aktiválásához.
* Exportálja a beteg állapotadatait az Azure API-ba az FHIR-hez, egy megfelelő adattárba.
* Exportálja az összesített elemzéseket meglévő vagy új üzleti alkalmazásokba.

>[!div class="mx-imgBorder"] 
>![CPM-irányítópult](media/in-patient-dashboard.png)

## <a name="next-steps"></a>További lépések

A folyamatos betegfigyelő megoldás kiépítésének megkezdéséhez:

* [Az alkalmazássablon telepítése](tutorial-continuous-patient-monitoring.md)
* [Példaarchitektúra megtekintése](concept-continuous-patient-monitoring-architecture.md)