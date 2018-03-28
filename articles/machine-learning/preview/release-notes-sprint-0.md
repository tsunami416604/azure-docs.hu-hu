---
title: Az Azure ML munkaterület sprint 0 október 2017 kibocsátási megjegyzései
description: Ez a dokumentum részletesen a frissítéseket az Azure ML sprint 0 kiadása
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-0---october-2017"></a>Sprint 0 – 2017. október 

**Verziószám: 0.1.1710.04013**

Üdvözli az Azure Machine Learning munkaterület kezdeti nyilvános előzetes verzióban ezek a Microsoft ignite-on 2017 konferencián követő első frissítésére. A fő ebben a kiadásban frissítései megbízhatóság és a stabilitás javítja.  A kritikus problémákra azt többek között:

## <a name="new-features"></a>Új funkciók
- macOS magas Sierra mostantól támogatott.

## <a name="bug-fixes"></a>Hibajavítások
### <a name="workbench-experience"></a>Munkaterület-élmény
- A fogd és vidd egy fájlt a munkaterületre hatására az összeomlás-munkaterület Telepítésmegosztási.
- A konfigurált egy IDE munkaterület nem ismeri fel a Visual STUDIO Code terminálablakot _az ml_ parancsok.

### <a name="workbench-authentication"></a>Munkaterület-hitelesítés
Jelentett különböző bejelentkezési és hitelesítési hibák javító frissítés számos előre meghatározott jellemzőkkel azt.
- Hitelesítési ablak tartja a popping felfelé, különösen akkor, ha nincs stabil internetkapcsolat.
- Továbbfejlesztett megbízhatósági hibákat elhárító hitelesítési jogkivonat lejáratáról körül.
- Bizonyos esetekben hitelesítési ablak kétszer.
- Munkaterület főablaka továbbra is "hitelesítő" üzenetet a hitelesítési folyamat ténylegesen befejezését és az előugró párbeszédpanelen már jeleníti meg.
- Ha nincs internetkapcsolat, a hitelesítési párbeszéd ugrik fel üres képernyőt.

### <a name="data-preparation"></a>Adatok előkészítése 
- Ha egy konkrét értékre vannak leszűrve, hibákat és a hiányzó értékeket is ki van szűrve.
- Mintavételi stratégia módosítása eltávolítja az ezt követő meglévő összekapcsolási műveletek.
- Hiányzó érték cseréje átalakító nem figyelembe NaN.
- Dátum következtetésének kivételt okoz, amikor null értéket talált.

### <a name="job-execution"></a>Feladat végrehajtása
- Ha feladat végrehajtása nem sikerült feltölteni a projektmappa, mert az túllépte a maximális mérete, nincs egyértelmű hiba üzenet.
- Ha a felhasználó Python-parancsfájl módosítja a munkakönyvtárat, a fájlok írása kimenetek mappák nem követi nyomon. 
- Ha az aktív Azure-előfizetés eltérő az aktuális projektben tartozik, a feladat elküldése eredmények 403-as hiba.
- Amikor Docker nincs jelen, ha a felhasználó megpróbál Docker egy végrehajtási célként használandó nem egyértelmű hibaüzenetet adott vissza.
- .runconfig fájl nem menti automatikusan, amikor a felhasználó a hivatkozásra kattint _futtatása_ gombra.

### <a name="jupyter-notebook"></a>Jupyter notebook
- Notebook kiszolgáló nem indítható el, ha a felhasználó bejelentkezési bizonyos használ.
- Notebook server hibaüzenetek jelent a naplókban felhasználó számára látható.

### <a name="azure-portal"></a>Azure Portal
- Az Azure portál sötét téma kiválasztásának hatására a modell-felügyelet panel megjeleníteni egy fekete mezőt.

### <a name="operationalization"></a>Operationalization
- A beépített véletlenszerű nevű új Docker-lemezkép újbóli felhasználása a jegyzék egy webszolgáltatás-bővítmény frissítése hatására.
- Web service naplóit Kubernetes fürt nem lehet beolvasni.
- Félrevezető hibaüzenet van nyomtatva, amikor a felhasználó megpróbál létrehozni egy modell felügyeleti fiókja vagy egy ML számítási fiók, és engedélyekkel kapcsolatos probléma lép fel.
