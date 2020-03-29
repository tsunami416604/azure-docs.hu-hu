---
title: Az Azure NSG-folyamatnaplók megjelenítése – Power BI
titleSuffix: Azure Network Watcher
description: Ez a lap bemutatja, hogyan jelenítheti meg az NSG-folyamatnaplókat a Power BI-val.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 955e13b88037aa42b59707698549b1c980720990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840604"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>A Hálózati biztonság csoport folyamatnaplóinak megjelenítése a Power BI segítségével

A hálózati biztonsági csoport folyamatnaplói lehetővé teszik a hálózati biztonsági csoportok be- és kimenő IP-forgalmával kapcsolatos információk megtekintését. Ezek a folyamatnaplók szabályonként jelenítik meg a kimenő és bejövő folyamatokat, a folyamatáltal alkalmazott hálózati adaptert, a folyamat 5-hangú adatait (Forrás/cél IP, Forrás/cél port, Protokoll), valamint azt, hogy a forgalmat engedélyezték vagy megtagadták-e.

A naplófájlok manuális keresésével nehéz lehet betekintést nyerni a folyamatnaplózási adatokba. Ebben a cikkben megoldást kínálunk a legutóbbi folyamatnaplók megjelenítésére és a hálózati forgalom megismerésére.

> [!Warning]  
> A következő lépések az 1-es verziójú folyamatnaplókkal működnek. További információt a [Hálózati biztonsági csoportok folyamatnaplózásának bemutatása című témakörben talál.](network-watcher-nsg-flow-logging-overview.md) A következő utasítások módosítás nélkül nem működnek a naplófájlok 2-es verziójával.

## <a name="scenario"></a>Forgatókönyv

A következő esetben a Power BI desktopot az NSG-folyamatnaplózási adatok fogadójaként konfigurált tárfiókhoz kapcsoljuk. Miután csatlakoztunk a tárfiókunkhoz, a Power BI letölti és elemzi a naplókat, hogy vizuálisan reprezentálta a hálózati biztonsági csoportok által naplózott forgalmat.

A sablonban megadott vizualizációk segítségével megvizsgálhatja a következőket:

* Legjobb beszélők
* Idősorozat-folyami adatok irány- és szabálydöntés szerint
* Folyamatok hálózati adapter MAC-címe szerint
* NSG és szabály szerint zajló folyamatok
* Folyamatok célport szerint

A megadott sablon szerkeszthető, így módosíthatja, hogy új adatokat, vizualizációkat adjon hozzá, vagy az igényeinek megfelelően módosítsa a lekérdezéseket.

## <a name="setup"></a>Telepítés

Mielőtt elkezdené, a fiók egy vagy több hálózati biztonsági csoportjában engedélyeznie kell a hálózati biztonsági csoport folyamatnaplózását. A hálózati biztonsági folyamatnaplók engedélyezésével kapcsolatos tudnivalókat a következő cikkben találja: Bevezetés a [hálózati biztonsági csoportok folyamatnaplózásába.](network-watcher-nsg-flow-logging-overview.md)

A Power BI Desktop ügyfélnek is telepítve kell lennie a számítógépen, és elegendő szabad helyet kell tárolnia a számítógépen a tárfiókban található naplóadatok letöltéséhez és betöltéséhez.

![Visio-diagram][1]

### <a name="steps"></a>Lépések

