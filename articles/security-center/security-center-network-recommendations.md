---
title: A hálózati erőforrások védelme Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum olyan Azure Security Center javaslatokat tartalmaz, amelyek segítenek az Azure-hálózati erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: fb91d6a9cdc56c88b424b7e0382f283c8b55dac9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234329"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>A hálózati erőforrások védetté Azure Security Center
Azure Security Center folyamatosan elemzi az Azure-erőforrások biztonsági állapotát a hálózati biztonsággal kapcsolatos ajánlott eljárásokhoz. Ha Security Center észleli a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

Ez a cikk az Azure-erőforrásokra vonatkozó ajánlásokat tárgyalja a hálózati biztonsági szempontból. Hálózatkezelési javaslatok központ a következő generációs tűzfalak, hálózati biztonsági csoportok, JIT virtuális gép hozzáférése a bejövő forgalmi szabályokhoz, és így tovább. A hálózati javaslatok és a javítási műveletek listáját lásd: [biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md).

> [!NOTE]
> A **hálózatkezelés** oldalon részletesen megismerheti az Azure-erőforrások állapotát hálózati perspektívából. A hálózati Térkép és az adaptív hálózati vezérlők csak a standard szintű Azure Security Center érhetők el. [Ha az ingyenes szintet használja, akkor a gombra kattintva megtekintheti  az örökölt hálózatkezelési és a hálózati erőforrásokra vonatkozó ajánlásokat](#legacy-networking).
>

A **hálózatkezelés** oldal áttekintést nyújt azokról a részekről, amelyeket részletesen bemutathat a hálózati erőforrások állapotával kapcsolatos további információk eléréséhez:

- Hálózati Térkép (csak Azure Security Center Standard szint)
- Adaptív hálózat-megerősítés
- Hálózatkezelési biztonsági javaslatok.
- Örökölt **hálózatkezelés** panel (az előző hálózatkezelés panel) 
 
![Hálózatkezelés panel](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Hálózati Térkép
Az interaktív hálózati Térkép grafikus nézetet biztosít a biztonsági átfedésekkel, így javaslatokat és bepillantást nyerhet a hálózati erőforrások megerősítéséhez. A Térkép használatával megtekintheti az Azure-beli számítási feladatok hálózati topológiáját, a virtuális gépek és az alhálózatok közötti kapcsolatokat, valamint a térképről adott erőforrásokra és az ezekre az erőforrásokra vonatkozó javaslatokkal való részletezés lehetőségét.

A hálózati Térkép megnyitása:

1. Security Center az erőforrás-biztonsági higiénia területen válassza a **hálózatkezelés**lehetőséget.
2. A **hálózati Térkép** területen kattintson a **topológia**megjelenítése elemre.
 
A topológiai Térkép alapértelmezett nézete a következőket jeleníti meg:

- Az Azure-ban kiválasztott előfizetések. A Térkép több előfizetést is támogat.
- A Resource Manager erőforrástípus virtuális gépek, alhálózatok és virtuális hálózatok (a klasszikus Azure-erőforrások nem támogatottak)
- Társ virtuális hálózatok
- Csak a magas vagy közepes súlyosságú [hálózati javaslatokkal](security-center-recommendations.md) rendelkező erőforrások  
- Internet felé irányuló erőforrások
- A Térkép az Azure-ban kiválasztott előfizetésekhez van optimalizálva. Ha módosítja a kijelölést, a rendszer az új beállítások alapján újraszámítja és újraoptimalizálja a térképet.  

![Hálózati topológia térképe](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>A hálózati Térkép ismertetése

A hálózati Térkép az Azure-erőforrásokat topológiai nézetben és  **forgalmi** nézetben jelenítheti meg.

### <a name="the-topology-view"></a>A topológia nézet

A hálózati Térkép **topológia** nézetében a következő információkat tekintheti meg a hálózati erőforrásokkal kapcsolatban:

- A belső körben a kiválasztott előfizetéseken belüli összes virtuális hálózatok megtekintheti, a következő kör pedig az összes alhálózatot, a külső kör pedig az összes virtuális gép.
- A Térkép erőforrásaihoz csatlakozó sorokban megtudhatja, hogy mely erőforrások kapcsolódnak egymáshoz, és hogy az Azure-hálózat hogyan épül fel. 
- A súlyossági mutatók segítségével gyorsan áttekintheti, hogy mely erőforrások rendelkeznek a Security Center által megnyitott javaslatokkal.
- Rákattinthat bármelyik erőforrásra a részletezéshez, és megtekintheti az adott erőforrás részleteit és javaslatait közvetlenül, valamint a hálózati Térkép kontextusában.  
- Ha túl sok erőforrás jelenik meg a térképen, Azure Security Center a saját saját algoritmusát használja az erőforrások intelligens fürtözéséhez, kiemelve a legkritikusabb állapotú erőforrásokat, és a legtöbb súlyossági szintű javaslatot. 

Mivel a Térkép interaktív és dinamikus, minden csomópont kattintható, a nézet pedig a szűrők alapján változhat:

1. A hálózat térképen a felül látható szűrők használatával módosíthatja a megjelenő tudnivalókat. A térképen a következők alapján lehet összpontosítani:

   -  **Biztonsági állapot**: Az Azure-erőforrások súlyossága (magas, közepes, alacsony) alapján szűrheti a térképet.
   - **Javaslatok**: Kiválaszthatja, hogy mely erőforrások jelenjenek meg az adott erőforrásokon aktív javaslatok alapján. Megtekintheti például azokat az erőforrásokat, amelyekhez Security Center azt javasolja, hogy engedélyezze a hálózati biztonsági csoportokat.
   - **Hálózati zónák**: Alapértelmezés szerint a Térkép csak az internet felé irányuló erőforrásokat jeleníti meg, a belső virtuális gépeket is kiválaszthatja.
 
2. A bal felső sarokban lévő **visszaállítás** gombra kattintva bármikor visszatérhet a térképhez az alapértelmezett állapotba.

Erőforrások részletezése:

1. Amikor kiválaszt egy adott erőforrást a térképen, megnyílik a jobb oldali ablaktábla, és általános információkat nyújt az erőforrásról, a csatlakoztatott biztonsági megoldásokról, ha vannak ilyenek, valamint az erőforrásra vonatkozó ajánlásokat. Ez ugyanaz a viselkedés a kiválasztott erőforrásokhoz tartozó típusoknál. 
2. Ha a Térkép egyik csomópontja fölé viszi a mutatót, megtekintheti az erőforrással kapcsolatos általános információkat, beleértve az előfizetést, az erőforrás típusát és az erőforráscsoportot is.
3. A hivatkozással nagyíthatja az elemleírást, és áthelyezheti a leképezést az adott csomóponton. 
4. Ha a térképet egy adott csomópontról szeretné áthelyezni, nagyítsa ki.

### <a name="the-traffic-view"></a>A forgalom nézet

A **forgalmi** nézet az erőforrások közötti lehetséges adatforgalomról nyújt térképet. Ez egy vizualizációs térképet biztosít az összes konfigurált szabályhoz, amely meghatározza, hogy mely erőforrások tudnak kommunikálni. Ez lehetővé teszi a hálózati biztonsági csoportok meglévő konfigurációjának megtekintését, valamint a számítási feladatokban lévő lehetséges kockázatos konfigurációk gyors azonosítását.

### <a name="uncover-unwanted-connections"></a>Nemkívánatos kapcsolatok felfedése

Ennek a nézetnek az erőssége, hogy megmutassa ezeket az engedélyezett kapcsolatokat a meglévő sebezhetőségekkel együtt, így az adatok több szakasza segítségével elvégezheti az erőforrások szükséges megerősítését. 

Előfordulhat például, hogy észlelhető két olyan gép, amelyről nem tudott kommunikálni, így könnyebben elkülönítheti a munkaterheléseket és az alhálózatokat.

### <a name="investigate-resources"></a>Erőforrások vizsgálata

Erőforrások részletezése:

1. Amikor kiválaszt egy adott erőforrást a térképen, megnyílik a jobb oldali ablaktábla, és általános információkat nyújt az erőforrásról, a csatlakoztatott biztonsági megoldásokról, ha vannak ilyenek, valamint az erőforrásra vonatkozó ajánlásokat. Ez ugyanaz a viselkedés a kiválasztott erőforrásokhoz tartozó típusoknál. 
2. A **forgalom** lehetőségre kattintva megtekintheti a lehetséges kimenő és bejövő adatforgalom listáját az erőforráson – ez egy átfogó lista, amely képes kommunikálni az erőforrással, és hogy ki tud kommunikálni a szolgáltatással, és mely protokollokat és portokat használhatja. Ha például kiválaszt egy virtuális gépet, az összes kommunikálni képes virtuális gép megjelenik, és amikor kiválaszt egy alhálózatot, megjelenik az összes olyan alhálózat, amelyről kommunikálni tud.

**Ezek az információk a hálózati biztonsági csoportok és a speciális gépi tanulási algoritmusok elemzésén alapulnak, amelyek több szabályt elemeznek a crossover-és interakciók megismerése érdekében.** 

![Hálózati forgalom térképe](./media/security-center-network-recommendations/network-map-traffic.png)

## Örökölt hálózatkezelés<a name ="legacy-networking"></a>

Ha nem rendelkezik Security Center standard szintű csomaggal, ez a szakasz az ingyenes hálózati javaslatok megtekintését ismerteti.

Ezen információk eléréséhez a hálózatkezelés panelen kattintson a **régi hálózatkezelés megtekintése**elemre. 

![Örökölt hálózatkezelés](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Internet facing endpoints (Internet felé néző végpontok) szakasz
Az internetre irányuló **végpontok** szakaszban megtekintheti azokat a virtuális gépeket, amelyek jelenleg egy internetkapcsolattal rendelkező végponttal és annak állapotával vannak konfigurálva.

Ez a tábla a végpont nevét, az internet felé irányuló IP-címet, valamint a hálózati biztonsági csoport aktuális súlyossági állapotát és a NGFW-javaslatokat tartalmazza. A tábla súlyosság szerint rendezve jelennek meg.

### <a name="networking-topology-section"></a>Networking topology (Hálózati topológia) szakasz
A **hálózati topológia** szakasz az erőforrások hierarchikus nézetét tartalmazza.

Ez a tábla (virtuális gépek és alhálózatok) súlyosság szerint rendezve jelennek meg.

Ebben a topológia nézetben az első szint a virtuális hálózatok jeleníti meg. A második megjeleníti az alhálózatokat, a harmadik szint pedig az ezekhez az alhálózatokhoz tartozó virtuális gépeket jeleníti meg. A jobb oldali oszlopban látható a hálózati biztonsági csoportra vonatkozó javaslatok aktuális állapota az adott erőforrásokra vonatkozóan.

A harmadik szint a korábban leírthoz hasonló virtuális gépeket jelenít meg. Bármelyik erőforrásra kattintva további információkat kaphat, vagy alkalmazhatja a szükséges biztonsági vezérlést vagy konfigurációt.

## <a name="network-recommendations"></a>Hálózatokra vonatkozó javaslatok

|Javaslat neve|Leírás|Severity|Biztonsági pontszám|Erőforrás típusa|
|----|----|----|----|----|----|
|Az alhálózat szintjén engedélyezni kell a hálózati biztonsági csoportokat.|Engedélyezze a hálózati biztonsági csoportok számára az alhálózatokban üzembe helyezett erőforrások hálózati hozzáférésének szabályozását.|Magas/közepes|30|Subnet|
|A virtuális gépeket hálózati biztonsági csoporttal kell társítani|Engedélyezze a hálózati biztonsági csoportok számára a virtuális gépek hálózati hozzáférésének szabályozását.|Magas/közepes|30|Virtuális gép|
|A hozzáférésnek korlátozva kell lennie az internetre irányuló virtuális gépekkel rendelkező, engedékeny hálózati biztonsági csoportoknak|A meglévő engedélyezési szabályok elérésének korlátozásával megerősítheti az internetre irányuló virtuális gépek hálózati biztonsági csoportjait.|Magas|20|Virtuális gép|
|A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni|Megerősítheti a webalkalmazásokat futtató virtuális gépek hálózati biztonsági csoportját (NSG) olyan NSG-szabályokkal, amelyek túlzottan engedékenyek a webalkalmazási portok tekintetében.|Magas|20|Virtuális gép|
|A App Serviceshoz való hozzáférést korlátozni kell|Korlátozza a hozzáférést a App Services a hálózati konfiguráció módosításával, hogy megtagadja a bejövő forgalmat a túl széles tartományokból.|Magas|10|App Service-ben|
|A felügyeleti portokat be kell zárni a virtuális gépeken|A felügyeleti portokhoz való hozzáférés korlátozása érdekében a virtuális gépek hálózati biztonsági csoportjának megerősítése.|Magas|10|Virtuális gép|
DDoS Protection a standardot engedélyezni kell|A DDoS Protection szolgáltatás szabványának engedélyezésével biztosíthatja a nyilvános IP-címekkel rendelkező alkalmazásokat tartalmazó virtuális hálózatok védelmét. A DDoS elleni védelem lehetővé teszi a hálózati térfogatos és a protokollok elleni támadások enyhítését.|Magas|10|Virtuális hálózat|
|A virtuális gépen lévő IP-továbbítást le kell tiltani|Tiltsa le az IP-továbbítást. Ha az IP-továbbítás engedélyezve van egy virtuális gép hálózati adapterén, a gép más célhelyekre irányuló forgalmat is fogadhat. Az IP-továbbítás csak ritkán szükséges (például a virtuális gép hálózati virtuális berendezésként való használata esetén), ezért ezt a hálózati biztonsági csapatnak kell áttekintenie.|Közepes|10|Virtuális gép|
|Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|A "csak HTTPS" hozzáférés engedélyezése a webes alkalmazásokhoz. A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban.|Közepes|20|Webalkalmazás|
|Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken|Alkalmazzon igény szerinti (JIT) virtuálisgép-hozzáférés-vezérlést a kiválasztott portok elérésének végleges zárolásához, és engedélyezze, hogy a jogosult felhasználók csak korlátozott ideig nyissák meg őket a JIT használatával.|Magas|20|Virtuális gép|
|A Function apps csak HTTPS-kapcsolaton keresztül érhető el|A "csak HTTPS" hozzáférés engedélyezése a Function apps alkalmazásokhoz. A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban.|Közepes|20|Függvényalkalmazás|
|A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell|Biztonságos átvitel engedélyezése a Storage-fiókokba. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől.|Magas|20|Tárfiók|

## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
