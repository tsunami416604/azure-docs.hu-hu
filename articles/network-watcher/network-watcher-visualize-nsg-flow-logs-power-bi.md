---
title: "A Power BI visualizing Azure hálózati biztonsági csoport folyamata naplók |} Microsoft Docs"
description: "Ezen a lapon jelenítheti meg NSG folyamata naplók és a Power BI ismerteti."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 19bd7ed4bab915d7918a192a046653666cfaa498
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualizing hálózati biztonsági csoport folyamata naplók és a Power bi-ban

Hálózati biztonsági csoport folyamata naplók lehetővé teszik a bemenő és kimenő IP-forgalom vonatkozó információk megtekintése a hálózati biztonsági csoportok. Ezek egymást követő naplók megjelenítése vonatkozik a kimenő és bejövő forgalom alapon egy szabályt, a hálózati adapter a folyamatot, 5 rekordos információ a folyamattal (forrás vagy a cél IP, forrás vagy a cél Port Protocol), és ha a forgalom lett engedélyez vagy tilt.

A naplófájlok manuálisan keresve dcu adatbázisába folyamata naplózási adatok nehézkes lehet. Ez a cikk azt jelenítheti meg az utolsó folyamat-naplók és a hálózati forgalom megismerése megoldást nyújt.

## <a name="scenario"></a>Forgatókönyv

Az alábbi példa nem csatlakozni a Power BI desktop a tárfiók jelenleg konfigurált, a gyűjtő az NSG Flow naplózási adatok. Azt a kapcsolódás után a tárfiókhoz a Power BI tölti le, és a elemzi a forgalmat hálózati biztonsági csoportok által naplózott vizuális ábrázolását adja meg a naplókat.

A látványelemek, tekintse meg a sablonban megadott használatával:

* Felső Talkers
* Adatsorozat Flow időadatok irányt vagy szabály döntése alapján
* Hálózati illesztő MAC-cím alapján adatfolyamok
* NSG-t és a szabály által adatfolyamok
* Folyamatok által célport

A megadott sablon szerkeszthető, így módosíthatja úgy, hogy az új adatok, a látványelemek, vagy szerkessze a lekérdezéseket az igényeinek.

## <a name="setup"></a>Beállítás

Mielőtt hozzákezd, rendelkeznie kell hálózati biztonsági csoport Flow naplózás engedélyezve van egy vagy több hálózati biztonsági csoportok a fiókjában. Hálózati biztonsági engedélyezésével kapcsolatos flow a naplókat, a következő cikk tartalmazza: [folyamata naplózási a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).

A Power BI Desktop-ügyféllel a gépen, és elég szabad hely a letöltése és betölteni a napló adatait, hogy létezik-e a tárfiókban lévő számítógépre is rendelkeznie kell.

![Visio diagram][1]

### <a name="steps"></a>Lépések

