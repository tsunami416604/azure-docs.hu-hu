---
title: Kapcsolódás Azure-beli virtuális hálózatokhoz ISE-Azure Logic Apps
description: Hozzon létre egy integrációs szolgáltatási környezetet (ISE), amely hozzáférhet az Azure Virtual Networks (virtuális hálózatok) szolgáltatáshoz Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 4c4eb5a6cb7527bcb3eb21beebb8063b0bd021d3
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680472"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Appsból integrációs szolgáltatási környezet (ISE) használatával

Olyan esetekben, amikor a Logic apps és az integrációs fiókoknak hozzáférésre van szüksége egy Azure-beli [virtuális hálózathoz](../virtual-network/virtual-networks-overview.md), hozzon létre egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Az ISE egy privát és elszigetelt környezet, amely dedikált tárolót és más, a nyilvános vagy a "globális" Logic Apps szolgáltatástól elkülönített erőforrásokat használ. Ez a elkülönítés azt is csökkenti, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére.

Ha ISE-t hoz létre, az Azure *befecskendezi* az ISE-t az Azure-beli virtuális hálózatba, amely ezután telepíti a Logic Apps szolgáltatást a virtuális hálózatba. Logikai alkalmazás vagy integrációs fiók létrehozásakor válassza ki az ISE helyét. A logikai alkalmazás vagy integrációs fiók ezután közvetlenül hozzáférhet az erőforrásokhoz, például a virtuális gépekhez, a kiszolgálókhoz, a rendszerekhez és a szolgáltatásokhoz a virtuális hálózaton.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Ahhoz, hogy a Logic apps és az integrációs fiókok együtt működjenek az ISE-ben, mindkettőnek *ugyanazt az ISE* -t kell használnia, mint a helyük.

