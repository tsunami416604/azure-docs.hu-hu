---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 kiadási megjegyzések| Microsoft dokumentumok
description: Az 1.2-es frissítést futtató StorSimple virtuális tömb kritikus nyitott problémáit és megoldásait ismerteti.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420603"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 kiadási megjegyzések

A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtual array frissítések megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek. Ahogy ismertté válnak a megoldást igénylő kritikus fontosságú problémák, hozzáadjuk őket a dokumentumhoz. A StorSimple virtuális tömb telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

Az 1.2-es frissítés a **10.0.10311.0**szoftververziónak felel meg.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt ad. A frissítés telepítéséhez használt csomagokra vonatkozó részletes utasításokért látogasson el az [1.2-es frissítés letöltése című fájlba.](#download-update-12)
> - Az 1.2-es frissítés csak akkor érhető el az Azure Portalon keresztül, ha az eszközén az 1.0-s vagy 1.1-es frissítés fut.

## <a name="whats-new-in-update-12"></a>Az 1.2-es frissítés újdonságai

A frissítés a következő hibajavításokat tartalmazza:

- Továbbfejlesztett rugalmasság a törölt fájlok feldolgozásakor.
- Továbbfejlesztett kezelési kivételek a kód indítási útvonalában, ami a biztonsági mentések, a visszaállítás, a felhőolvasások és az automatikus térvisszanyerés csökkentett hibáihoz vezet.

## <a name="download-update-12"></a>1.2-es frissítés letöltése

A frissítés letöltéséhez nyissa meg a [Microsoft Update katalóguskiszolgálót,](https://www.catalog.update.microsoft.com/Home.aspx) és töltse le a KB4502035 kb4502035 kb. Ez a csomag a következő csomagokat tartalmazza:

 - **KB4493446,** amely 2019 áprilisáig tartalmazza a 2012 R2 összesítő Windows-frissítéseit. Ha többet szeretne tudni arról, hogy mi szerepel ebben az összesítésben, látogasson el az [áprilisi havi biztonsági összesítésre.](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)
 - **KB3011067,** amely a Microsoft Update önálló csomagfájlJa: WindowsTH-KB3011067-x64. Ez a fájl az eszköz szoftverének frissítésére szolgál.

Töltse le a KB4502035-öt, és kövesse az alábbi utasításokat [A frissítés alkalmazása a helyi webes felhasználói felületen](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)keresztül .

## <a name="issues-fixed-in-update-12"></a>Az 1.2-es frissítésben javított problémák

Az alábbi táblázat az ebben a kiadásban javított problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Törlés| A szoftver korábbi verzióiban volt egy probléma, amikor az eszköz használata nem változott még akkor sem, amikor a fájlokat törölték. Ez a probléma ebben a verzióban lett kijavítva. A rétegezési kód elérési útja a törölt fájlok feldolgozásakor rugalmasabbá vált.|
| 2 |Kivételkezelés| A szoftver korábbi verzióiban a rendszer újraindítását követően olyan hiba történt a biztonsági mentések, a visszaállítás, a felhőből történő olvasás és az automatikus térvisszanyerés során. Ez a kiadás az indítási útvonalon a kivételek kezelésének módját illetően tartalmaz módosításokat.|

## <a name="known-issues-in-update-12"></a>Ismert problémák az 1.2-es frissítésben

Az 1.2-es frissítésben nem észleltek új problémákat. Az összes kiadás által észlelt probléma átkerül a korábbi kiadásokból. Az előző kiadásokban szereplő ismert problémák összegzését az [1.1-es frissítés Ismert problémái című témakörben található.](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)

## <a name="next-steps"></a>További lépések

Töltse le a KB4502035 frissítést, és [alkalmazza a frissítést a helyi internetes felhasználói felületen](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)keresztül.

## <a name="references"></a>Referencia

Régebbi kiadási megjegyzést keres? Ugrás:
* [StorSimple Virtual Array Update 1.1 Release Notes](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple virtual array update 1.0 kibocsátási megjegyzések](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple virtuális tömb frissítése 0,6 Kibocsátási megjegyzések](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple virtuális tömb frissítése 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.4 Kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.3 Kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.1 és 0.2 Kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzések](storsimple-ova-pp-release-notes.md)
