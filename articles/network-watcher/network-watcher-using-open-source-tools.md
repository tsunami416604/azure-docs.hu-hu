---
title: Hálózati forgalmi minták megjelenítése nyílt forráskódú eszközökkel
titleSuffix: Azure Network Watcher
description: Ez a lap azt ismerteti, hogyan használhatja a Network Watcher csomagrögzítést a Capanalysis segítségével a virtuális gépekre és a virtuális gépekről érkező forgalmi minták megjelenítéséhez.
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
ms.openlocfilehash: f36db28b58cd57b6407019b378a82632aa6c6228
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840655"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>A virtuális gépekre érkező és onnan induló hálózati forgalmi minták megjelenítése nyílt forráskódú eszközökkel

A csomagrögzítések olyan hálózati adatokat tartalmaznak, amelyek lehetővé teszik a hálózati kriminalisztika és a mély csomagvizsgálat elvégzését. Számos megnyitható forráseszköz van, amelyekkel elemezheti a csomagrögzítéseket, hogy betekintést nyerjen a hálózatba. Az egyik ilyen eszköz a CapAnalysis, egy nyílt forráskódú csomagrögzítő vizualizációs eszköz. A csomagrögzítési adatok megjelenítése értékes módja annak, hogy gyorsan betekintést nyerjen a hálózaton belüli mintákba és anomáliákba. A vizualizációk az ilyen elemzések egyszerű elfogyasztható módon történő megosztására is szolgálnak.

Az Azure Network Watcher lehetővé teszi az adatok rögzítését azáltal, hogy lehetővé teszi a csomagrögzítések elvégzését a hálózaton. Ez a cikk bemutatja, hogyan vizualizálhatja és nyerhet betekintést a csomagrögzítésekből a CapAnalysis és a Network Watcher használatával.

## <a name="scenario"></a>Forgatókönyv

Van egy egyszerű webalkalmazás üzembe helyezett egy virtuális gép az Azure-ban szeretné használni a nyílt forráskódú eszközöket a hálózati forgalom megjelenítéséhez a folyamatminták és az esetleges anomáliák gyors azonosításához. A Network Watcher segítségével beszerezheti a hálózati környezet csomagrögzítését, és közvetlenül tárolhatja azt a tárfiókban. A CapAnalysis ezután közvetlenül a tárolóblobból tudja beadni a csomagrögzítést, és megjeleníti annak tartalmát.

![forgatókönyv][1]

## <a name="steps"></a>Lépések

### <a name="install-capanalysis"></a>CapAnalysis telepítése

