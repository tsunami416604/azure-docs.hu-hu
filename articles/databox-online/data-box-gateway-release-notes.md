---
title: Az Azure Data Box-átjáró általános rendelkezésre állás kibocsátási megjegyzések |} A Microsoft Docs
description: Általánosan elérhető kiadások futtató Azure-Box adatátjáróhoz kritikus megoldatlan problémák és megoldásuk ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 3f3e07d745d83041afc9dafd64678a3ac4a65012
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418024"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway General Availability release notes

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák azonosításához, és az Azure Data Box Edge és az Azure Data Box Gateway kiadás megoldott problémák az általánosan elérhető (GA).

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A Data Box Edge/Data Box Gateway, üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az általánosan elérhető kiadás felel meg a szoftver verziójával:

- **Data Box átjáró 1903 (1.5.810.441)**
- **Data Box Edge 1903 (1.5.810.441)**


## <a name="whats-new"></a>Újdonságok

- **Új virtuális lemez lemezképek** – új VHDX és VMDK mostantól elérhetők az Azure Portalon. Töltse le a lemezképek létrehozása, konfigurálása, és új Data Box Gateway általánosan elérhető eszközök üzembe. A Data Box Gateway eszközök létrehozott a korábbi előzetes verziók nem frissíthető erre a verzióra. További információért ugorjon [üzembe helyezését az Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Támogatja az NFS** -NFS-támogatása jelenleg előzetes verzióban érhető el, és elérhető legyen a 3.0-s verzió és v4.1 ügyfelek férnek hozzá a Data Box Edge és a Data Box Gateway eszközöket.
- **Storage rugalmasságot** – a Data Box Edge-eszköz képes elviselni a hiba, a Storage rugalmassági funkcióval egy adatlemezt. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Storage rugalmasságot kiválasztásával engedélyezheti a **rugalmas** beállítást a **tárolási beállítások** a helyi webes felhasználói Felületét.


## <a name="known-issues-in-ga-release"></a>Ismert problémák a GA kiadás

Az alábbi táblázat összefoglalja az ismert problémákról a Data Box átjáró verzióját futtatja.

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás és megjegyzések |
| --- | --- | --- | --- |
| **1.** |Fájltípusok | A következő fájltípusok nem támogatottak: fájlokat, blokk-fájlok, sockets, pipe-ok, a szimbolikus hivatkozások karakter.  |Ossza meg ezeket a fájlokat az NFS első létrehozott 0 hosszúságú fájlokat eredményez. Ezeket a fájlokat a is jelenti, és továbbra is a hibás állapotú *error.xml*. <br> Szimbolikus hivatkozások címtárakhoz soha nem offline kezdeti megjelölve könyvtárak eredményez. Ennek eredményeképpen, előfordulhat, hogy nem jelenik meg a szürke keresztszűrés a könyvtárak, amely azt jelzi, hogy a könyvtárakat offline üzemmódban van, a kapcsolódó tartalmat teljesen töltött fel az Azure-bA. |
| **2.** |Törlés | Ebben a kiadásban egy hiba miatt az NFS-megosztások törlésekor, majd a megosztás nem törölhetők. A megosztás állapot *törlése*.  |Ez akkor történik, csak ha a megosztás egy nem támogatott fájl nevét. |
| **3.** |Másolás | Adatok másolása hibával meghiúsul:  A kért művelet nem sikerült a fájlrendszer egy korlátozása miatt.  |A másodlagos Data Stream (ADS) fájl mérete 128 KB-nál nagyobb társított nem támogatott.   |


## <a name="next-steps"></a>További lépések

- [Az üzembe helyezés az Azure Data Box Gateway előkészítése](data-box-gateway-deploy-prep.md).
- [Az üzembe helyezés az Azure Data Box Edge előkészítése](data-box-edge-deploy-prep.md).