Az ISE megnövelte a futtatási időtartamot, a tárterület megőrzését, az átviteli sebességet, a HTTP-kérést és a válasz időtúllépését, az üzenetek méretét és az egyéni összekötői kérelmeket. További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md). További információ a ISEs: [Azure Virtual Network-erőforrások elérése Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Győződjön meg arról, hogy a virtuális hálózat minden szükséges portja meg van nyitva, hogy a forgalom a virtuális hálózat alhálózatai között is áthaladjon az ISE-n keresztül.

* Hozza létre az ISE-t.

* Extra kapacitás hozzáadása az ISE-hez.

> [!IMPORTANT]
> A Logic apps, a beépített triggerek, a beépített műveletek és az ISE-ben futó összekötők a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md). Ha nem rendelkezik virtuális hálózattal, Ismerje meg, hogyan [hozhat létre Azure-beli virtuális hálózatot](../virtual-network/quick-create-portal.md). 

  * A virtuális hálózatnak négy *üres* alhálózattal kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre és helyezzen üzembe az ISE-ben. Ezeket az alhálózatokat előre is létrehozhatja, vagy megvárhatja, amíg létre nem hozza az ISE-t, ahol egyszerre létrehozhat alhálózatokat. További információ az [alhálózatokra vonatkozó követelményekről](#create-subnet).

  * Az alhálózatok nevének alfabetikus karakterrel vagy aláhúzással kell kezdődnie, és nem használhatja ezeket a karaktereket: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Ha az ISE-t egy Azure Resource Manager sablonon keresztül szeretné telepíteni, először győződjön meg arról, hogy egy üres alhálózatot delegált a Microsoft. Logic/integrationServiceEnvironment. Ezt a delegálást nem kell végrehajtania, amikor a Azure Portalon keresztül telepíti.

  * Győződjön meg arról, hogy a virtuális hálózat [elérhetővé teszi ezeket a portokat](#ports) , így az ISE megfelelően működik, és elérhető marad.

  * Ha a [ExpressRoute](../expressroute/expressroute-introduction.md)-t használja, amely privát kapcsolatot biztosít a Microsoft Cloud Services szolgáltatással, [létre kell hoznia egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely a következő útvonalat tartalmazza, és a táblázatot az ISE által használt összes alhálózathoz csatolja:

    **Név**: <*route-Name* ><br>
    **Címzési előtag**: 0.0.0.0/0<br>
    **Következő ugrás**: Internet

* Ha egyéni DNS-kiszolgálókat szeretne használni az Azure-beli virtuális hálózathoz, [ezeket a lépéseket követve állítsa be ezeket a kiszolgálókat](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) , mielőtt TELEPÍTENÉ az ISE-t a virtuális hálózatra. Ellenkező esetben minden alkalommal, amikor megváltoztatja a DNS-kiszolgálót, újra kell indítania az ISE-t is.

  > [!IMPORTANT]
  > Ha az ISE létrehozása után megváltoztatja a DNS-kiszolgáló beállításait, győződjön meg arról, hogy újraindítja az ISE-t. A DNS-kiszolgáló beállításainak kezelésével kapcsolatos további információkért lásd: [virtuális hálózat létrehozása, módosítása vagy törlése](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="ports"></a>

## <a name="check-network-ports"></a>Hálózati portok keresése

Ha egy Azure-beli virtuális hálózattal rendelkező ISE-t használ, egy gyakori telepítési probléma egy vagy több letiltott porttal rendelkezik. Az ISE és a célként megadott rendszer közötti kapcsolatok létrehozásához használt összekötők magukban foglalhatják a saját portra vonatkozó követelményeket is. Ha például az FTP-összekötő használatával kommunikál egy FTP-rendszerrel, győződjön meg arról, hogy az FTP-rendszeren használt port elérhető, például a 21-es port a parancsok küldéséhez. Győződjön meg arról, hogy az ISE elérhető marad, és megfelelően működik, majd nyissa meg az alábbi táblázatban megadott portokat. Ellenkező esetben, ha a szükséges portok nem érhetők el, az ISE működése leáll.

> [!IMPORTANT]
> A forrásoldali portok elmúlóak, ezért ügyeljen arra, hogy az összes szabályhoz `*` állítsa be őket.
> Az alhálózatokon belüli belső kommunikációhoz az ISE megköveteli, hogy az alhálózatokon belül minden portot meg lehessen nyitni.

* Ha megkötések nélkül hozott létre új virtuális hálózatot és alhálózatokat, nem kell beállítania [hálózati biztonsági csoportokat (NSG)](../virtual-network/security-overview.md#network-security-groups) a virtuális hálózatban az alhálózatok közötti adatforgalom szabályozásához.

* Egy meglévő virtuális hálózatban *opcionálisan* beállíthatja a NSG a [hálózati forgalom alhálózatok közötti szűrésével](../virtual-network/tutorial-filter-network-traffic.md). Ha ezt az útvonalat választja, azon a virtuális hálózaton, amelyen be szeretné állítani a NSG, győződjön meg arról, hogy az alábbi táblázatban megadott portokat nyitja meg. [NSG biztonsági szabályok](../virtual-network/security-overview.md#security-rules)használata esetén a TCP-és UDP-protokollokra is szükség van.

* Ha korábban már meglévő NSG vagy tűzfallal rendelkezik a virtuális hálózatban, győződjön meg arról, hogy az alábbi táblázatban megadott portok vannak megnyitva. [NSG biztonsági szabályok](../virtual-network/security-overview.md#security-rules)használata esetén a TCP-és UDP-protokollokra is szükség van.

Az alábbi táblázat leírja a virtuális hálózat azon portjait, amelyeket az ISE használ, és ahol a portok használatban vannak. A [Resource Manager szolgáltatás címkéi](../virtual-network/security-overview.md#service-tags) olyan IP-cím-előtagokat jelölnek, amelyek a biztonsági szabályok létrehozásakor megkönnyítik a bonyolultságot.

| Rendeltetés | Irány | Célportok | Forrásoldali szolgáltatás címkéje | Cél szolgáltatáscímkéje | Megjegyzések |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Kommunikáció a Azure Logic Apps | Kimenő | 80, 443 | VirtualNetwork | Internet | A port a külső szolgáltatástól függ, amellyel a Logic Apps szolgáltatás kommunikál |
| Azure Active Directory | Kimenő | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage-függőség | Kimenő | 80, 443 | VirtualNetwork | Adattárolás | |
| Alhálózati kommunikáció | Bejövő & kimenő | 80, 443 | VirtualNetwork | VirtualNetwork | Az alhálózatok közötti kommunikációhoz |
| Kommunikáció Azure Logic Apps | Bejövő | 443 | Belső hozzáférési végpontok: <br>VirtualNetwork <p><p>Külső hozzáférési végpontok: <br>Internet <p><p>**Megjegyzés**: ezek a végpontok az [ISE létrehozásakor kiválasztott](#create-environment)végpont-beállításra vonatkoznak. További információ: [végponti hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | Annak a számítógépnek vagy szolgáltatásnak az IP-címe, amely a logikai alkalmazásban található kérelem-triggert vagy webhookot hívja meg. A port bezárása vagy blokkolása megakadályozza a HTTP-hívásokat a logikai alkalmazásokhoz kérelem-eseményindítókkal. |
| Logikai alkalmazás futtatási előzményei | Bejövő | 443 | Belső hozzáférési végpontok: <br>VirtualNetwork <p><p>Külső hozzáférési végpontok: <br>Internet <p><p>**Megjegyzés**: ezek a végpontok az [ISE létrehozásakor kiválasztott](#create-environment)végpont-beállításra vonatkoznak. További információ: [végponti hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | Annak a számítógépnek az IP-címe, amelyről meg kell tekinteni a logikai alkalmazás futtatási előzményeit. Bár a port bezárása vagy blokkolása nem akadályozza meg a futtatási előzmények megtekintését, a futtatási előzményekben nem tekintheti meg az egyes lépések bemeneteit és kimeneteit. |
| Kapcsolatok kezelése | Kimenő | 443 | VirtualNetwork  | AppService | |
| Diagnosztikai naplók közzététele & metrikák | Kimenő | 443 | VirtualNetwork  | AzureMonitor | |
| Kommunikáció az Azure Traffic Manager | Bejövő | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps Designer – dinamikus tulajdonságok | Bejövő | 454 | Internet | VirtualNetwork | A kérelmek az [adott régióban a Logic apps hozzáférési végpont bejövő IP-címeiből](../logic-apps/logic-apps-limits-and-config.md#inbound)származnak. |
| App Service felügyeleti függőség | Bejövő | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Összekötő üzembe helyezése | Bejövő | 454 | AzureConnectors | VirtualNetwork | Összekötők üzembe helyezéséhez és frissítéséhez szükséges. A port bezárása vagy blokkolása esetén az ISE-telepítések sikertelenek lesznek, és meggátolják az összekötők frissítését és javítását. |
| Összekötő-házirend üzembe helyezése | Bejövő | 3443 | AppService | VirtualNetwork | Összekötők üzembe helyezéséhez és frissítéséhez szükséges. A port bezárása vagy blokkolása esetén az ISE-telepítések sikertelenek lesznek, és meggátolják az összekötők frissítését és javítását. |
| Azure SQL-függőség | Kimenő | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Kimenő | 1886 | VirtualNetwork | AzureMonitor | Állapot közzétételének Resource Health |
| API Management felügyeleti végpont | Bejövő | 3443 | APIManagement | VirtualNetwork | |
| Függőség a naplótól az Event hub-házirendbe és a figyelési ügynökbe | Kimenő | 5672 | VirtualNetwork | EventHub | |
| Azure cache elérése Redis-példányok között szerepkör-példányok között | Bejövő <br>Kimenő | 6379-6383 | VirtualNetwork | VirtualNetwork | Emellett ahhoz, hogy az ISE működjön az Azure cache-sel az Redis-hez, meg kell nyitnia ezeket [a kimenő és bejövő portokat az Azure cache Redis – gyakori kérdések című témakörben](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Bejövő | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Az ISE létrehozása

Az integrációs szolgáltatási környezet (ISE) létrehozásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)az Azure fő menüjében válassza az **erőforrás létrehozása**lehetőséget.
A keresőmezőbe írja be szűrőként az "integrációs szolgáltatási környezet" kifejezést.

   ![Új erőforrás létrehozása](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. A integrációs szolgáltatási környezet létrehozás ablaktáblán válassza a **Létrehozás**lehetőséget.

   ![Válassza a létrehozás lehetőséget](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Adja meg ezeket az adatokat a környezetében, majd válassza a **felülvizsgálat + létrehozás**lehetőséget, például:

   ![Adja meg a környezet részleteit](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Szükséges | Value (Díj) | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés-neve*> | A környezetéhez használni kívánt Azure-előfizetés |
   | **Erőforráscsoport** | Igen | <*Azure-Erőforrás-csoport neve* > | Az Azure-erőforráscsoport, amelyben létre szeretné hozni a környezetet |
   | **Integrációs szolgáltatási környezet neve** | Igen | <*környezet neve* > | Az ISE neve, amely csak betűket, számokat, kötőjeleket (`-`), aláhúzást (`_`) és pontokat (`.`) tartalmazhat. |
   | **Hely** | Igen | <*Azure-Datacenter – régió* > | Az Azure-adatközpont régiója, ahol üzembe helyezheti a környezetet |
   | **Termékváltozat** | Igen | **Prémium** vagy **fejlesztői (SLA nélkül)** | A létrehozandó és használandó ISE SKU. Az adatsku-változatok közötti különbségekért lásd: [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-i. <p><p>**Fontos**: Ez a beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. |
   | **További kapacitás** | Prémium szintű <br>Igen <p><p>Developer: <br>Nem alkalmazható | Prémium szintű <br>0 – 10 <p><p>Developer: <br>Nem alkalmazható | Az ISE-erőforráshoz használandó további feldolgozási egységek száma. A kapacitás létrehozás utáni hozzáadásával kapcsolatban lásd: [ISE-kapacitás hozzáadása](#add-capacity). |
   | **Hozzáférési végpont** | Igen | **Belső** vagy **külső** | Az ISE-hez használni kívánt hozzáférési végpontok típusa, amely meghatározza, hogy az ISE-ben a kérelem vagy a webhook aktiválva van-e a logikai alkalmazásokban a virtuális hálózaton kívülről érkező hívásokat. A végpont típusa a logikai alkalmazás futtatási előzményeiben lévő bemenetekhez és kimenetekhez is hatással van. További információ: [végponti hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Fontos**: Ez a beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. |
   | **Virtuális hálózat** | Igen | <*Azure-Virtual-Network-name* > | Az Azure-beli virtuális hálózat, ahová be szeretné szúrni a környezetét, hogy a környezetében a logikai alkalmazások hozzáférhessenek a virtuális hálózathoz. Ha nem rendelkezik hálózattal, [először hozzon létre egy Azure-beli virtuális hálózatot](../virtual-network/quick-create-portal.md). <p>**Fontos**: ezt az injekciót *csak* akkor hajthatja végre, ha létrehozza az ISE-t. |
   | **Alhálózatok** | Igen | <*alhálózat – erőforrás-lista* > | Az ISE négy *üres* alhálózatot igényel ahhoz, hogy erőforrásokat hozzon létre és helyezzen üzembe a környezetben. Az egyes alhálózatok létrehozásához [kövesse az ebben a táblázatban szereplő lépéseket](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Alhálózat létrehozása**

   Ahhoz, hogy erőforrásokat hozzon létre és helyezzen üzembe a környezetében, az ISE-nek négy olyan *üres* alhálózatra van szüksége, amely nem delegál semmilyen szolgáltatást. A környezet létrehozása után ezeket az alhálózati címeket *nem* módosíthatja.
   
   > [!IMPORTANT]
   > 
   > Az alhálózatok nevének alfabetikus karakterrel vagy aláhúzással (No Number) kell kezdődnie, és nem használja ezeket a karaktereket: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.
   
   Emellett minden alhálózatnak meg kell felelnie a következő követelményeknek:

   * Az [osztály nélküli Inter-domain Routing (CIDR) formátumot](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) és egy B osztályú címtartományt használ.

   * Legalább egy `/27`t használ a címtartomány esetében, mert minden alhálózatnak *legalább 32-es címnek kell* lennie *.* Példa:

     * a `10.0.0.0/27` 32-es címmel rendelkezik, mert 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32.

     * a `10.0.0.0/24` 256-es címmel rendelkezik, mert 2<sup>(32-24)</sup> 2<sup>8</sup> vagy 256.

     * `10.0.0.0/28` csak 16 címmel rendelkezik, és túl kicsi, mert 2<sup>(32-28)</sup> 2<sup>4</sup> vagy 16.

     A címek kiszámításával kapcsolatos további tudnivalókért lásd: [IPv4-CIDR blokkok](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Ha a [ExpressRoute](../expressroute/expressroute-introduction.md)-t használja, [létre kell hoznia egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely a következő útvonalat tartalmazza, és csatolja a táblát az ISE által használt összes alhálózathoz:

     **Név**: <*route-Name* ><br>
     **Címzési előtag**: 0.0.0.0/0<br>
     **Következő ugrás**: Internet

   1. Az **alhálózatok** listában válassza az **alhálózat konfigurációjának kezelése**lehetőséget.

      ![Alhálózat konfigurációjának kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Az **alhálózatok** ablaktáblán válassza az **alhálózat**lehetőséget.

      ![Alhálózat hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Az **alhálózat hozzáadása** panelen adja meg ezt az információt.

      * **Name (név**): az alhálózat neve
      * **Címtartomány (CIDR blokk)** : az alhálózat tartománya a virtuális hálózatban és CIDR formátumban

      ![Alhálózat adatainak hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Ha elkészült, kattintson **az OK gombra**.

   1. Ismételje meg ezeket a lépéseket három további alhálózatra.

      > [!NOTE]
      > Ha a létrehozni kívánt alhálózatok nem érvényesek, a Azure Portal egy üzenetet jelenít meg, de nem blokkolja a folyamat állapotát.

   Az alhálózatok létrehozásával kapcsolatos további információkért lásd: [virtuális hálózati alhálózat hozzáadása](../virtual-network/virtual-network-manage-subnet.md).

1. Miután az Azure sikeresen ellenőrizte az ISE-információkat, válassza a **Létrehozás**lehetőséget, például:

   ![Sikeres ellenőrzés után válassza a létrehozás lehetőséget.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Az Azure elindítja a környezet üzembe helyezését, de a folyamat befejezése előtt akár két órával *is* eltarthat. A központi telepítés állapotának megtekintéséhez az Azure eszköztárán válassza az értesítések ikont, amely megnyitja az értesítések panelt.

   ![Központi telepítés állapotának keresése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha a telepítés sikeresen befejeződött, az Azure megjeleníti ezt az értesítést:

   ![Az üzembe helyezés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Ellenkező esetben kövesse az üzembe helyezés hibaelhárításához Azure Portal utasításokat.

   > [!NOTE]
   > Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure akár egy órát is igénybe vehet az alhálózatok felszabadítása előtt. Ez azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználja ezeket az alhálózatokat egy másik ISE-ben.
   >
   > Ha törli a virtuális hálózatot, az Azure általában akár két órával az alhálózatok felszabadítása előtt is eltarthat, de ez a művelet hosszabb időt is igénybe vehet. 
   > A virtuális hálózatok törlésekor győződjön meg arról, hogy egyetlen erőforrás sincs még csatlakoztatva. 
   > Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Ha szeretné megtekinteni a környezetét, válassza az **Ugrás az erőforráshoz** lehetőséget, ha az Azure nem automatikusan a környezetbe lép az üzembe helyezés befejeződése után.

1. Az ISE hálózati állapotának ellenõrzéséhez tekintse meg az [integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)című témakört.

1. A Logic apps és más összetevők az ISE-ben való létrehozásának megkezdéséhez lásd: összetevők [hozzáadása az integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-kapacitás hozzáadása

A prémium ISE alapegység rögzített kapacitással rendelkezik, így ha több átviteli sebességre van szüksége, akkor akár a létrehozáskor, akár utána több skálázási egységet is hozzáadhat. A teljesítmény mérőszámai alapján vagy számos további feldolgozó egységen alapuló autoskálázást használhat. Ha mérőszámok alapján választja ki az automatikus skálázást, különböző feltételek közül választhat, és megadhatja a feltételek teljesítésének küszöbértékét. A fejlesztői SKU nem tartalmazza a méretezési egységek hozzáadásának képességét.

1. A Azure Portal keresse meg az ISE-t.

1. Az ISE használati és teljesítmény-metrikáinak áttekintéséhez az ISE főmenüjében válassza az **Áttekintés**lehetőséget.

   ![Az ISE használatának megtekintése](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Az automatikus skálázás beállításához a **Beállítások**területen válassza a horizontális **felskálázás**lehetőséget. A **Konfigurálás** lapon válassza az **autoskálázás engedélyezése**lehetőséget.

   ![Automatikus skálázás bekapcsolása](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Az **autoskálázási beállítás neve**mezőben adja meg a beállítás nevét.

1. Az **alapértelmezett** szakaszban válassza a **skála alapján mérőszámot** vagy a **skálázást egy adott példányszámra**.

   * Ha a példány-alapú lehetőséget választja, adja meg a 0 és 10 közötti feldolgozási egységek számát.

   * Ha a metrika-alapú elemet választja, kövesse az alábbi lépéseket:

     1. A **szabályok** szakaszban válassza a **szabály hozzáadása**elemet.

     1. A **skálázási szabály** ablaktáblán állítsa be a feltételek és a művelet végrehajtását, ha a szabály eseményindítót végez.

     1. Ha elkészült, válassza a **Hozzáadás**lehetőséget.

1. Ha elkészült az autoskálázási beállításokkal, mentse a módosításokat.

## <a name="next-steps"></a>Következő lépések

* [Összetevők hozzáadása az integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hálózati állapot ellenõrzése integrációs szolgáltatási környezetekben](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