1. Töltse le és nyissa meg a következő Power BI-sablont a Power BI Desktop alkalmazás [hálózati figyelő Power bi folyamata naplózza sablon](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Adja meg a szükséges lekérdezés-paraméterek
    1. **StorageAccountName** – Megadja azt az NSG-t szeretné betölteni, és megjelenítheti folyamata naplók tartalmazó storage-fiók nevét.
    1. **NumberOfLogFiles** – Itt adhatja meg, amelyeket szeretne letölteni, és megjelenítheti a Power BI fájlok száma. Ha 50 van megadva, például az 50 legfrissebb naplófájlokat. FF tudunk 2 NSG-k engedélyezve és konfigurálva van az NSG folyamata naplókat küld ennek a fióknak, majd a naplók az elmúlt 25 óra lehet megtekinteni.

    ![a Power BI fő][2]

1. Adja meg a tárfiók elérési kulcsának. Érvényes elérési kulcsok nyissa meg a storage-fiókot az Azure portál és választ talál **hívóbetűk** a beállítások menüből. Kattintson a **Connect** majd a módosítások alkalmazásához.

    ![Tárelérési kulcsok][3]

    ![hozzáférési kulcs 2][4]

4.  A naplók letöltéséhez és értelmezni, és most már használhatja a korábban létrehozott látványelemek.

## <a name="understanding-the-visuals"></a>A látványelemek ismertetése

A sablonban megadott látványelemek, amelyek segítenek az NSG Flow naplóadatokat célszerű olyan. A következő ábrákon az irányítópult néz amikor feltöltve adatokkal mintát láthatók. Az alábbiakban azt vizsgálja meg az egyes visual nagyobb részletességgel 

![Power BI][5]
 
A felső Talkers visual azt mutatja be az IP-címek, amelyek az adott időszakban a legtöbb kapcsolatok kezdeményezett megadott. A listák összhangban kapcsolatok relatív száma. 

![toptalkers][6]

A következő alkalommal adatsorozat diagramokon megjelenítése az adott időszakban adatfolyamok száma. A felső grafikon által a folyamat iránya van szegmentált, és az alacsonyabb van szegmentált által végrehajtott (engedélyezése vagy megtagadása) alkalmazással. A Megjelenítés választhat vizsgálja meg a forgalom trendek az idő múlásával és bármely rendellenes igényeiben jelentkező direkt vagy elutasítja a forgalom vagy forgalom szegmentálását.

![flowsoverperiod][7]

Hálózati adapterenként, a folyamat iránya és az alacsonyabb szegmentált felső folyamatok által végzett döntési szegmentált következő diagramjait megjelenítése. Az információ, amely a virtuális gépek ezekről a legtöbb más viszonyítva, és ha folyamatban van-e egy adott virtuális gép férhet engedélyez vagy tilt.

![flowspernic][8]

A következő fánk kerék diagram által célport adatfolyamok részletes információkat jeleníti meg. Az információ megtekintheti a leggyakrabban használt célport használja a megadott időn belül.

![Fánk][9]

A következő sávdiagram szemlélteti az NSG-t és a szabály által. Az információ megtekintheti az NSG-ket a legtöbb forgalmat, és a forgalom felosztása felelős egy NSG-szabály által.

![barchart][10]
 
A következő tájékoztató diagramokon szerepel a naplókat, az időszak, és a legkorábbi naplóban rögzített dátumának rögzített adatfolyamok száma NSG-ket információkat jelenítenek meg. Ezt az információt lehetővé teszi egy meghatározni, hogy milyen NSG-k vannak naplózott és a forgalom dátumtartományt.

![infochart1][11]

![infochart2][12]

Ez a sablon tartalmazza a következő szeletelők használatával csak a leginkább megváltozása adatok megtekintéséhez. Az erőforráscsoportok, NSG-ket és szabályok alapján szűrhet. 5 rekordos információt, döntést és az idő, a napló írása történt is végezhet.

![a szeletelők][13]

## <a name="conclusion"></a>Összegzés

Jelenleg mutatott ebben a forgatókönyvben, hogy a hálózati figyelőt, és a Power BI által biztosított hálózati biztonsági csoport Flow naplókat, azt el tudják jelenítheti meg, és megismerheti a forgalmat. A megadott sablont használ, a Power BI tölti le a naplók közvetlenül a tárolási és dolgozza fel a helyileg. Sablon betöltéséhez szükséges idő függ a kért fájlok száma, és a fájlok összesített mérete tölti le.

Nyugodtan szabja testre a sablon az igényeinek. Számos módon számos használható a Power BI és hálózati biztonsági csoport Flow naplófájlokat. 

## <a name="notes"></a>Megjegyzések

* Alapértelmezés szerint naplója`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Ha más adatok ezeket a lekérdezéseket a lehívásos és a folyamat az adatok lehet módosítani egy másik címtárban már létezik.

* A megadott sablon használata nem ajánlott a naplók több mint 1 GB.

* Ha nagy mennyiségű naplókat, azt javasoljuk, hogy a használatával, például a Data Lake vagy SQL server egy másik adattároló megoldás vizsgálata.

## <a name="next-steps"></a>Következő lépések

Útmutató: az NSG-folyamat naplók megjelenítése a Elastick veremmel rendelkező ellátogatva [megjelenítése Azure hálózati figyelő NSG folyamata naplók nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
