---
title: A Power bi-JAL visualizing Azure-beli hálózati biztonsági csoport folyamatnaplóit |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogyan jelenítheti meg az NSG-Folyamatnaplók Power BI-jal.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 6df49f9cd308f4bb9b1fef6e5860872526ce8bb7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079026"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>A Power bi-JAL visualizing hálózati biztonsági csoport folyamatnaplóit

Hálózati biztonsági csoportok folyamatnaplóit lehetővé teszik a bejövő és kimenő IP-forgalomra vonatkozó információk megtekintése a hálózati biztonsági csoportok. Ezek a flow naplók megjelenítése / szabály történik, a hálózati Adaptert a flow a kimenő és bejövő folyamatok vonatkozik, az 5-ször több információt a folyamat (forrás és a cél IP-Címek használatához forrás és a cél-Port, protokoll), és ha a forgalom engedélyezett vagy tiltott.

Elemezheti a naplófájlok manuálisan kereséssel flow naplózási adatok nehezen lehet. Ez a cikk azt a legutóbbi forgalmi naplók megjelenítése, és ismerje meg a hálózati forgalom megoldást nyújtanak.

> [!Warning]  
> Az alábbi lépéseket a flow 1-es naplók verzió együttműködve. További információkért lásd: [csoportforgalom naplózása a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md). Az alábbi utasítások nem fog működni a naplófájlokat, módosítás nélküli 2. verzióban.

## <a name="scenario"></a>Forgatókönyv

A következő esetben a Power BI desktopban a storage-fiókba, a gyűjtő konfiguráltunk az NSG-folyamat naplózás adatokhoz csatlakozunk. Csatlakozunk a storage-fiók, miután a Power BI tölti le, és elemzi a naplókat, adja meg a vizuális megjelenítését, a forgalom hálózati biztonsági csoportok által naplózott.

A Vizualizációk, tekintse meg a sablonban megadott használata:

* Fő kapcsolattartók
* Idősorok forgalmi adatai irány és szabálymeghatározás szerint
* Folyamatok hálózati adapter MAC-címe szerint
* NSG-t és a szabály által folyamatok
* Folyamatok célport szerint

A megadott sablon az szerkeszthető, így módosíthatja, hogy az új adatok, a Vizualizációk, vagy igény szerint lekérdezések szerkesztése.

## <a name="setup"></a>Beállítás

Mielőtt hozzákezd, rendelkeznie kell hálózati biztonsági csoport Flow naplózás engedélyezve van egy vagy több hálózati biztonsági csoportok a fiókjában található. A hálózati biztonságot forgalom naplóinak engedélyezéséről útmutatásért tekintse meg a következő cikket: [Bevezetés a hálózati biztonsági csoportok csoportforgalom naplózása](network-watcher-nsg-flow-logging-overview.md).

A Power BI Desktop-ügyfél telepítve a gépre, és töltse le és betölteni a naplóadatokat, hogy a tárfiók létezik a számítógép elegendő szabad lemezterülettel kell rendelkeznie.

![Visio-diagram][1]

### <a name="steps"></a>Lépések

