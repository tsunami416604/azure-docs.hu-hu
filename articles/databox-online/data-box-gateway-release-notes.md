---
title: Azure Data Box Gateway általános rendelkezésre állási kiadási megjegyzések| Microsoft dokumentumok
description: Az általános rendelkezésre állási kiadást futtató Azure Data Box Gateway kritikus nyitott problémáit és megoldásait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265401"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway általános rendelkezésre állási kiadási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és az Azure Data Box Edge és az Azure Data Box Gateway általános rendelkezésre állásával (GA) kapcsolatos megoldott problémákat.

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A Data Box Edge/Data Box Gateway telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

A GA kiadás megfelel a szoftver verziók:

- **Data Box átjáró 1903 (1.5.814.447)**
- **Adatdoboz széle 1903 (1.5.814.447)**


## <a name="whats-new"></a>Újdonságok

- **Új virtuális lemezképek** – Új VHDX és VMDK már elérhető az Azure Portalon. Töltse le ezeket a lemezképeket az új Data Box Gateway GA-eszközök kiépítéséhez, konfigurálásához és üzembe helyezéséhez. A korábbi előzetes verziókban létrehozott Data Box Gateway-eszközök nem frissíthetők erre a verzióra. További információ: Felkészülés az [Azure Data Box Gateway üzembe helyezésére](data-box-gateway-deploy-prep.md)című területen.
- **NFS-támogatás** – Az NFS-támogatás jelenleg előzetes verzióban érhető el, és a Data Box Edge és a Data Box Gateway eszközökhöz hozzáférő 3.0-s és 4.1-es verziójú ügyfelek számára érhető el.
- **Tárolási rugalmasság** – A Data Box Edge eszköz képes ellenállni egy adatlemez meghibásodásának a Storage rugalmassági funkcióval. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Engedélyezheti a tárolási rugalmasságot, ha a helyi webes felhasználói felület **Tárolási beállításai** között a **Rugalmas** beállítást választja.


## <a name="known-issues-in-ga-release"></a>Ismert problémák a GA kiadásban

Az alábbi táblázat a Data Box Gateway futó kiadásának ismert problémáit tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Fájltípusok | A következő fájltípusok nem támogatottak: karakterfájlok, blokkfájlok, szoftvercsatornák, csövek, szimbolikus hivatkozások.  |A fájlok másolása 0 hosszúságú fájlok létrehozását eredményezi az NFS-megosztáson. Ezek a fájlok hibaállapotban maradnak, és *a hiba.xml*fájlban is jelennek meg. <br> A könyvtárakra mutató szimbolikus hivatkozások azt eredményezik, hogy a könyvtárak soha nem lesznek megjelölve offline módban. Ennek eredményeképpen előfordulhat, hogy nem jelenik meg a szürke kereszt a könyvtárakon, amely azt jelzi, hogy a könyvtárak offline állapotúak, és az összes kapcsolódó tartalom teljesen feltöltve lett az Azure-ba. |
| **2.** |Törlés | Ha egy NFS-megosztást töröl, akkor előfordulhat, hogy a megosztás nem törlődik. A megosztás állapota a Törlés felirat *jelenik*meg.  |Ez csak akkor fordul elő, ha a megosztás nem támogatott fájlnevet használ. |
| **3.** |Másolás | Az adatmásolás hiba miatt sikertelen: A kért művelet fájlrendszerkorlátozás miatt nem hajtható végre.  |A 128 KB-nál nagyobb fájlmérethez társított alternatív adatfolyam (ADS) nem támogatott.   |


## <a name="next-steps"></a>További lépések

- [Felkészülés az Azure Data Box Gateway telepítésére.](data-box-gateway-deploy-prep.md)
- [Készüljön fel az Azure Data Box Edge üzembe helyezésére.](data-box-edge-deploy-prep.md)
