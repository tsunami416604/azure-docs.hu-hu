---
title: Hálózati forgalmának mintáit nyílt forráskódú eszközök és az Azure hálózati figyelőt megjelenítése |} Microsoft Docs
description: Ez a lap ismerteti Capanalysis megjelenítéséhez a virtuális gépek érkező vagy oda irányuló forgalmat hálózati figyelőt csomagrögzítéssel használata.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 7b1e1383e8e244a7cdb30be1e08514a6a4dd7b14
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302233"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Hálózati forgalmának mintáit felé és felől a virtuális gépek nyílt forráskódú eszközökkel megjelenítése

Csomag rögzítésekre hajthat végre a hálózati vizsgálatokhoz és a mély Csomagvizsgálat hálózati adatokat tartalmaznak. Nincsenek sok megnyílik csomag rögzíti a hálózattal kapcsolatos dcu elemzéséhez használható forrás-eszközöket. Egy ilyen eszköz CapAnalysis, egy nyílt forráskódú csomag rögzítési képi megjelenítés eszköz. Gyorsan kapcsolattípusokból megoldások és a hálózaton belüli rendellenességeket elemzések hasznos eszközök csomag rögzítési adatok megjelenítése. Képi megjelenítések is biztosít egy ilyen insights megosztási egyszerűen felhasználhatóvá módon.

Azure hálózati figyelőt adatok rögzítéséhez azzal, hogy lehetővé teszi a végrehajtását csomag rögzíti a hálózat lehetőséget nyújt. Ez a cikk ismerteti, hogy a lépésein végighaladva jelenítheti meg, és információt kaphat a csomagból rögzíti CapAnalysis használata a hálózati figyelőt.

## <a name="scenario"></a>Forgatókönyv

Rendelkezik egy egyszerű webalkalmazást telepített egy Azure-ban jelenítheti meg a hálózati forgalmat, és gyorsan azonosíthatja a folyamat mintákat és bármely esetleges rendellenességeket nyílt forrású eszközök segítségével. A hálózati figyelőt szerezzen be hálózati környezete csomag rögzítőeszközt, és közvetlenül a tárfiók tárolja. CapAnalysis ezután a csomagrögzítéssel az közvetlenül a tárolási blobból betöltési és jelenítheti meg annak tartalmát.

![forgatókönyv][1]

## <a name="steps"></a>Lépések

### <a name="install-capanalysis"></a>CapAnalysis telepítése

CapAnalysis telepítése virtuális gépen, olvassa el a hivatalos utasításokat itt https://www.capanalysis.net/ca/how-to-install-capanalysis.
Ahhoz, távolról fér hozzá CapAnalysis, meg kell nyitnia a virtuális gép 9877 portjához új bejövő szabály hozzáadásával. A hálózati biztonsági szabályok létrehozásával kapcsolatban bővebben lásd [egy meglévő NSG-szabályok létrehozására](../virtual-network/manage-network-security-group.md#create-a-security-rule). A szabály sikeres hozzáadása után kell tudni elérni a CapAnalysis `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Egy csomag rögzítési munkamenet elindításához használt Azure hálózati figyelőt

Hálózati figyelő lehetővé teszi a csomagok és nyomon követheti a forgalom mindkét virtuális gép rögzítése. Olvassa el a következő utasításokat [kezelése csomagrögzítéseket hálózati figyelőt](network-watcher-packet-capture-manage-portal.md) egy csomag rögzítési munkamenet elindításához. A csomagrögzítéssel CapAnalysis érhető el, a tárolási blob is tárolható.

### <a name="upload-a-packet-capture-to-capanalysis"></a>A csomagrögzítéssel CapAnalysis feltöltése
A csomagrögzítéssel meghoznia hálózati figyelőt "Importálása az URL" lapján, és a tárolási blob mutató hivatkozás biztosítása a csomagrögzítéssel tároló közvetlenül feltölthet.

Hivatkozás biztosítása CapAnalysis, ellenőrizze, SAS-jogkivonat hozzáfűzése a tárolási blob URL-CÍMÉT.  Ehhez a közös hozzáférésű jogosultságkód navigáljon a tárfiókból, jelölje ki az engedélyezett engedélyek és a SAS létre gombra kattintva hozzon létre egy tokent. A SAS-jogkivonat majd fűzze hozzá a csomag rögzítési tárolási blob URL-cím.

Az eredményül kapott URL-címet a következőhöz hasonlóan a következő URL-címet fog kinézni: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Rögzíti a elemzésekor csomag

CapAnalysis jelenítheti meg a csomagrögzítéssel, minden olyan elemzés különböző szempontjából különböző lehetőséget kínál. A vizuális összesítések a hálózati forgalom trendjeinek megértését, és gyorsan direkt szokatlan tevékenység. Ezek a szolgáltatások néhány az alábbi listában láthatók:

1. Attribútumfolyam táblák

    Ebben a táblázatban lehetővé teszi a csomagadatok, a tranzakciós társított időbélyegzőjét és a különböző protokollok, a folyamat, valamint a forrás és cél IP társított folyamatok listáját.

    ![capanalysis folyamat lap][5]

1. Protokoll – áttekintés

    Ezen az ablaktáblán lehetővé teszi, hogy gyorsan megtekintheti a különböző protokollok és földrajzi keresztüli hálózati forgalmat.

    ![capanalysis protokoll – áttekintés][6]

1. Statisztika

    Ezen az ablaktáblán lehetővé teszi a hálózati forgalom statisztikáit a következőképpen tekintheti – bájt küldött és protokollt használja a forrás és cél IP-címek, adatfolyamok mind a forrás és cél IP-címek, kapott különböző tranzakciók, és az adatfolyamok időtartama.

    ![capanalysis statisztikák][7]

1. geomap

    Ezen az ablaktáblán biztosít a nézet a hálózati forgalom az egyes országok forgalom mennyiségét méretezhetők színeivel. Kiválaszthatja a kijelölt országok további folyamata statisztikákról, például az adatok küldése és fogadása szükséges a IP-címeket az adott ország arányát megtekintéséhez.

    ![geomap][8]

1. Szűrők

    CapAnalysis biztosít az adott csomagok gyors elemzés szűrők. Például ha szeretné, a forgalom részhalmaz adott dcu protokoll által szűrje az adatokat.

    ![szűrők][11]

    Látogasson el [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) összes CapAnalysis képességeivel kapcsolatos további.

## <a name="conclusion"></a>Összegzés

Hálózati figyelőt csomag rögzítése funkció lehetővé teszi a hálózati Törvényszéki ellátásához, és jobb megértése érdekében a hálózati forgalom szükséges adatok rögzítéséhez. Ebben a forgatókönyvben azt lehet bemutatta, hogyan hálózati figyelőt csomagok készítését könnyen integrálható a nyílt forráskódú képi megjelenítés eszközök. Az adott nyílt forráskódú eszközökkel CapAnalysis például megjelenítheti a csomagok készítését, mély Csomagvizsgálat, és gyorsan azonosíthatja a trendeket belül a hálózati forgalmat.

## <a name="next-steps"></a>További lépések

NSG folyamata naplók kapcsolatos további információkért látogasson el a [naplózza az NSG-folyamat](network-watcher-nsg-flow-logging-overview.md)

Megtudhatja, hogyan jelenítheti meg az NSG folyamata naplók a Power BI ellátogatva [megjelenítése NSG forgalomáramlás naplók és a Power bi-ban](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
