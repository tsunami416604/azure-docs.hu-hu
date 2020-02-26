---
title: Azure Security Center javaslatok használata a biztonság növelése érdekében | Microsoft Docs
description: " Megtudhatja, hogyan használhatók a biztonsági szabályzatok és javaslatok a Azure Security Centerban a biztonsági támadások enyhítése érdekében. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603279"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center javaslatok használata a biztonság növelése érdekében
A biztonsági szabályzat konfigurálásával, majd a Azure Security Center által biztosított javaslatok végrehajtásával csökkentheti a jelentős biztonsági események esélyét. Ebből a cikkből megtudhatja, hogyan használhatja a biztonsági házirendeket és javaslatokat a Security Center a biztonsági támadások enyhítése érdekében. 

Security Center automatikusan folyamatos vizsgálatokat futtat az Azure-erőforrások biztonsági állapotának elemzéséhez. Ha a Security Center felismeri a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges biztonsági vezérlők konfigurálásának folyamatán. Security Center 24 órán belül frissíti javaslatait, a következő kivételekkel:

- Az operációs rendszer biztonsági konfigurációs javaslatai 48 órán belül frissülnek
- A Endpoint Protection kapcsolatos javaslatok 8 órán belül frissülnek

## <a name="scenario"></a>Forgatókönyv
Ebből a forgatókönyvből megtudhatja, Security Center Hogyan csökkentheti a biztonsági incidensek esélyét a Security Center javaslatok figyelésével és a műveletek elvégzésével. A forgatókönyv a Security Center [tervezési és üzemeltetési útmutatóban](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)ismertetett fiktív vállalatot, contosot és szerepköröket használja. Ebben a forgatókönyvben a következő personák szerepköreire koncentrálunk:

![Forgatókönyv szerepkörei](./media/security-center-using-recommendations/scenario-roles.png)

A contoso nemrég áttelepített néhány helyszíni erőforrást az Azure-ba. A contoso szeretné megóvni erőforrásaikat, és csökkenti a felhőben lévő erőforrásaik sebezhetőségét.

## <a name="use-azure-security-center"></a>Azure Security Center használata
David, a contoso informatikai biztonsága már kiválasztotta a Security Center a contoso előfizetéseit, hogy Azure Security Center a biztonsági rések megelőzése és észlelése érdekében. 

Security Center automatikusan elemzi a contoso Azure-erőforrásainak biztonsági állapotát, és alkalmazza az alapértelmezett biztonsági házirendeket. Ha Security Center felismeri a lehetséges biztonsági réseket, a biztonsági házirendben beállított vezérlők alapján **javaslatokat** hoz létre. 

David az Azure Security Standard szintű csomagot futtatja az összes előfizetésében, hogy megkapja a rendelkezésre álló javaslatok és biztonsági funkciók teljes csomagját. Jeff az összes olyan meglévő helyszíni kiszolgálót is bevezeti, amely még nem lett áttelepítve a felhőbe, így kihasználhatja Security Center hibrid támogatását a [Windows](quick-onboard-windows-computer.md) -és [Linux](quick-onboard-linux-computer.md) -kiszolgálókon.

Jeff egy Felhőbeli számítási feladatok tulajdonosa. Jeff feladata, hogy a contoso biztonsági házirendjeinek megfelelően alkalmazza a biztonsági ellenőrzéseket. 

Jeff a következő feladatokat hajtja végre:

- A Security Center által biztosított biztonsági javaslatok figyelése
- Értékelje ki a biztonsági javaslatokat, és döntse el, hogy kell-e alkalmaznia vagy elvetni a javaslatokat.
- Biztonsági javaslatok alkalmazása

### <a name="remediate-threats-using-recommendations"></a>Fenyegetések szervizelése javaslatok használatával
A napi megfigyelési tevékenységük részeként Jeff bejelentkezik az Azure-ba, és megnyitja Security Center. 

1. Jeff kiválasztja a munkaterhelések előfizetéseit.

2. Jeff ellenőrzi a **biztonságos pontszámot** , hogy átfogó képet kapjon arról, hogy az előfizetések hogyan biztonságosak, és hogy a pontszám 548.

3. Jeffnek el kell döntenie, hogy mely ajánlásokat kell először kezelni. Így Jeff a biztonságos pontszámra kattint, és megkezdi a javaslatok kezelését annak alapján, hogy mennyire javítja a [biztonságos pontszám hatását](security-center-secure-score.md).

4. Mivel Jeff számos csatlakoztatott virtuális géppel és kiszolgálóval rendelkezik, Jeff úgy dönt, hogy a **számítási és az alkalmazásokra**koncentrál.

5. Amikor Jeff a **számítás és az alkalmazások**elemre kattint, megtekinti a javaslatok listáját, és a biztonságos pontszám hatásának megfelelően kezeli őket.

6. Jeff számos internetkapcsolattal rendelkező virtuális gépet tartalmaz, és mivel a portok ki vannak téve, aggódnak amiatt, hogy a támadók megszerezhetik a kiszolgálók feletti irányítást. Így Jeff úgy dönt, hogy a virtuális gépek igény szerinti [**elérését**](security-center-just-in-time.md)használja.

Jeff továbbra is a magas prioritású és közepes prioritású ajánlásokon halad át, és döntéseket hoz a megvalósítással kapcsolatban. Jeff minden ajánláshoz a Security Center által biztosított részletes információkat tekinti át, hogy megtudja, mely erőforrások érintettek, milyen hatással van a biztonsági pontszámra, mit jelent az egyes javaslatok, és hogyan lehet elhárítani az egyes problémák enyhítésének lépéseit.

## <a name="conclusion"></a>Összegzés
A Security Center figyelési javaslatai segítenek a biztonsági rések megszüntetésében a támadás előtt. A javaslatok szervizelése során a biztonságos pontszám és a számítási feladatok biztonsági helyzete javul. Security Center automatikusan felfedi a telepített új erőforrásokat, felméri azokat a biztonsági szabályzattal, és új javaslatokat biztosít számukra a biztosításához.


## <a name="next-steps"></a>Következő lépések
Győződjön meg arról, hogy van egy figyelési folyamat, amelyben rendszeresen ellenőrzi a Security Center található javaslatokat, így biztosíthatja, hogy az erőforrások az idő múlásával biztonságosak legyenek.

Ez a forgatókönyv azt mutatja be, hogyan használhatók a biztonsági szabályzatok és javaslatok a Security Centerban a biztonsági támadások enyhítése érdekében.

Ismerje meg, hogyan reagálhat a fenyegetésekre a [biztonsági riasztások kezelésével és megválaszolásával](security-center-managing-and-responding-alerts.md).