A CapAnalysis virtuális gépre való telepítéséhez itt https://www.capanalysis.net/ca/how-to-install-capanalysisolvashat a hivatalos utasításokról.
A CapAnalysis távoli eléréséhez meg kell nyitnia a 9877-es portot a virtuális számítógépen egy új bejövő biztonsági szabály hozzáadásával. A szabályok hálózatbiztonsági csoportokban való létrehozásáról a [Szabályok létrehozása meglévő NSG-ben](../virtual-network/manage-network-security-group.md#create-a-security-rule)című részben tájékforalamizhat. A szabály sikeres hozzáadása után a CapAnalysis-et el kell tudnia tudni érni a`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Csomagrögzítési munkamenet indítása az Azure Network Watcher használatával

A Network Watcher lehetővé teszi a csomagok rögzítését a virtuális gépeken lévő és azon kívüli forgalom nyomon követéséhez. A [Csomagrögzítések kezelése a Hálózatfigyelővel](network-watcher-packet-capture-manage-portal.md) című útmutatóban a csomagrögzítési munkamenet elindításához olvassa el a figyelési munkamenetet. A csomagrögzítés tárolható egy tárolási blob ban, hogy a CapAnalysis elérhesse.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Csomagrögzítés feltöltése a CapAnalysis alkalmazásba
Közvetlenül feltöltheti a hálózati figyelő által készített csomagrögzítést az "Importálás URL-ről" lapon, és megadhat egy hivatkozást a csomagrögzítést tárolóblobra.

CapAnalysis hivatkozás ának megadásakor győződjön meg arról, hogy hozzáfűzi a SAS-jogkivonatot a storage blob URL-címéhez.  Ehhez keresse meg a megosztott hozzáférésű aláírást a tárfiókból, jelölje ki az engedélyezett engedélyeket, és nyomja meg a SAS létrehozása gombot egy jogkivonat létrehozásához. Ezután hozzáfűzheti a SAS-jogkivonatot a csomaglefoglalási storage blob URL-címéhez.

Az eredményül kapott URL a következő URL-hez hasonlóan fog kinézni:http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Csomagrögzítések elemzése

A CapAnalysis különböző lehetőségeket kínál a csomagrögzítés megjelenítésére, amelyek mindegyike más szemszögből biztosítja az elemzést. Ezekkel a vizuális összefoglalókkal megismerheti a hálózati forgalmi trendeket, és gyorsan észlelhet bármilyen szokatlan tevékenységet. Néhány ilyen funkció az alábbi listában látható:

1. Folyamattáblázatok

    Ez a táblázat a csomagadatok folyamatainak listáját, a folyamatokhoz társított időbélyegzőt és a folyamathoz társított különböző protokollokat, valamint a forrás- és cél IP-címet tartalmazza.

    ![capanalysis folyamatoldal][5]

1. Protokoll – áttekintés

    Ez az ablaktábla lehetővé teszi a hálózati forgalom elosztásának gyors megtekintését a különböző protokollokon és földrajzi területeken.

    ![capanalysis protokoll – áttekintés][6]

1. Statisztika

    Ez az ablaktábla lehetővé teszi a hálózati forgalmi statisztikák megtekintését – a forrás- és cél IP-ktől küldött és fogadott bájtok, a forrás- és célIP-ek folyamatai, a különböző folyamatokhoz használt protokoll, valamint a folyamatok időtartama.

    ![capanalysis statisztika][7]

1. Földrajzi térkép

    Ez az ablaktábla térképnézetet biztosít a hálózati forgalomról, a színek pedig az egyes országokból/régiókból érkező forgalom mennyiségére vannak skálázva. A kiemelt országok/régiók kiválasztásával további folyamatstatisztikákat tekinthet meg, például az adott országban/régióban az IP-ktől küldött és fogadott adatok arányát.

    ![geotérkép][8]

1. Szűrők

    A CapAnalysis szűrők készletét biztosítja az egyes csomagok gyors elemzéséhez. Például választhatja az adatok protokoll szerinti szűrését, hogy a forgalom adott részhalmazáról konkrét információkhoz jusson.

    ![szűrők][11]

    Látogasson [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) el, ha többet szeretne megtudni a CapAnalysis összes képességéről.

## <a name="conclusion"></a>Összegzés

A Network Watcher csomagrögzítési funkciója lehetővé teszi a hálózati kriminalisztika elvégzéséhez szükséges adatok rögzítését és a hálózati forgalom jobb megértését. Ebben a forgatókönyvben megmutattuk, hogyan lehet a Network Watcher csomagrögzítéseit könnyen integrálni a nyílt forráskódú vizualizációs eszközökkel. A csomagok rögzítésének megjelenítéséhez nyílt forráskódú eszközök, például a CapAnalysis használatával mély csomagvizsgálatot végezhet, és gyorsan azonosíthatja a hálózati forgalom trendjeit.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az NSG-folyamatnaplókról, látogasson el az [NSG Flow naplókba](network-watcher-nsg-flow-logging-overview.md)

Megtudhatja, hogy miként jelenítheti meg az NSG-folyamatnaplókat a Power BI-val a [Visualize NSG-folyamatok naplóinak megjelenítéséhez a Power BI-val](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
