---
title: Mappa szerkezetének leképezése Azure File Sync topológiára
description: Egy meglévő fájl és mappa struktúrájának leképezése az Azure-fájlmegosztás számára a Azure File Sync való használatra. Közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 948090d0ee956ca1798d7b0f46bb33276c4d6354
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143599"
---
Ebben a lépésben azt értékeli, hogy hány Azure-fájlmegosztás szükséges. Egyetlen Windows Server-példány (vagy fürt) akár 30 Azure-fájlmegosztás szinkronizálására is képes.

Előfordulhat, hogy a köteteken további mappák vannak, amelyeket a felhasználók és alkalmazások SMB-megosztásként helyileg oszt meg. A legegyszerűbb módszer egy helyszíni megosztás kimutatása, amely a 1:1-et egy Azure-fájlmegosztás felé képezi le. Ha elég kicsi a száma, akkor az egy adott Windows Server-példány esetében 30 alatt marad, a 1:1-es leképezést ajánljuk.

Ha a 30-nál több megosztással rendelkezik, gyakran szükségtelen egy helyszíni megosztást (1:1) leképezni egy Azure-fájlmegosztás számára. Vegye figyelembe a következő beállításokat.

#### <a name="share-grouping"></a>Csoport megosztása

Ha a HR-részleg (például) összesen 15 megosztást tartalmaz, érdemes lehet egy Azure-fájlmegosztás összes HR-adatait tárolni. Egy Azure-fájlmegosztás több helyszíni megosztásának tárolása nem akadályozza meg, hogy a szokásos 15 SMB-megosztást hozza létre a helyi Windows Server-példányon. Ez csak azt jelenti, hogy a 15 megosztás legfelső szintű mappáit almappákként rendezi a közös mappában. Ezután szinkronizálja ezt a közös mappát egy Azure-fájlmegosztás használatával. Így a helyszíni megosztások ezen csoportjára csak egyetlen Azure-fájlmegosztás szükséges a felhőben.

#### <a name="volume-sync"></a>Kötet szinkronizálása

Azure File Sync támogatja a kötetek gyökerének Azure-fájlmegosztás felé történő szinkronizálását. Ha a gyökérkönyvtárat szinkronizálja, akkor az összes almappa és fájl ugyanarra az Azure-megosztásra kerül.

A kötet gyökerének szinkronizálása nem mindig a legjobb válasz. Több hely szinkronizálása is előnyökkel jár. Így például segít megőrizni az elemek számát a szinkronizálási hatókörben. Az alacsonyabb számú elemet tartalmazó Azure File Sync beállítása nem elég hasznos a fájlok szinkronizálásához. Az elemek alacsonyabb száma is előnyökkel jár, például a következő esetekben:

* Az Azure fájlmegosztás pillanatképének felhőalapú visszaállítását biztonsági mentésként is elvégezheti.
* A helyszíni kiszolgálók vész-helyreállítási sebessége jelentősen felgyorsulhat.
* Az Azure-fájlmegosztás (szinkronizáláson kívül) által közvetlenül végrehajtott módosítások észlelhetők és gyorsabban szinkronizálhatók.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Egy üzembe helyezési Térkép strukturált megközelítése

Mielőtt egy későbbi lépésben üzembe helyezi a felhőalapú tárolót, fontos, hogy térképet hozzon létre a helyszíni mappák és az Azure-fájlmegosztás között. Ez a leképezés ezután tájékoztatja, hogy hány és melyik Azure File Sync *szinkronizálási csoport* erőforrásai lesznek kiépítve. A szinkronizálási csoport összekapcsolja az Azure-fájlmegosztást és a kiszolgálón található mappát, és létrehozza a szinkronizálási kapcsolatot.

A következő korlátozásokkal és ajánlott eljárásokkal határozhatja meg, hogy milyen számú Azure-fájlmegosztás szükséges. Ez segít a Térkép optimalizálásában.

