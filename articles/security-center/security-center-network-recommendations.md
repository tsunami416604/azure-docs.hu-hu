---
title: Az Azure Security Center a hálózati erőforrások védelme |} A Microsoft Docs
description: Ez a dokumentum címek javaslatok az Azure Security Centerben, amelyekkel megvédheti az Azure network-erőforrásait, és megfelel a biztonsági házirendek maradjon.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 3bb8d4796d23b84f815d28efa45af619efe19af3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338233"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Az Azure Security Center hálózati erőforrások védelme
Az Azure Security Center folyamatosan elemzi a hálózati biztonsági eljárások az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a erősíti, és az erőforrások védelme a szükséges vezérlők konfigurálásának folyamatán hoz létre.

Ez a cikk foglalkozik, javaslatok, amelyek a alkalmazni a hálózati biztonsági szempontból az Azure-erőforrások. Hálózati javaslatok center next generation-tűzfalak, a hálózati biztonsági csoportok, virtuális gépek igény szerinti hozzáférés túlzottan megengedő bejövő forgalomra vonatkozó szabályokat és további körül. Hálózati javaslatok és javítási műveletek listáját lásd: [biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> A **hálózatkezelés** lap lehetővé teszi az Azure resource health hálózati szempontból részletes ismertetése. A hálózati térkép létrehozásához és adaptív hálózati vezérlők az Azure Security Center standard szintű csomag esetében érhetők el. [Ha az ingyenes szintet használja, a gombra kattinthat **örökölt hálózatműködés megtekintése** és fogadni a hálózati erőforrás javaslatok](#legacy-networking).
>

A **hálózatkezelés** lap a szakaszok is részletes áttekintést nyújt a hálózati erőforrások állapotával kapcsolatos további információért mélyedjen:

- Hálózati térkép létrehozásához (csak az Azure Security Center Standard csomagja esetén)
- NSG-t korlátozások a (hamarosan elérhető lesz. Regisztráljon az előzetes verzió)
- Hálózati biztonsági javaslatokat.
- Örökölt **hálózatkezelés** panelen (a korábbi hálózati panel) 
 
![Hálózat panel](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Hálózati térkép létrehozásához
A hálózati interaktív térkép segítségével grafikus leképezést kaphat a biztonsági lefedi a javaslatok és elemzések az biztosít a hálózati erőforrások korlátozására. A térkép segítségével megtekintheti a hálózati topológia az Azure számítási feladatok, a virtuális gépek és alhálózatok és a funkció részletesen elemezheti a térképen az adott erőforrásokat, és ezeket az erőforrásokat a javaslatok között kapcsolatok.

A hálózati térkép megnyitásához:

1. A Security Center erőforrás biztonsági higiéniai, alatt válassza ki **hálózatkezelés**.
2. A **hálózati térkép létrehozásához** kattintson **topológiájának megtekintéséhez**.
 
A topológia térkép alapértelmezett nézetét jeleníti meg:
- Az Azure-ban kiválasztott előfizetések. A térkép több előfizetést is támogatja.
- Virtuális gépek, alhálózatok és virtuális hálózatokat a Resource Manager-erőforrástípus (klasszikus Azure-erőforrások nem támogatottak)
- Csak olyan erőforrásokat, [hálózati javaslatok](security-center-recommendations.md) az olyan magas és közepes súlyosság  
- Az Internet felé néző erőforrások
- A térképen kiválasztott az Azure-ban az előfizetéseket van optimalizálva. Módosítsa a kijelölést, ha a térkép újraszámítja, és újra optimalizált új beállításai alapján.  

![Hálózati topológia térkép](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>A hálózati térkép ismertetése

A hálózati térképen jeleníti meg, az Azure-erőforrások egy **topológia** nézet és a egy **forgalom** megtekintése.

### <a name="the-topology-view"></a>A topológia e nézetében

Az a **topológia** nézet a hálózati térkép, megtekintheti a következő információkat a hálózati erőforrásokról:
- A belső lát láthatja a virtuális hálózatok a kiválasztott előfizetések belül, a következő kör összes alhálózatot, a külső összes virtuális gépet.
- A sorok a térképen az erőforrások összekapcsolása lehetővé teszik, hogy mely erőforrások tartoznak egymással, és hogyan épül fel az Azure-hálózatok. 
- A súlyosság mutatók segítségével gyorsan áttekintheti, amelyek erőforrások javaslatai nyissa meg a Security Centerből.
- Kattintson a őket feltárásához és az erőforrás részleteit megtekintheti az erőforrások, és megjeleníti a javaslatokat közvetlenül, és a hálózati környezetében képezze le.  
- Ha túl sok folyamatban van a térképen megjelenített erőforrásokat, az Azure Security Center a szellemi tulajdont képező algoritmusát, intelligens használja a fürt az erőforrásokat, kiemelve az erőforrásokat, amelyek a legtöbb kritikus állapotban van, és a legtöbb magas súlyosságú javaslatok. 

Mivel a térkép interaktív és dinamikus, minden csomópontnak számít kattintható, és a nézetben módosíthatja a szűrők alapján:

1. Módosíthatja, amit lát a hálózati térképen tetején a szűrőkkel használatával. Ön arra összpontosíthat a térkép alapján:
   -  **Biztonsági állapot**: A térkép (magas, közepes, alacsony), az Azure-erőforrások súlyosság alapján szűrhetők.
   - **Javaslatok**: Kiválaszthatja, hogy mely erőforrások jelennek meg alapján mely ajánlások aktívak ezeket az erőforrásokat a. Ha például tekintheti meg, amelyhez a Security Center javasolja hálózati biztonsági csoportok engedélyezése csak az erőforrásokat.
   - **Zónák hálózati**: Alapértelmezés szerint a térképen jeleníti meg, csak az Internet felé néző erőforrásokat, kiválaszthatja, valamint a belső virtuális gépeket.
 
2. Kattinthat **alaphelyzetbe** bal felső sarokban, és a térkép térjen vissza az alapértelmezett állapotba.

Részletes elemzések erőforrás:
1. Amikor kiválaszt egy adott erőforrást a térképen, a jobb oldali panelen megnyílik, és az erőforrást, ha vannak ilyenek, csatlakoztatott biztonsági megoldások kapcsolatos általános információkat biztosít, és a javaslatok az erőforráshoz. Olyan erőforrástípusok választja működése azonos típusú. 
2. Ha az egérmutatót egy csomópontot a térképen, megtekintheti az erőforrás, beleértve az előfizetést, erőforrástípust és erőforráscsoport kapcsolatos általános információkat.
3. A hivatkozás segítségével az elemleírás nagyíthatja és a térkép refocus adott csomóponton. 
4. A forrásadatok egy adott csomópont a térkép refocus, kicsinyítés.

### <a name="the-traffic-view"></a>A Traffic view

A **forgalom** a nézet tartalmazza az összes lehetséges forgalom az erőforrások közötti leképezést. Ez lehetővé teszi, hogy az összes szabály, amely konfigurált visual leképezést határoz meg, akikkel erőforrások kommunikálhassanak. Ez lehetővé teszi, hogy tekintse meg a meglévő konfigurációt a hálózati biztonsági csoportok, valamint a gyorsan azonosíthatja a lehetséges kockázatos konfigurációk belül a számítási feladatokat.

### <a name="uncover-unwanted-connections"></a>Fedje fel a nem kívánt kapcsolatok

Ez a nézet erőssége képessége bemutatják, ezeket a engedélyezett kapcsolatokat és a biztonsági réseket, hogy létezik, így használhatja a keresztmetszete hajtsa végre a szükséges vezethet be az erőforrások, az adatokat. 

Például előfordulhat, hogy észlelni, hogy Ön nem tudtunk két gép képes kommunikálni, lehetővé teszi, hogy hatékonyabb elkülönítése a számítási feladatok és az alhálózatokat.

### <a name="investigate-resources"></a>Erőforrások vizsgálata

Részletes elemzések erőforrás:
1. Amikor kiválaszt egy adott erőforrást a térképen, a jobb oldali panelen megnyílik, és az erőforrást, ha vannak ilyenek, csatlakoztatott biztonsági megoldások kapcsolatos általános információkat biztosít, és a javaslatok az erőforráshoz. Olyan erőforrástípusok választja működése azonos típusú. 
2. Kattintson a **forgalom** Ez az átfogó listáját aki képes kommunikálni az erőforrást, és ki azt a, és mely protokollok és portok keresztül léphet kapcsolatba az erőforrás - lehetséges kimenő és bejövő forgalom listáját lásd:.

**Ezek az adatok elemzése a hálózati biztonsági csoportok, valamint a fejlett gépi tanulási algoritmusok elemzéséhez, megtudhatja, hogy azok vágánykapcsolatokat és és kapcsolati több szabály alapul.** 

![Hálózati forgalom térkép](./media/security-center-network-recommendations/network-map-traffic.png)

## Az örökölt hálózati <a name ="legacy-networking"></a>

Ha a Security Center Standard csomagja nem rendelkezik, ez a szakasz azt ismerteti, hogyan ingyenes hálózatokra vonatkozó javaslatok megtekintése.

Ezt az információt, a hálózat panelen eléréséhez kattintson **örökölt hálózatműködés megtekintése**. 

![Az örökölt hálózati](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Internet facing endpoints (Internet felé néző végpontok) szakasz
Az a **internetről elérhető végpontok** szakaszban láthatja a virtuális gépek, amelyeken jelenleg az internetkapcsolattal rendelkező végpontot, és annak állapotát.

Ez a táblázat tartalmazza a végpont neve, az Internet felé néző IP-címet és a hálózati biztonsági csoport és az NGFW javaslatok aktuális súlyossági állapotát. A tábla súlyosság szerint rendezve.

### <a name="networking-topology-section"></a>Networking topology (Hálózati topológia) szakasz
A **hálózati topológia** szakasz rendelkezik az erőforrások hierarchikus nézetét.

Ez a táblázat (virtuális gépek és alhálózatok) súlyosság szerint rendezve.

A topológia e nézetében az első szintű jeleníti meg a virtuális hálózatok. A második megjelenik rendelkezik alhálózatokkal, és a harmadik szinten az ezekhez az alhálózatokhoz tartozó virtuális gépek jeleníti meg. A jobb oldali oszlopban az adott erőforrásokhoz tartozó hálózati biztonsági csoport javaslatok aktuális állapotát jeleníti meg.

A harmadik szinten megjeleníti a virtuális gépek, a korábban leírtakhoz hasonlóan. Kattintson bármely erőforrás további vagy a alkalmazni a szükséges biztonsági rendszabályok / konfigurációk.

## <a name="network-recommendations"></a>Hálózatokra vonatkozó javaslatok

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Gép|40|Hálózati biztonsági csoportok engedélyezése a virtuális gépeken|Hálózati biztonsági csoportok a a virtuális gépek hálózati hozzáférésének engedélyezése.|
|Alhálózat|35|A hálózati biztonsági csoportok alhálózatokhoz engedélyezése |Hálózati biztonsági csoportok a az alhálózatokra üzembe helyezett erőforrások hálózati hozzáférésének engedélyezése.|
|Gép|30|Igény szerinti hálózati hozzáférés-vezérlés alkalmazása|Csak idő VM hozzáférés-vezérlés véglegesen zárolni a kiválasztott portot a hozzáférést a alkalmazni, és engedélyezze a jogosult felhasználók számára, hogy ugyanazt a mechanizmust keresztül és a egy korlátozott ideig a megnyitásukhoz.|
|Gép|20|Internetről elérhető végponton keresztüli hozzáférés korlátozása|Az internetkapcsolattal rendelkező virtuális gépek a meglévő hozzáférés korlátozásával, a hálózati biztonsági goups Felvértezni lehetővé teszik a szabályok.|
|Gép|10|Új generációs tűzfal hozzáadása|Next Generation Firewall (NGFW) megoldás, amelyekkel jobban megvédheti az internetkapcsolattal rendelkező virtuális gépek hozzáadásához.|
|Gép|5|Hálózati átjáró csak tűzfalon keresztül haladjon a forgalom|A következő generációs tűzfal megoldás a telepítés befejezéséhez a védett internetkapcsolattal rendelkező virtuális gépek forgalmat kell átirányítani csak a következő generációs tűzfal megoldás használatával.|
|VNet|5|DDoS protection-szabvány engedélyezése|Ezeket a virtuális hálózatokat a nyilvános IP-címek az alkalmazások nem védi a standard szintű DDOS protection szolgáltatás. Lehetővé teszi, hogy csökkenti a hálózat-es és a protokoll támadások engedélyezése javasolt.|
|Gép|10|Új generációs tűzfal hozzáadása|egy Next Generation Firewall (NGFW) megoldás, amelyekkel jobban megvédheti az internetkapcsolattal rendelkező virtuális gépek nn.|
|Gép|5|Hálózati átjáró csak tűzfalon keresztül haladjon a forgalom|A következő generációs tűzfal megoldás a telepítés befejezéséhez a védett internetkapcsolattal rendelkező virtuális gépek forgalmat kell átirányítani csak a következő generációs tűzfal megoldás használatával.|
Virtuális hálózat|5|DDoS protection-szabvány engedélyezése|Ezeket a virtuális hálózatokat a nyilvános IP-címekkel rendelkező alkalmazások nem védi a standard szintű DDOS protection szolgáltatás. Lehetővé teszi, hogy csökkenti a hálózat-es és a protokoll támadások engedélyezése javasolt.|
## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
