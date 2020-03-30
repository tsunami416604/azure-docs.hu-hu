---
title: A hálózati erőforrások védelme az Azure Security Centerben
description: Ez a dokumentum az Azure Security Centerben található javaslatokkal foglalkozik, amelyek segítenek megvédeni az Azure hálózati erőforrásait, és megfelelnek a biztonsági szabályzatoknak.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77431485"
---
# <a name="protect-your-network-resources"></a>A hálózati erőforrások védelme
Az Azure Security Center folyamatosan elemzi az Azure-erőforrások biztonsági állapotát a hálózati biztonsággal kapcsolatos gyakorlati tanácsok érdekében. Amikor a Security Center potenciális biztonsági réseket azonosít, javaslatokat készít, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán az erőforrások megkeményedése és védelme érdekében.

Ez a cikk a Security Center erőforrás-biztonsági szakaszának **Hálózat** lapja.

A hálózatkezelésre vonatkozó javaslatok teljes listáját a Hálózati javaslatok című témakörben [található.](recommendations-reference.md#recs-network)

Ez a cikk az Azure-erőforrásokra hálózati biztonsági szempontból vonatkozó javaslatokkal foglalkozik. A hálózati javaslatok a következő generációs tűzfalak, a hálózati biztonsági csoportok, a JIT virtuálisgép-hozzáférés, a túlságosan megengedő bejövő forgalmi szabályok és egyebek köré épülnek. A hálózati javaslatok és a szervizelési műveletek listáját az Azure Security Center biztonsági javaslatainak kezelése ( [Kezelés) témakörben található.](security-center-recommendations.md)

> [!NOTE]
> A **Hálózatkezelés** lapon részletesen belevetheti magát az Azure-erőforrások állapotába a hálózat szempontjából. A hálózati térkép és az adaptív hálózati vezérlők csak az Azure Security Center standard szintű szintjén érhetők el. [Ha az ingyenes szintet használja, kattintson az **örökölt hálózati kapcsolatok megtekintése gombra,** és megkapja a hálózati erőforrás-javaslatokat.](#legacy-networking)
>

A **Hálózat** lap áttekintést nyújt a szakaszok akkor mélyen belemerül, hogy minél több információt az egészségügyi hálózati erőforrások:

- Hálózati térkép (csak az Azure Security Center standard szintű szintje)
- Adaptív hálózat-megerősítés
- Hálózati biztonsági javaslatok.
- Örökölt **hálózati** panel (az előző hálózati panel) 
 
[![Hálózat ablaktábla](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Hálózati térkép
Az interaktív hálózati térkép grafikus nézetet biztosít a biztonsági átfedésekkel, amelyek javaslatokat és elemzéseket tartalmaznak a hálózati erőforrások megerősítéséhez. A térkép segítségével láthatja az Azure-számítási feladatok hálózati topológiáját, a virtuális gépek és alhálózatok közötti kapcsolatokat, valamint a leásás képességét a térképről az adott erőforrásokba és az erőforrásokra vonatkozó javaslatokba.

A hálózati térkép megnyitása:

1. A Biztonsági központ Erőforrás-biztonsági higiénia csoportban válassza a **Hálózat lehetőséget.**
2. A **Hálózati térkép** csoportban kattintson a **Topológia megtekintése gombra.**
 
A topológiatérkép alapértelmezett nézete a következőket jeleníti meg:

- Az Azure-ban kiválasztott előfizetések. A térkép több előfizetést is támogat.
- Az Erőforrás-kezelő erőforrástípus virtuális gépei, alhálózatai és virtuális hálózatai (a klasszikus Azure-erőforrások nem támogatottak)
- Társviszony-létesített virtuális hálózatok
- Csak olyan erőforrások, amelyek magas vagy közepes súlyosságú [hálózati ajánlásokkal](security-center-recommendations.md) rendelkeznek  
- Internettel szemben álló erőforrások
- A térkép az Azure-ban kiválasztott előfizetésekhez van optimalizálva. Ha módosítja a kijelölést, a rendszer újraszámítja a térképet, és az új beállítások alapján újraoptimalizálja.  

[![Hálózati topológia térkép](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>A hálózati térkép ismertetése

A hálózati térkép az Azure-erőforrásokat **topológia** nézetben és **forgalmi** nézetben jelenítheti meg. 

### <a name="the-topology-view"></a>A topológia nézet

A hálózati térkép **Topológia** nézetében a hálózati erőforrásokkal kapcsolatos alábbi elemzéseket tekintheti meg:

- A belső kör, láthatja az összes virtuális hálózatok a kiválasztott előfizetések, a következő kör az összes alhálózatok, a külső kör az összes virtuális gépek.
- A térképen lévő erőforrásokat összekötő vonalak segítségével megtudhatja, hogy mely erőforrások vannak társítva egymáshoz, és hogyan épül fel az Azure-hálózat. 
- A súlyossági mutatók segítségével gyorsan áttekintést kaphat abiztonsági központ tól származó nyitott javaslatokról.
- Az erőforrások bármelyikére kattintva leáshat róluk, és közvetlenül megtekintheti az erőforrás részleteit és ajánlásait, valamint a hálózattérkép kontextusában.  
- Ha túl sok erőforrás jelenik meg a térképen, az Azure Security Center saját algoritmusa segítségével intelligens fürt az erőforrásokat, kiemelve az erőforrásokat, amelyek a legkritikusabb állapotban vannak, és a legmagasabb súlyossági javaslatokat. 

Mivel a térkép interaktív és dinamikus, minden csomópont kattintható, és a nézet a szűrők alapján változhat:

1. A hálózati térképen láthatókat a felső szűrők kel módosíthatja. A térkép a következők alapján fókuszálható:

   -  **Biztonsági állapot:** Szűrheti a térképet az Azure-erőforrások súlyossága (magas, közepes, alacsony) alapján.
   - **Javaslatok**: Kiválaszthatja, hogy mely erőforrások jelenjenek meg az alapján, hogy mely javaslatok aktívak ezeken az erőforrásokon. Megtekintheti például azokat az erőforrásokat, amelyekhez a Security Center a hálózati biztonsági csoportok engedélyezését javasolja.
   - **Hálózati zónák**: Alapértelmezés szerint a térkép csak az internetre néző erőforrásokat jeleníti meg, kiválaszthatja a belső virtuális gépeket is.
 
2. A bal felső sarokban a **Visszaállítás gombra** kattintva bármikor visszaállíthatja a térkép alapértelmezett állapotát.

Erőforrás leásása:

1. Amikor kiválaszt egy adott erőforrást a térképen, megnyílik a jobb oldali ablaktábla, és általános információkat ad az erőforrásról, a csatlakoztatott biztonsági megoldásokról, ha vannak ilyenek, és az erőforrásra vonatkozó javaslatokat. Ez az egyes kiválasztott erőforrástípusok viselkedése. 
2. Amikor az egérmutatót egy csomópont fölé viszi a térképen, megtekintheti az erőforrással kapcsolatos általános információkat, beleértve az előfizetést, az erőforrástípusát és az erőforráscsoportot.
3. A hivatkozás segítségével ráközelítsen az eszközhegyre, és összpontosítsa újra a térképet az adott csomópontra. 
4. Ha a térképet egy adott csomóponttól távol szeretné elirányítani, kicsinyítse le.

### <a name="the-traffic-view"></a>A Forgalom nézet

A **Forgalom** nézet az erőforrások közötti összes lehetséges forgalom térképét tartalmazza. Ez vizuális térképet biztosít az összes konfigurált szabályról, amely meghatározza, hogy mely erőforrások kommunikálhatnak kivel. Ez lehetővé teszi a hálózati biztonsági csoportok meglévő konfigurációjának megtekintését, valamint a számítási feladatokon belüli lehetséges kockázatos konfigurációk gyors azonosítását.

### <a name="uncover-unwanted-connections"></a>Nem kívánt kapcsolatok feltárása

A nézet erőssége abban relevancia, hogy képes megjeleníteni ezeket az engedélyezett kapcsolatokat a meglévő biztonsági résekkel együtt, így az adatok ezen keresztmetszetének segítségével elvégezheti az erőforrások szükséges megerősítését. 

Például észlelheti, hogy két gép, amely nem volt tudatában lehet kommunikálni, lehetővé téve, hogy jobban elkülönítse a számítási feladatok és alhálózatok.

### <a name="investigate-resources"></a>Erőforrások vizsgálata

Erőforrás leásása:

1. Amikor kiválaszt egy adott erőforrást a térképen, megnyílik a jobb oldali ablaktábla, és általános információkat ad az erőforrásról, a csatlakoztatott biztonsági megoldásokról, ha vannak ilyenek, és az erőforrásra vonatkozó javaslatokat. Ez az egyes kiválasztott erőforrástípusok viselkedése. 
2. Kattintson a **Forgalom** elemre az erőforrás lehetséges kimenő és bejövő forgalmának listájának megtekintéséhez – ez egy átfogó lista abból, hogy ki kommunikálhat az erőforrással, és kivel kommunikálhat, és mely protokollokon és portokon keresztül. Például, ha kiválaszt egy virtuális gépet, az összes virtuális gép, amellyel kommunikálni tud, és amikor kiválaszt egy alhálózatot, az összes alhálózat, amellyel kommunikálni tud, megjelenik.

**Ezek az adatok a hálózati biztonsági csoportok elemzésén, valamint a fejlett gépi tanulási algoritmusokon alapulnak, amelyek több szabályt elemeznek a keresztezések és interakciók megértéséhez.** 

[![Forgalmi térkép hálózatba való beiktatása](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="legacy-networking"></a>Örökölt hálózatkezelés<a name ="legacy-networking"></a>

Ha nem rendelkezik a Security Center standard szint, ez a szakasz ismerteti, hogyan tekintheti meg az ingyenes hálózati javaslatokat.

Az adatok eléréséhez kattintson a Hálózat panelen az **Örökölt hálózatok megtekintése gombra.** 

[![Örökölt hálózatkezelés](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Internet facing endpoints (Internet felé néző végpontok) szakasz
Az **Internet felé néző végpontok** szakaszban láthatja azokat a virtuális gépeket, amelyek jelenleg internetfelé néző végponttal és annak állapotával vannak konfigurálva.

Ez a tábla a végpont nevét, az internetfelé néző IP-címet, valamint a hálózati biztonsági csoport és az NGFW-javaslatok aktuális súlyossági állapotát. A táblázat súlyosság szerint van rendezve.

### <a name="networking-topology-section"></a>Networking topology (Hálózati topológia) szakasz
A **Hálózati topológia** szakasz hierarchikus nézeteket nyújt az erőforrásokról.

Ez a tábla súlyosság szerint van rendezve (virtuális gépek és alhálózatok).

Ebben a topológia nézetben az első szint megjeleníti a virtuális hálózatokat. A második az alhálózatokat jeleníti meg, a harmadik szint pedig az alhálózatokhoz tartozó virtuális gépeket. A jobb oldali oszlopban a hálózati biztonsági csoport ezen erőforrásokra vonatkozó ajánlásainak aktuális állapota látható.

A harmadik szint megjeleníti a virtuális gépeket, amelyek hasonlóak a korábban leírthoz. Bármelyik erőforrásra kattintva többet tudhat meg, vagy alkalmazhatja a szükséges biztonsági ellenőrzést vagy konfigurációt.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a más Azure-erőforrástípusokra vonatkozó javaslatokról, olvassa el az alábbi témakört:

* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Az Azure SQL-szolgáltatás védelme az Azure Security Centerben](security-center-sql-service-recommendations.md)