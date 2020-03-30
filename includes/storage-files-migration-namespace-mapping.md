---
title: Mappastruktúra hozzárendelése Azure-fájlszinkronizálási topológiához
description: Meglévő fájl- és mappastruktúra leképezése az Azure-fájlmegosztásokhoz az Azure File Sync használatával. Közös szövegterület, amely et megosztanak az áttelepítési dokumentumok között.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124731"
---
Ebben a lépésben kiértékeli, hogy hány Azure-fájlmegosztásra van szüksége. Egyetlen Windows Server (vagy fürt) legfeljebb 30 Azure-fájlmegosztást szinkronizálhat.

Előfordulhat, hogy több mappa van a köteteken, amelyeket jelenleg helyileg oszt meg SMB-megosztásként a felhasználók és az alkalmazások számára. A legegyszerűbb az lenne, hogy elképzelni egy helyszíni megosztás leképezése 1:1 egy Azure-fájlmegosztás. Ha egy Windows Server esetében elég kicsi, 30 alatt van, akkor 1:1-es hozzárendelés ajánlott.

Ha több megosztást, mint 30, gyakran szükségtelen leképezni egy helyszíni megosztás 1:1 egy Azure-fájlmegosztásra.
Vegye figyelembe a következő lehetőségeket:

#### <a name="share-grouping"></a>Csoportosítás megosztása

Például ha a HR-részleg összesen 15 megosztást, majd érdemes lehet az összes HR-adatok egyetlen Azure-fájlmegosztás tárolják. Több helyszíni megosztás oka egyetlen Azure-fájlmegosztásban nem akadályozza meg a szokásos 15 SMB-megosztás létrehozását a helyi Windows Serveren. Ez csak azt jelenti, hogy a 15 megosztás gyökérmappáit almappákként rendezi egy közös mappa alatt. Ezután szinkronizálja ezt a közös mappát egy Azure-fájlmegosztással. Így csak egyetlen Azure-fájlmegosztás a felhőben van szükség ehhez a csoporthoz a helyszíni megosztások.

#### <a name="volume-sync"></a>Kötet szinkronizálása

Az Azure File Sync támogatja a kötet gyökérének szinkronizálását egy Azure-fájlmegosztással.
Ha szinkronizálja a gyökérmappát, akkor az összes almappa és fájl ugyanabban az Azure-fájlmegosztásban fog végződni.

A kötet gyökerének szinkronizálása nem mindig a legjobb válasz. Több hely szinkronizálása is előnyökkel jár, így az elemek száma szinkronizálási hatókörönként alacsonyabb marad. Az Azure File Sync beállítása kisebb számú elemsel nem csak a fájlszinkronizálás szempontjából előnyös. Az alacsonyabb számú elem más forgatókönyveket is hasznosít, például:

* felhőoldali visszaállítás egy azure-beli fájlmegosztás pillanatképéből, amely biztonsági másolatként készült
* a helyszíni kiszolgáló vészhelyreállítása jelentősen felgyorsíthatja a
* a közvetlenül az Azure-fájlmegosztásban (a szinkronizáláson kívül) végrehajtott módosítások gyorsabban észlelhetők és szinkronizálhatók

#### <a name="a-structured-approach-to-a-deployment-map"></a>A telepítési térkép strukturált megközelítése

A felhőalapú tárolás üzembe helyezése előtt egy későbbi lépésben fontos, hogy hozzon létre egy leképezést a helyszíni mappák és az Azure-fájlmegosztások között. Ez a leképezés ezután tájékoztatja, hogy hány és milyen Azure File Sync "szinkronizálási csoport" erőforrásokat fog kiépíteni. A szinkronizálási csoport összeköti az Azure-fájlmegosztást és a kiszolgálón lévő mappát, és szinkronizálási kapcsolatot hoz létre.

Ha el szeretné dönteni, hogy hány Azure-fájlmegosztásra van szüksége, tekintse át az alábbi korlátokat és gyakorlati tanácsokat. Ezzel segít optimalizálni a térképet:

* Az Azure File Sync ügynökkel rendelkező kiszolgáló legfeljebb 30 Azure-fájlmegosztással szinkronizálható.
* Az Azure-fájlmegosztás egy tárfiókban van telepítve. Ez teszi a tárfiókot a teljesítményszámok, például az IOPS és az átviteli teljesítmény méretezési cél. Két standard (nem prémium) Azure-fájlmegosztás elméletileg telítheti a tárfiók által biztosítható maximális teljesítményt. Ha azt tervezi, hogy csak csatolja az Azure File Sync ezeket a fájlmegosztásokat, majd több Azure-fájlmegosztások csoportosítása ugyanabba a tárfiókba nem okoz problémát. Tekintse át az Azure-fájlmegosztási teljesítménycélokat, hogy mélyebb betekintést nyerjen a megfelelő metrikákba. Ha azt tervezi, hogy egy olyan alkalmazást emel ki az Azure-ba, amely natív módon fogja használni az Azure-fájlmegosztást, akkor nagyobb teljesítményre lehet szüksége az Azure-fájlmegosztásból. Ha ez egy lehetőség, még a jövőben is, akkor az Azure-fájlmegosztás hozzárendelése a saját tárfiókhoz a legjobb.
* Előfizetésenként legfeljebb 250 tárfiók érhető el egyetlen Azure-régióban.

> [!TIP]
> Ezt az információt szem előtt tartva gyakran szükségessé válik, hogy a köteteken több legfelső szintű mappát csoportosítson egy közös, új gyökérkönyvtárba. Ezután szinkronizálja ezt az új gyökérkönyvtárat és az összes hozzá csoportosított mappát egyetlen Azure-fájlmegosztásra.                                                    

Ez a módszer lehetővé teszi, hogy kiszolgálónként a 30 Azure-fájlmegosztás szinkronizálási korlátján belül maradjon.
Ez a csoportosítás egy közös gyökér alatt nincs hatással az adatokhoz való hozzáférésre. Az ACL-ek továbbra is úgy maradnak, ahogy van, csak módosítania kell a megosztási útvonalakat (például az SMB- vagy NFS-megosztásokat), amelyek a kiszolgálómappákban találhatók, és amelyeket most általános gyökérre módosított. Semmi más nem változik.

Az Azure File Sync egy másik fontos szempontja, valamint a kiegyensúlyozott teljesítmény és tapasztalat az Azure File Sync teljesítményének méretezési tényezőinek megértése. Nyilvánvalóan, mikor fájlokat van szinkronizál felső a Internet, nagyobb fájlokat fog több idő és sávszélesség -hoz szinkronizál.

> [!IMPORTANT]
> Az Azure File Sync legfontosabb méretezési vektora a szinkronizálandó elemek (fájlok és mappák) száma.

Az Azure File Sync támogatja a szinkronizálást akár 100 000 elem egyetlen Azure-fájlmegosztásra. Ez a korlát túlléphető, és csak azt ábrázolja, amit az Azure File Sync csapat rendszeresen tesztel.

Ajánlott alacsonyan tartani az elemek számát a szinkronizálási hatókörönként. Ez a szempont fontos tényező, amelyet figyelembe kell venni a mappák Azure-fájlmegosztásokhoz való hozzárendelése során.

Még akkor is, ha a helyzetben egy mappacsoport logikailag szinkronizálható ugyanazzal az Azure-fájlmegosztással (az új, közös gyökérmappa-megközelítést használva felülről), még mindig jobb lehet a mappák újracsoportosítása, hogy azok egy Azure-fájlmegosztás helyett kettőre szinkronizálódjanak. Ezzel a módszerrel a fájlmegosztásonkénti fájlok és mappák számát egyensúlyban tarthatja a kiszolgálón.

#### <a name="create-a-mapping-table"></a>Leképezési tábla létrehozása

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        A korábbi fogalmak kombinációjával meghatározhatja, hogy hány Azure-fájlmegosztásra van szüksége, és hogy a meglévő adatok mely részei kerülnek végül az Azure-fájlmegosztásra.
        
        Hozzon létre egy táblázatot, amely rögzíti a gondolatait, hogy a következő lépésben hivatkozhat rá. A rendszerezés fontos, mivel számos Azure-erőforrás egyszerre történő kiépítésekor könnyen elveszítheti a leképezési terv részleteit. A teljes leképezés létrehozásának elősegítése érdekében sablonként tölthetünk le egy Microsoft Excel fájlt.

[//]: # (A HTML az egyetlen módja annak, hogy egy beágyazott kéthasábos táblázatot adjon hozzá, amelynek használata a szöveg/hivatkozás ugyanazon a sorban van.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Töltsön le egy névtér-leképezési sablont.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