* A telepített Azure File Sync ügynökkel rendelkező kiszolgálók akár 30 Azure-fájlmegosztás szinkronizálásával is szinkronizálhatók.
* Egy Azure-fájlmegosztás üzembe helyezése egy Storage-fiókon belül történik. Ennek köszönhetően a Storage-fiók egy méretezési célt szolgál a teljesítményszámlálók, például a IOPS és az átviteli sebesség számára. 

  Két standard (nem prémium szintű) Azure-fájlmegosztás elméletileg felhasználhatja a Storage-fiók által kézbesíthető maximális teljesítményt. Ha azt tervezi, hogy csak Azure File Sync csatol a fájlmegosztás számára, akkor a több Azure-fájlmegosztás ugyanahhoz a Storage-fiókhoz való csoportosítása nem okoz problémát. Tekintse át az Azure fájlmegosztás teljesítményének céljait, és tekintse át a releváns mérőszámokat. 

  Ha azt tervezi, hogy az Azure-ban natív módon fogja használni az Azure-fájlmegosztást, akkor előfordulhat, hogy az Azure-fájlmegosztás nagyobb teljesítményre van szüksége. Ha ez a lehetőség még a jövőben is lehetséges, az Azure-fájlmegosztás a saját Storage-fiókjához való leképezése a legmegfelelőbb.
* Egy Azure-régióban legfeljebb 250 Storage-fiók adható meg előfizetésre.

> [!TIP]
> Ezekkel az információkkal gyakran szükségessé válik, hogy a köteteken több legfelső szintű mappát egy közös, új gyökérkönyvtárba csoportosítson. Ezután szinkronizálja ezt az új gyökérkönyvtárat és a hozzá tartozó összes mappát egyetlen Azure-fájlmegosztás számára. Ez a módszer lehetővé teszi, hogy kiszolgálónként legfeljebb 30 Azure fájlmegosztás-szinkronizáláson belül maradjon.
>
> A közös gyökér alá tartozó csoportosítás nem befolyásolja az adataihoz való hozzáférést. Az ACL-ek maradnak. Csak a megosztási útvonalakat (például az SMB-vagy NFS-megosztásokat) kell módosítania, hogy azok a kiszolgálói mappákban legyenek, amelyeket mostantól egy közös gyökérben módosítanak. Semmi más nem változik.

Azure File Sync egy másik fontos aspektusa, és a kiegyensúlyozott teljesítmény és a tapasztalatok megismerik a Azure File Sync teljesítményének méretezési tényezőit. A fájlok interneten keresztüli szinkronizálása természetesen a nagyobb fájlok több időt és sávszélességet is igénybe vesznek a szinkronizáláshoz.

> [!IMPORTANT]
> A Azure File Sync legfontosabb méretezési vektora a szinkronizálandó elemek (fájlok és mappák) száma.

Azure File Sync támogatja akár 100 000 elem szinkronizálását egyetlen Azure-fájlmegosztás számára. Ez a korlát túlléphető, és csak azt jeleníti meg, hogy az Azure File Sync csapat rendszeresen tesztelje a teszteket.

Az ajánlott eljárás az, hogy a szinkronizálási hatókörben lévő elemek száma ne legyen alacsony. Ez fontos szempont a mappák Azure-fájlmegosztás számára történő leképezésében.

Ebben az esetben lehetséges, hogy a mappák egy halmaza képes logikailag szinkronizálni ugyanazt az Azure-fájlmegosztást (a korábban említett új, közös gyökérmappa-megközelítés használatával). Előfordulhat azonban, hogy továbbra is jobb lesz a mappák újracsoportosítása, például egy Azure-fájlmegosztás helyett kettőre szinkronizálva. Ezzel a módszerrel megtarthatja, hogy a fájlmegosztás hány fájlt és mappát használjon a kiszolgálón.

#### <a name="create-a-mapping-table"></a>Leképezési tábla létrehozása

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Az előző fogalmak kombinációjának segítségével meghatározhatja, hogy hány Azure-fájlmegosztás szükséges, és hogy a meglévő adatai mely részeit fogják megállapítani az Azure-fájlmegosztás végén.
        
        Hozzon létre egy táblázatot, amely rögzíti a gondolatait, így a következő lépésben hivatkozhat rá. A szervezett maradás fontos, mert könnyen elveszítheti a leképezési terv részleteit, ha egyszerre több Azure-erőforrást is üzembe helyez. Ha segítségre van szüksége a teljes leképezés létrehozásához, letöltheti a Microsoft Excel-fájlokat sablonként.

[//]: # (A HTML csak úgy jelenik meg, ha egy beágyazott kétoszlopos táblázatot ad hozzá a munkaképek elemzésével és szövegével vagy hiperhivatkozásával ugyanazon a sorban.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Névtér-leképezési sablon letöltése.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