1. Töltse le és nyissa meg a következő Power BI-sablon a Power BI Desktop alkalmazás [a Power bi Network Watcher flow-sablon naplók](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Adja meg a szükséges lekérdezési paraméterek
   1. **StorageAccountName** – tartalmazó az NSG-Folyamatnaplók, amelyeket szeretne betölteni, és jelenítheti meg a tárfiók nevét határozza meg.
   1. **NumberOfLogFiles** – számát határozza meg, töltse le és a Power bi-ban jelenítheti meg szeretné naplófájlok. Ha például 50 meg van adva a 50 legfrissebb naplófájlokat. Ha 2 NSG-k engedélyezve és konfigurálva van az NSG-Folyamatnaplók küldeni ennek a fióknak, majd a naplók az elmúlt 25 óra is megtekinthetők.

      ![power BI main][2]

1. Adja meg a tárfiók hozzáférési kulcsára. Annak érvényes hozzáférési kulcsok az Azure portal és a kiválasztja a tárfiókba lépve **Tárelérési kulcsok** beállítások menüjében. Kattintson a **Connect** majd alkalmazza a módosításokat.

    ![Hozzáférési kulcsok][3]

    ![hozzáférési kulcs 2][4]

4. A naplók letöltése és elemzése, és most már használhat az előzetesen létrehozott Vizualizációk.

## <a name="understanding-the-visuals"></a>A Vizualizációk ismertetése

A sablonban megadott olyan, vizualizációkat, amelyek segítenek az NSG-folyamat naplóadatok jelentéssel bírnak. A következő képen az irányítópult néz ki adatokkal feltöltve amikor mintát. Az alábbiakban vizsgáljuk meg részletesebben egyes Vizualizációk 

![powerbi][5]
 
A felső Talkers visual azt mutatja be az IP-címek, amelyek a legtöbb kapcsolat időszak alatt kezdeményezett megadott. A mezők mérete felel meg a relatív kapcsolatok száma. 

![toptalkers][6]

A következő time series diagramok megjelenítése a időszakban folyamatok száma. A felső diagramon alapján a folyamat iránya van szegmentált, és az alacsonyabb a döntést (engedélyezése vagy megtagadása) alapján van szegmentált. Ezzel a vizualizációval is vizsgálja meg a forgalom trendek az idő múlásával és bármilyen szokatlan csúcsok direkt vagy elutasítja a forgalom vagy forgalomelkülönítésnek.

![flowsoverperiod][7]

A folyamatok száma hálózati adapterenként, a folyamat iránya, és az alacsonyabb alapján szegmentált felső szegmentált által végzett következő diagramok megjelenítése. Ezen adatok mélyebb bepillantást nyerhet a virtuális gépek, amelyek a legtöbb más viszonyítva továbbítani, és ha folyamatban van-e a forgalmat egy adott virtuális géphez engedélyezett vagy tiltott.

![flowspernic][8]

A következő kerék fánkdiagram információkat a folyamatok célport szerint jeleníti meg. Ezekkel az információkkal megtekintheti a leggyakrabban használt célportok használja a megadott időn belül.

![fánkdiagram][9]

A következő sávdiagram látható a folyamat az NSG-t és a szabály által. Ezekkel az információkkal az NSG-k felelősek a legtöbb forgalmat, és a forgalom áttekintését láthatja az NSG-KET a szabály által.

![barchart][10]
 
A következő tájékoztató diagramok az NSG-t a naplókat, az időszak, valamint a dátum a legkorábbi napló rögzített rögzített folyamatok száma szerepel információkat jelenítenek meg. Ezt az információt biztosítja képet milyen NSG-ket a rendszer a naplózott és folyamatok dátumtartománya.

![infochart1][11]

![infochart2][12]

Ez a sablon tartalmazza a következő szeletelők használatával csak a leginkább érdekli az adatok megtekintéséhez. Az erőforráscsoportok, az NSG-k és a szabályok szűrésével. 5-ször több információt, a döntést és az idő a napló íródott is végezhet.

![Szeletelők][13]

## <a name="conclusion"></a>Összegzés

Megmutattuk ebben a forgatókönyvben, hogy a Network Watcher és a Power BI által biztosított hálózati biztonsági csoport Flow naplók segítségével tudjuk jelenítheti meg, és ismerje meg a forgalmat. A megadott sablont használ, a Power BI a naplók közvetlenül a storage-ból letöltött, és feldolgozza őket helyileg. A sablon betöltésének ideje kért fájlok számától függően változik, és a letöltött fájlok összesített mérete.

Nyugodtan szabja testre a sablon az igényeinek. Számos módon számos, a Power BI használható az hálózati biztonsági csoport Flow naplókat. 

## <a name="notes"></a>Megjegyzések

* A naplók alapértelmezés szerint be vannak tárolva `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Ha más adatok állnak rendelkezésre azok a lekérdezések a lehívásos és a folyamat az adatok módosítani kell egy másik címtárban.

* A megadott sablon nem ajánlott a több mint 1 GB-naplók segítségével.

* Ha nagy mennyiségű naplók, azt javasoljuk, hogy megvizsgálja egy megoldást egy másik adattárban, például a Data Lake- vagy SQL server használatával.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan jelenítheti meg az NSG-Folyamatnaplók az Elastic Stackkel végzett funkcionáló [megjelenítése Azure Network Watcher NSG folyamatnaplóit, nyílt forráskódú eszközök használatával](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
