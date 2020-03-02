---
title: Mappa szerkezetének leképezése Azure File Sync topológiára
description: Egy meglévő fájl és mappa struktúrájának leképezése az Azure-fájlmegosztás számára a Azure File Sync való használatra. Egy közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209557"
---
Ebben a lépésben azt értékeli, hogy hány Azure-fájlmegosztás szükséges. Egyetlen Windows-kiszolgáló (vagy fürt) akár 30 Azure-fájlmegosztás szinkronizálására is képes.

Előfordulhat, hogy a StorSimple több mappája is van, amelyeket a felhasználók és alkalmazások számára jelenleg a helyi SMB-megosztásként oszt meg. A legegyszerűbb példa arra, hogy egy helyszíni megosztást egy Azure-fájlmegosztás 1:1-re való leképezéséhez. Ha ez a szám kezelhető kis méretű, azaz egy Windows-kiszolgáló esetében 30 alatta van, vagy két Windows-kiszolgálót (60) és így tovább, akkor a rendszer 1:1-leképezést ajánl.

Ha a 30-nál több megosztással rendelkezik, gyakran szükségtelen egy helyszíni megosztást (1:1) leképezni egy Azure-fájlmegosztás számára.
vegye figyelembe a következő lehetőségeket:

#### <a name="share-grouping"></a>Csoport megosztása

Ha például a HR-részleg összesen 15 megosztással rendelkezik, akkor érdemes lehet egyetlen Azure-fájlmegosztás összes HR-adatait tárolnia. Egy Azure-fájlmegosztás több helyszíni megosztásának tárolása nem akadályozza meg, hogy a szokásos 15 SMB-megosztást hozza létre a helyi Windows Server-kiszolgálón. Ez csak azt jelenti, hogy a 15 megosztás legfelső szintű mappáit almappákként rendezi a közös mappában. Ezután szinkronizálja ezt a közös mappát egy Azure-fájlmegosztás használatával. Így a helyszíni megosztások ezen csoportjára csak egyetlen Azure-fájlmegosztás szükséges a felhőben.

#### <a name="volume-sync"></a>Kötet szinkronizálása

Azure File Sync támogatja a kötetek gyökerének Azure-fájlmegosztás felé történő szinkronizálását.
Ha szinkronizálja a gyökérkönyvtárat, az összes almappa és fájl ugyanabban az Azure-fájlmegosztás végén fog történni.

#### <a name="other-best-practices-to-consider"></a>Egyéb ajánlott eljárások

A kiszolgálón a 30 Azure-beli fájlmegosztás szinkronizálási korlátján kívül a fő szempont a szinkronizálás hatékonysága.

Ha a kiszolgálón több megosztás található a saját Azure-fájlmegosztás szinkronizálásával, a szinkronizálás párhuzamosan is működhet. A skálázási vektor nem a szinkronizálási hatókörben lévő összes fájl mérete. A feldolgozást igénylő elemek (fájlok és mappák) száma.

Egyetlen Azure-fájlmegosztás akár 100 TiB-t is képes tárolni.
Azure File Sync támogatja az Azure-fájlmegosztás legfeljebb 100 000 000 elemének szinkronizálását.

A gyökérszintű kötet szinkronizálása nem mindig lesz a legjobb válasz. Több hely szinkronizálása is előnyös, így a szinkronizálási hatókörben lévő elemek számának megtartása is segít. Az Azure file Sync kevesebb elemmel való beállítása nem csupán fontos a szinkronizáláshoz, hanem a biztonsági másolatokből származó Felhőbeli visszaállítás előnyeit, valamint a vész-helyreállítási sebességének biztosítását is, ha elveszíti a kiszolgálót, és olyan újat hoz létre, amely ugyanahhoz az Azure Fil-hoz csatlakozik. e megosztások.

A fenti fogalmak kombinációjának segítségével meghatározhatja, hogy hány Azure-fájlmegosztás szükséges, és hogy a meglévő StorSimple-adatai mely részeit fogják megállapítani az Azure-fájlmegosztás végén.

Hozzon létre egy listát, amely rögzíti a gondolatait, így a következő lépésben hivatkozhat rá. Az itt megrendezett maradás fontos, mivel könnyen elveszítheti a leképezési terv részleteit, ha egyszerre több erőforrást is kiépít.