1. A Power BI Alkalmazás [hálózati figyelője PowerBI-folyamatnaplók sablonjának](https://aka.ms/networkwatcherpowerbiflowlogstemplate) letöltése és megnyitása a következő Power BI-sablonban
1. Adja meg a szükséges lekérdezési paramétereket
   1. **StorageAccountName** – Megadja a betölteni és vizualizálni kívánt NSG-folyamatnaplókat tartalmazó tárfiók nevét.
   1. **NumberOfLogFiles** – Megadja a Power BI-ban letölteni és megjeleníteni kívánt naplófájlok számát. Ha például 50 van megadva, az 50 legújabb naplófájl. Ha 2 NSG-t engedélyeztünk és konfiguráltunk úgy, hogy NSG-folyamatnaplókat küldjünk erre a fiókra, akkor az elmúlt 25 órányi napló megtekinthető.

      ![power BI fő][2]

1. Adja meg a tárfiók hozzáférési kulcsát. Az érvényes hozzáférési kulcsokat úgy találhatja meg, hogy az Azure Portalon navigál a tárfiókra, és a Beállítások menü **hozzáférési kulcsok parancsát** választja. Kattintson a **Csatlakozás gombra,** majd alkalmazza a módosításokat.

    ![hozzáférési kulcsok][3]

    ![2. hozzáférési kulcs][4]

4. A naplók letöltése és elemzésre kerülnek, és most már használhatja az előre létrehozott vizualizációkat.

## <a name="understanding-the-visuals"></a>A vizualizációk ismertetése

A sablonban olyan vizualizációk találhatók, amelyek segítenek megérteni az NSG-folyamatnapló adatait. Az alábbi képek en látható, hogy hogyan néz ki az irányítópult, amikor adatokkal van feltöltve. Az alábbiakban megvizsgáljuk az egyes vizuális részletesebb 

![powerbi][5]
 
A Top Talkers vizualizáció azokat az IP-ket jeleníti meg, amelyek a legtöbb kapcsolatot kezdeményezték a megadott időszakban. A dobozok mérete a kapcsolatok relatív számának felel meg. 

![toptalkers][6]

A következő idősorozat-grafikonok az időszak során a folyamatok számát mutatják. A felső grafikon tágítja az áramlás irányát, és az alsó szegmentált a döntés (engedélyezése vagy megtagadása). Ezzel a vizualizációval megvizsgálhatja a forgalmi trendeket az idő múlásával, és észlelheti a forgalom vagy a forgalom szegmentálásának rendellenes csúcsait vagy csökkenését.

![flowoverperiod][7]

A következő grafikonok a hálózati interfészenkénti folyamatokat mutatják, a felső toldalékot áramlásirány szerint szegmentálva, az alsót pedig döntés szerint szegmentálva. Ezekkel az információkkal betekintést nyerhet abba, hogy a virtuális gépek közül melyik kommunikált a legtöbbet másokhoz képest, és ha egy adott virtuális gép forgalmat engedélyeznek vagy megtagadnak.

![áramlásperonikus][8]

A következő fánkkerék-diagram a Folyamatok célport szerinti bontását mutatja. Ezzel az információval megtekintheti a megadott időszakban használt leggyakrabban használt célportokat.

![gyűrű][9]

A következő sávdiagramon a Flow by NSG és a Rule látható. Ezzel az információval láthatja a legnagyobb forgalomért felelős NSG-ket, valamint az NSG-n a forgalom szabályok szerinti bontását.

![sávdiagram][10]
 
A következő információs diagramok a naplókban szereplő NSG-k adatait, az időszak során rögzített folyamatok számát és a legkorábbi rögzített napló dátumát jelenítik meg. Ez az információ képet ad arról, hogy milyen NSG-ket naplóznak, és milyen dátumtartományt tartalmaz a folyamatok.

![infodiagram1][11]

![infodiagram2][12]

Ez a sablon a következő szeletelőket tartalmazza, amelyek lehetővé teszik, hogy csak azönt érdeklő adatokat tekintse meg. Szűrhet az erőforráscsoportokra, az NSG-kre és a szabályokra. Szűrhet 5-tuple információkra, döntésre és a napló írásának időpontjára is.

![Szeletelők][13]

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben megmutattuk, hogy a Network Watcher és a Power BI által biztosított Network Security Group Flow naplók használatával képesek vagyunk megvizelni és megérteni a forgalmat. A megadott sablon használatával a Power BI közvetlenül letölti a naplókat a tárolóból, és helyileg dolgozza fel azokat. A sablon betöltéséhez szükséges idő a kért fájlok számától és a letöltött fájlok teljes méretétől függ.

Nyugodtan testre szabhatja ezt a sablont az Ön igényeinek. A Power BI számos módon használható a hálózati biztonsági csoport folyamatnaplóival. 

## <a name="notes"></a>Megjegyzések

* A naplók alapértelmezés szerint a`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Ha más adatok is léteznek egy másik könyvtárban, módosítani kell az adatok lekérése és feldolgozása érdekében lekérik és feldolgozzák az adatokat.

* A megadott sablon nem ajánlott 1 GB-nál több naplóval való használatra.

* Ha nagy mennyiségű naplóval rendelkezik, azt javasoljuk, hogy vizsgálja meg a megoldást egy másik adattár, például a Data Lake vagy az SQL-kiszolgáló használatával.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan jelenítheti meg az NSG-folyamatnaplókat a rugalmas veremsegítségével, ha ellátogat a [Visualize Azure Network Watcher NSG folyamatnaplóiba nyílt forráskódú eszközök használatával](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
