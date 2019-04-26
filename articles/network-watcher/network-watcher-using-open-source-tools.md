---
title: Nyílt forráskódú eszközök és az Azure Network Watcherrel hálózati forgalmi minták vizualizációja |} A Microsoft Docs
description: Ezen a lapon Capanalysis a Network Watcher packet capture használva a virtuális gépek forgalmi minták vizualizációja ismerteti.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7c239bbf577645ddb8ab12c525d1b3a8832421df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680532"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Hálózati forgalmi minták a nyílt forráskódú eszközöket használó virtuális gépek megjelenítése

Csomagrögzítés hajthat végre a hálózati eseményadatokhoz és mély Csomagvizsgálat hálózati adatokat tartalmaznak. Nincsenek számos megnyílik forráskódú eszközök használatával csomagrögzítések juthat hozzá a hálózatához. Egy ilyen eszköz CapAnalysis, egy nyílt forráskódú csomag rögzítési vizualizációs eszköz. Vizualizációja packet capture módja a értékes gyorsan hozzájut a mintákat és rendellenességeket a hálózaton belül. Vizualizációk egy azt jelenti, hogy az ilyen elemzések megosztása könnyen értelmezhető módon is biztosítanak.

Az Azure Network Watcher lehetővé teszi azáltal, hogy csomagrögzítés végrehajtása a hálózaton lévő adatok rögzítéséhez. Ez a cikk ismerteti, egy az útmutató bemutatja, hogyan megjelenítése és értékes információkhoz juthatnak csomag rögzíti, és a Network Watcher CapAnalysis használatával.

## <a name="scenario"></a>Forgatókönyv

Rendelkezik egy egyszerű webalkalmazást üzembe helyezett virtuális gépen az Azure nyílt forráskódú eszközök segítségével jelenítheti meg a hálózati forgalmat, a flow minták és minden lehetséges hibák gyors azonosítását. A Network Watcher szerezze be a hálózati környezet csomagrögzítés, és közvetlenül a tárfiókban tárolja. CapAnalysis ezután képes feldolgozni a csomagrögzítés az közvetlenül a storage-blobból, és megjelenítheti annak tartalmát.

![forgatókönyv][1]

## <a name="steps"></a>Lépések

### <a name="install-capanalysis"></a>CapAnalysis telepítése

CapAnalysis telepíthető egy virtuális gépet, tekintse meg a hivatalos utasításokat https://www.capanalysis.net/ca/how-to-install-capanalysis.
Sorrendben CapAnalysis távoli kezeléséhez a tártallózónak, meg kell nyitnia a portot 9877 a virtuális Gépen egy új bejövő biztonsági szabály hozzáadásával. Hálózati biztonsági csoportokban szabályok létrehozásával kapcsolatos további információkért tekintse meg [szabályok létrehozása egy létező NSG-ben](../virtual-network/manage-network-security-group.md#create-a-security-rule). A szabály sikeres hozzáadása után kell tudni hozzáférni a CapAnalysis `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Használja az Azure Network Watcher packet capture munkamenet indításához

A Network Watcher lehetővé teszi, hogy a csomagok nyomon a virtuális gépek és gépből irányuló forgalom rögzítése. Olvassa el a utasításait [kezelés csomagrögzítéseket gyűjtsenek és a Network Watcher](network-watcher-packet-capture-manage-portal.md) packet capture munkamenet indításához. Csomagrögzítés tárolható egy storage-blobba CapAnalysis kell hozzáférniük.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Csomagrögzítés CapAnalysis feltöltése
A network watcher "Importálása URL-CÍMRŐL" lapján, és biztosítják, hogy a storage-blobba hivatkozás által készített a csomagrögzítés tároló csomagrögzítés közvetlenül feltölthet.

Egy hivatkozást a CapAnalysis megadásakor ügyeljen arra, hogy egy SAS-token hozzáfűzése a storage blob URL-címe.  Ehhez közös hozzáférésű jogosultságkódot nyissa meg a storage-fiókból, a használható engedélyek kijelölése és a SAS létrehozása gombra kattintva hozzon létre egy token. A SAS-jogkivonat majd hozzáfűzése a csomag capture storage blob URL-címe.

A megjelenő URL-cím a következőhöz hasonlóan a következő URL-címet fog kinézni: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Elemzésekor csomagrögzítéseket gyűjtsenek

CapAnalysis megjelenítése a csomagrögzítés, minden más szempontból biztosító elemzés különféle lehetőségeket kínál. Az alábbi vizuális összegzéseket a hálózati forgalom trendek megismerésével, és gyorsan azonosíthatják a kapcsolatos szokatlan tevékenységet. Ezek közül néhányat a következők láthatók:

1. Flow-táblák

    Ez a táblázat lehetővé teszi a csomagadatok, az időbélyeg társított folyamatok és a különböző protokollok, a folyamat, valamint a forrás és cél IP társított folyamatok listájában.

    ![capanalysis folyamat lap][5]

1. Protokoll – áttekintés

    Ez a panel lehetővé teszi, hogy gyorsan megtekintheti a hálózati forgalom eloszlását különféle protokollokat és földrajzi területek számára.

    ![capanalysis protokoll – áttekintés][6]

1. Statisztika

    Ezen a panelen megtekintheti a hálózati forgalom statisztikák – bájt küldött, és kapott a forrás és cél IP-címek, a forrás és cél IP-címek, minden flow protokollt használja a különböző folyamatok és folyamatok időtartama a teszi lehetővé.

    ![capanalysis statisztika][7]

1. Földrajzi térképet

    Ezen a panelen biztosít egy térkép-nézet a hálózati forgalom a színek az egyes országok méretezési lehetőségek érhetők el a forgalom mennyisége. Kiválaszthatja például az az időarány, amíg az adatok küldött és fogadott az adott országban IP-címekről további folyamat statisztika megtekintése kijelölt országokban.

    ![földrajzi térképet][8]

1. Szűrők

    CapAnalysis biztosít a szűrők az adott csomagok gyors elemzés céljából. Például lehet váltani a forgalom része az adott dcu protokoll által az adatok szűrésére.

    ![szűrők][11]

    Látogasson el [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) összes CapAnalysis képességeivel kapcsolatos további.

## <a name="conclusion"></a>Összegzés

A Network Watcher packet capture funkciójának lehetővé teszi, hogy a szükséges hálózati forensics végez, és értelmezheti a hálózati forgalom adatok rögzítését. Ebben a forgatókönyvben megmutattuk, hogyan csomagrögzítés a Network Watcher egyszerűen integrálható nyílt forráskódú adatábrázolási eszközök. Nyílt forráskódú eszközök, például a CapAnalysis használatával jelenítheti meg a csomagok rögzíti, mély Csomagvizsgálat, és gyorsan azonosíthatja a trendeket belül a hálózati forgalmat.

## <a name="next-steps"></a>További lépések

NSG-Folyamatnaplók kapcsolatos további információkért látogasson el [NSG-Folyamatnaplók](network-watcher-nsg-flow-logging-overview.md)

Ismerje meg, hogyan jelenítheti meg az NSG-Folyamatnaplók Power BI-jal funkcionáló [megjelenítése NSG-forgalomnaplók Power BI-jal](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
