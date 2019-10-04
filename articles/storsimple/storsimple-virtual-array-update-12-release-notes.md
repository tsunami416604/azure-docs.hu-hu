---
title: A Microsoft Azure StorSimple virtuális tömb frissítés 1.2 kibocsátási megjegyzései |} A Microsoft Docs
description: Az 1.2-es frissítést futtató StorSimple Virtual Array kritikus megoldatlan problémák és megoldásuk ismertetése
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420603"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>A StorSimple Virtual Array frissítés 1.2-es kibocsátási megjegyzései

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek. Ahogy ismertté kritikus fontosságú problémáit, a rendszer hozzáadja azokat. A StorSimple Virtual Array üzembe helyezése, előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

1\.2-es frissítés felel meg a szoftververzió **10.0.10311.0**.

> [!IMPORTANT]
> - Azok zavart okozó frissítések, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz leállást. A csomagokat, használja a frissítés részletes utasításokért ugorjon [frissítése 1.2 letöltése](#download-update-12).
> - 1\.2-es frissítés érhető el, hogy az Azure Portalon csak akkor, ha az eszköz fut-e frissítés 1.0 és 1.1.

## <a name="whats-new-in-update-12"></a>1\.2-es frissítés újdonságai

Ez a frissítés a következő hibajavításokat tartalmaz:

- Jobb rugalmasság feldolgozásakor törölt fájlok.
- A kód indítási vezető elérési út jobb kezelése kivételek csökkenteni a hibák a biztonsági mentés, visszaállítás, felhőalapú olvasási műveleteknél, és a terület-visszaigénylést automatikus.

## <a name="download-update-12"></a>Töltse le az 1.2-es frissítés

Ez a frissítés letöltéséhez nyissa meg a [a Microsoft Update katalógus](https://www.catalog.update.microsoft.com/Home.aspx) server és a KB4502035 letöltési csomagra. Ez a csomag tartalmazza a következő csomagokat:

 - **KB4493446** összegző Windows-frissítések, amely tartalmazza a 2012 R2 legfeljebb április 2019. Mit tartalmaz az összegző további információért látogasson el [április havi a biztonság összegzése](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** Ez az a Microsoft Update önálló csomagfájl WindowsTH-KB3011067-x64. Ez a fájl frissítése az eszközhöz használatos.

Töltse le a KB4502035, és ezeket az utasításokat követve [frissítést a helyi webes felhasználói felületen](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Az 1.2-es frissítés megoldott problémák

Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Funkció | Probléma |
| --- | --- | --- |
| 1 |Törlés| A szoftver korábbi verzióiban hiba történt, amikor az eszköz használatát módosította, akkor is, ha fájlt törölt. Ebben a verzióban a probléma megoldódik. Rétegezési kódelérési út rugalmasabb történt, a törölt fájlok feldolgozása során.|
| 2 |Kivételkezelés| A szoftver korábbi verzióiban hiba történt a következő, a rendszer újraindítása, amely a biztonsági mentés, visszaállítás, a felhőbe való olvasás sikertelen eredményezhet, és automatikus beállításainak terület-visszanyerést. Ebben a kiadásban feltárhatja, hogy hogyan kezelt kivételek az indítási elérési út módosításokat tartalmaz.|

## <a name="known-issues-in-update-12"></a>Ismert problémák az 1.2-es frissítés

Nincsenek új problémák nem 1.2-es frissítés a kiadási feljegyzett. A kiadási feljegyzett hibákat a korábbi kiadások átkerülnek. Tekintse meg az előző kiadásokban található ismert problémák összefoglalása, lépjen a [ismert hibái, 1.1-es frissítés](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>További lépések

Töltse le a KB4502035 és [frissítést a helyi webes felhasználói felületen](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referencia

Egy régebbi kiadási Megjegyzés keres? Ugrás:
* [A StorSimple Virtual Array frissítés 1.1 kibocsátási megjegyzései](storsimple-virtual-array-update-11-release-notes.md)
* [A StorSimple Virtual Array frissítés 1.0-ás kibocsátási megjegyzései](storsimple-virtual-array-update-1-release-notes.md)
* [A StorSimple Virtual Array frissítés a 0.6-os kibocsátási megjegyzései](storsimple-virtual-array-update-06-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,5 kibocsátási megjegyzései](storsimple-virtual-array-update-05-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple Virtual Array frissítés 0.1, 0.2-es és kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)
