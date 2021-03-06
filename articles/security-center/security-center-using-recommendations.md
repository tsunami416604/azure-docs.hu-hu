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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4c6b8b0bfa78dca5ca32c8c72edcc463ebb9057a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322138"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Az Azure Security Center javaslatainak használata a biztonság növelése érdekében

A biztonsági szabályzat konfigurálásával, majd a Azure Security Center által biztosított javaslatok végrehajtásával csökkentheti a jelentős biztonsági események esélyét. Ebből a cikkből megtudhatja, hogyan használhatja a biztonsági házirendeket és javaslatokat a Security Center a biztonsági támadások enyhítése érdekében. 

Security Center automatikusan folyamatos vizsgálatokat futtat az Azure-erőforrások biztonsági állapotának elemzéséhez. Ha a Security Center felismeri a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges biztonsági vezérlők konfigurálásának folyamatán. Security Center 24 órán belül frissíti javaslatait, a következő kivételekkel:

- Az operációs rendszer biztonsági konfigurációs javaslatai 48 órán belül frissülnek
- A Endpoint Protection kapcsolatos javaslatok 8 órán belül frissülnek

## <a name="scenario"></a>Forgatókönyv
Ebből a forgatókönyvből megtudhatja, Security Center Hogyan csökkentheti a biztonsági incidensek esélyét a Security Center javaslatok figyelésével és a műveletek elvégzésével. A forgatókönyv a Security Center [tervezési és üzemeltetési útmutatóban](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)ismertetett fiktív vállalatot, contosot és szerepköröket használja. Ebben a forgatókönyvben a következő personák szerepköreire koncentrálunk:

![Forgatókönyv szerepkörei](./media/security-center-using-recommendations/scenario-roles.png)

A contoso nemrég áttelepített néhány helyszíni erőforrást az Azure-ba. A contoso szeretné megóvni erőforrásaikat, és csökkenti a felhőben lévő erőforrásaik sebezhetőségét.

## <a name="use-azure-security-center"></a>Az Azure Security Center használata
David, a contoso informatikai biztonsága már kiválasztotta a Security Center a contoso előfizetéseit, hogy Azure Security Center a biztonsági rések megelőzése és észlelése érdekében. 

Security Center automatikusan elemzi a contoso Azure-erőforrásainak biztonsági állapotát, és alkalmazza az alapértelmezett biztonsági házirendeket. Ha Security Center felismeri a lehetséges biztonsági réseket, a biztonsági házirendben beállított vezérlők alapján **javaslatokat** hoz létre. 

David az Azure Securityt az Azure Defender szolgáltatással együtt futtatja az összes előfizetésében, hogy elérhetővé tegye a rendelkezésre álló javaslatok és biztonsági funkciók teljes csomagját. Jeff az összes olyan meglévő helyszíni kiszolgálót is bevezeti, amely még nem lett áttelepítve a felhőbe, így kihasználhatja Security Center hibrid támogatását a [Windows-és Linux-kiszolgálókon](quickstart-onboard-machines.md).

Jeff egy Felhőbeli számítási feladatok tulajdonosa. Jeff feladata, hogy a contoso biztonsági házirendjeinek megfelelően alkalmazza a biztonsági ellenőrzéseket. 

Jeff a következő feladatokat hajtja végre:

- A Security Center által biztosított biztonsági javaslatok figyelése
- Értékelje ki a biztonsági javaslatokat, és döntse el, hogy kell-e alkalmaznia vagy elvetni a javaslatokat.
- Biztonsági javaslatok alkalmazása

### <a name="remediate-threats-using-recommendations"></a>Fenyegetések szervizelése javaslatok használatával
A napi megfigyelési tevékenységük részeként Jeff bejelentkezik az Azure-ba, és megnyitja Security Center. 

1. Jeff kiválasztja a munkaterhelések előfizetéseit.

2. Jeff ellenőrzi a **biztonságos pontszámot** , hogy átfogó képet kapjon arról, hogy az előfizetések hogyan biztonságosak, és hogy a pontszám 548.

3. Jeffnek el kell döntenie, hogy mely ajánlásokat kell először kezelni. Így Jeff a biztonságos pontszámra kattint, és megkezdi az ajánlások kezelését a [biztonságos pontszámának](secure-score-security-controls.md)javítása alapján.

4. Mivel Jeff számos csatlakoztatott virtuális géppel rendelkezik, Jeff úgy dönt, hogy az [eszközök leltárában](asset-inventory.md)lévő gépekre koncentrál.

5. Amikor Jeff megnyitja az eszköz leltárát, megjelenik a javaslatok listája. Jeff a biztonságos pontszám hatásának megfelelően kezeli őket.

6. Jeff számos internetkapcsolattal rendelkező virtuális gépet tartalmaz, és mivel a portok ki vannak téve, aggódnak amiatt, hogy a támadók megszerezhetik a kiszolgálók feletti irányítást. Így Jeff úgy dönt, hogy a virtuális gépek igény szerinti [**elérését**](security-center-just-in-time.md)használja.

Jeff továbbra is a magas prioritású és közepes prioritású ajánlásokon halad át, és döntéseket hoz a megvalósítással kapcsolatban. Jeff minden ajánláshoz a Security Center által biztosított részletes információkat tekinti át, hogy megtudja, mely erőforrások érintettek, milyen hatással van a biztonsági pontszámra, mit jelent az egyes javaslatok, és hogyan lehet elhárítani az egyes problémák enyhítésének lépéseit.

### <a name="enforce-recommendations-to-prevent-security-misconfigurations"></a>Javaslatok kikényszerítés a biztonsági konfigurációs problémák megelőzésére

Annak biztosítása érdekében, hogy a felhasználók ne hozzanak létre olyan erőforrásokat, amelyek negatív hatással vannak Jeff pontszámára, a leglényegesebb ajánlások érvényesítési és megtagadási beállításait konfigurálhatják. További információ a [helytelen konfigurációkkal kapcsolatos kényszerítő/megtagadási javaslatok megelőzéséről](prevent-misconfigurations.md).


## <a name="conclusion"></a>Összegzés
A Security Center figyelési javaslatai segítenek a biztonsági rések megszüntetésében a támadás előtt. A javaslatok szervizelése során a biztonságos pontszám és a számítási feladatok biztonsági helyzete javul. Security Center automatikusan felfedi a telepített új erőforrásokat, felméri azokat a biztonsági szabályzattal, és új javaslatokat biztosít számukra a biztosításához.


## <a name="next-steps"></a>Következő lépések
Győződjön meg arról, hogy van egy figyelési folyamat, amelyben rendszeresen ellenőrzi a Security Center található javaslatokat, így biztosíthatja, hogy az erőforrások az idő múlásával biztonságosak legyenek.

Ez a forgatókönyv azt mutatja be, hogyan használhatók a biztonsági szabályzatok és javaslatok a Security Centerban a biztonsági támadások enyhítése érdekében.

Ismerje meg, hogyan reagálhat a fenyegetésekre a [biztonsági riasztások kezelésével és megválaszolásával](security-center-managing-and-responding-alerts.md).
