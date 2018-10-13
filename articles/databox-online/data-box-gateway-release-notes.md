---
title: Az Azure Data Box átjáró Preview kibocsátási megjegyzései |} A Microsoft Docs
description: Az Azure Data Box átjáró előzetes kiadást futtató kritikus megoldatlan problémák és megoldásuk ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f5e19d59dfddc3be849700f3678519179b5b39ba
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49164569"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Az Azure Data Box átjáró előzetes verzió kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure Data Box átjáró előzetes kiadás megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A Data Box Gateway, üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Előzetes kiadás felel meg a szoftververzió **Data Box átjáró előzetes verziója 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Az előzetes kiadásban megoldott problémák

Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Probléma |
| --- | --- |
| 1 | Ebben a kiadásban, ha egy másik eszköz (AzCopy) által feltöltött fájl frissülnek, és olyan módon, majd frissíti, amely növeli/terjeszti ki a fájl mérete, majd meg a következő hiba: *400-as hiba: InvalidBlobOrBlock (a megadott blob vagy letiltása tartalom érvénytelen.)*|


## <a name="known-issues-in-preview-release"></a>Az előzetes kiadásban ismert problémák

Az alábbi táblázat összefoglalja az ismert problémákról a Data Box Gateway előzetes kiadást futtató.

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás és megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |A Data Box Gateway eszközök létrehozott a korábbi előzetes verziók nem frissíthető erre a verzióra. |A virtuális lemez képeket tölt le az új kiadásban, és úgy konfigurálhatja, és új eszközök üzembe helyezésére. További információért ugorjon [üzembe helyezését az Azure Data Box Gateway](data-box-gateway-deploy-prep.md). |
| **2.** |Kiépített adatlemez |Bizonyos megadott méretű adatlemez kiépítése után, és a megfelelő mezőbe átjáró hozta létre, meg kell nem csökkentheti az adatlemezt. Kísérlet zsugorítása lemez eredményeit az adatvesztést a helyi adatokat az eszközön. | |
| **3.** |Frissítés |Ebben a kiadásban egyszerre csak egy megosztás is frissítheti. | |
| **4.** |Átnevezés |Az objektumok az átnevezés nem támogatott. |Ha ez a funkció elengedhetetlen a munkafolyamathoz, forduljon a Microsoft Support. |
| **5.** |Másolás| Ha az eszköz egy csak olvasható fájlba másolja, a csak olvasható tulajdonság nem őrződnek meg. | |
| **6.** |Logs| Ebben a kiadásban egy hiba miatt előfordulhat, hogy tekintse meg a 110-es hibakód példányait *error.xml* Felismerhetetlen elem nevekkel. | |
| **7.** |Feltöltés | Ebben a kiadásban egy hiba miatt bizonyos fájlok feltöltésekor hibakód 2003 példányainak jelenhet meg. | |
| **8.** |Fájltípusok | A következő Linux fájltípusok nem támogatottak: fájlokat, blokk-fájlok, sockets, pipe-ok, a szimbolikus hivatkozások karakter.  |Ossza meg ezeket a fájlokat az NFS első létrehozott 0 hosszúságú fájlokat eredményez. Ezeket a fájlokat a is jelenti, és továbbra is a hibás állapotú *error.xml*. |
| **9.** |Törlés | Ebben a kiadásban egy hiba miatt az NFS-megosztások törlésekor, majd a megosztás nem törölhetők. A megosztás állapot *törlése*.  |Ez akkor történik, csak ha a megosztás egy nem támogatott fájl nevét. |
| **10.** |Frissítés | Engedélyek és hozzáférés-vezérlési listák (ACL) nem maradnak a frissítési művelet között.  | |
| **11.** |Online súgó |Az Azure portal súgó hivatkozásai nem csatolhatja dokumentációját.|A súgó hivatkozásai az általánosan elérhető kiadások fog működni. |



## <a name="next-steps"></a>További lépések

- [Az üzembe helyezés az Azure Data Box Gateway előkészítése](data-box-gateway-deploy-prep.md).


