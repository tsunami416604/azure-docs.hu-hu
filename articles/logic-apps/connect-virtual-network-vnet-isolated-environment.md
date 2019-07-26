---
title: Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Apps egy integrációs szolgáltatási környezet (ISE) használatával
description: Integrációs szolgáltatási környezet (ISE) létrehozása, hogy a Logic apps és az integrációs fiókok hozzáférhessenek az Azure Virtual Networks (virtuális hálózatok) szolgáltatáshoz, miközben a nyilvános vagy a "globális" Azure-ból elszigetelten maradhatnak.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: cd611918b755ac3d5b6088ec6abe1711962921c7
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423156"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Appsból integrációs szolgáltatási környezet (ISE) használatával

Olyan esetekben, amikor a Logic apps és az integrációs fiókoknak hozzáférésre van szüksége egy Azure-beli [virtuális hálózathoz](../virtual-network/virtual-networks-overview.md), hozzon létre egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Az ISE egy privát és elszigetelt környezet, amely dedikált tárolót és más, a nyilvános vagy a "globális" Logic Apps szolgáltatástól elkülönített erőforrásokat használ. Ez a elkülönítés azt is csökkenti, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére.

Ha ISE-t hoz létre,  az Azure befecskendezi az ISE-t az Azure-beli virtuális hálózatba, amely ezután telepíti a Logic Apps szolgáltatást a virtuális hálózatba. Logikai alkalmazás vagy integrációs fiók létrehozásakor válassza ki az ISE helyét. A logikai alkalmazás vagy integrációs fiók ezután közvetlenül hozzáférhet az erőforrásokhoz, például a virtuális gépekhez, a kiszolgálókhoz, a rendszerekhez és a szolgáltatásokhoz a virtuális hálózaton.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Ahhoz, hogy a Logic apps és az integrációs fiókok együtt működjenek az ISE-ben, mindkettőnek *ugyanazt az ISE* -t kell használnia, mint a helyük.

Az ISE megnövelte a futtatási időtartamot, a tárterület megőrzését, az átviteli sebességet, a HTTP-kérést és a válasz időtúllépését, az üzenetek méretét és az egyéni összekötői kérelmeket. További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md). További információ a ISEs: [Azure Virtual Network-erőforrások elérése Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Győződjön meg arról, hogy a virtuális hálózat minden szükséges portja meg van nyitva, hogy a forgalom a virtuális hálózat alhálózatai között az integrációs szolgáltatási környezeten (ISE) keresztül utazzon.

* Hozza létre az integrációs szolgáltatási környezetet (ISE).

* Hozzon létre egy logikai alkalmazást, amely futhat az ISE-ben.

* Hozzon létre egy integrációs fiókot a logikai alkalmazásai számára az ISE-ben.

> [!IMPORTANT]
> A Logic apps, a beépített triggerek, a beépített műveletek és az ISE-ben futó összekötők a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md). Ha nem rendelkezik virtuális hálózattal, Ismerje meg, hogyan [hozhat létre Azure-beli virtuális hálózatot](../virtual-network/quick-create-portal.md).

  * A virtuális hálózatnak négy *üres* alhálózattal kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre és helyezzen üzembe az ISE-ben. Ezeket az alhálózatokat előre is létrehozhatja, vagy megvárhatja, amíg létre nem hozza az ISE-t, ahol egyszerre létrehozhat alhálózatokat. További információ az [alhálózatokra vonatkozó követelményekről](#create-subnet).
  
    > [!NOTE]
    > Ha a [ExpressRoute](../expressroute/expressroute-introduction.md)-t használja, amely privát kapcsolatot biztosít a Microsoft Cloud Services szolgáltatással, [létre kell hoznia egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely a következő útvonalon található, és az ISE által használt összes alhálózathoz csatolja a táblázatot:
    > 
    > **Név**: <*útvonal neve*><br>
    > **Címzési előtag**: 0.0.0.0/0<br>
    > **Következő ugrás**: Internet

  * Győződjön meg arról, hogy a virtuális hálózat [elérhetővé teszi ezeket](#ports) a portokat, így az ISE megfelelően működik, és elérhető marad.

* Ha egyéni DNS-kiszolgálókat szeretne használni az Azure-beli virtuális hálózathoz, [ezeket a lépéseket követve állítsa be ezeket](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) a kiszolgálókat, mielőtt TELEPÍTENÉ az ISE-t a virtuális hálózatra. Ellenkező esetben minden alkalommal, amikor megváltoztatja a DNS-kiszolgálót, újra kell indítania az ISE-t, amely az ISE nyilvános előzetes verziójával elérhető képesség.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="check-network-ports"></a>Hálózati portok keresése

Ha integrációs szolgáltatási környezetet (ISE) használ egy virtuális hálózattal, egy gyakori telepítési probléma egy vagy több letiltott porttal rendelkezik. Az ISE és a célként megadott rendszer közötti kapcsolatok létrehozásához használt összekötők magukban foglalhatják a saját portra vonatkozó követelményeket is. Ha például az FTP-összekötő használatával kommunikál egy FTP-rendszerrel, győződjön meg arról, hogy az adott FTP-rendszeren használt port, például a 21. port a parancsok küldéséhez lehetőség elérhető.

A virtuális hálózat azon alhálózatai közötti forgalom szabályozásához, ahol az ISE üzembe helyezését végzi, opcionálisan beállíthat [hálózati biztonsági csoportokat (NSG)](../virtual-network/security-overview.md) a virtuális hálózatban úgy, hogy az alhálózatok [közötti hálózati forgalmat szűri](../virtual-network/tutorial-filter-network-traffic.md). Ha ezt az útvonalat választja, győződjön meg arról, hogy az ISE megnyit bizonyos portokat az alábbi táblázatban leírtak szerint a NSG használó virtuális hálózaton. Ha a virtuális hálózatban meglévő NSG vagy tűzfallal rendelkezik, győződjön meg arról, hogy ezek a portok meg vannak nyitva. Így az ISE elérhető marad, és megfelelően működik, hogy ne veszítse el az ISE hozzáférését. Ellenkező esetben, ha a szükséges portok nem érhetők el, az ISE működése leáll.

Ezek a táblázatok leírják a virtuális hálózat azon portjait, amelyeket az ISE használ, és ahol a portok használatban vannak. A [Resource Manager szolgáltatás címkéi](../virtual-network/security-overview.md#service-tags) olyan IP-cím-előtagokat jelölnek, amelyek a biztonsági szabályok létrehozásakor megkönnyítik a bonyolultságot.

> [!IMPORTANT]
> Az alhálózatokon belüli belső kommunikációhoz az ISE megköveteli, hogy az alhálózatokon belül minden portot meg lehessen nyitni.

| Cél | Direction | Portok | Forrás-szolgáltatáscímke | Cél-szolgáltatáscímke | Megjegyzések |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Kommunikáció a Azure Logic Apps | Kimenő | 80 & 443 | VirtualNetwork | Internet | A port a külső szolgáltatástól függ, amellyel a Logic Apps szolgáltatás kommunikál |
| Azure Active Directory | Kimenő | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage-függőség | Kimenő | 80 & 443 | VirtualNetwork | Storage | |
| Alhálózati kommunikáció | Bejövő & kimenő | 80 & 443 | VirtualNetwork | VirtualNetwork | Az alhálózatok közötti kommunikációhoz |
| Kommunikáció Azure Logic Apps | Bejövő | 443 | Internet | VirtualNetwork | Annak a számítógépnek vagy szolgáltatásnak az IP-címe, amely a logikai alkalmazásban található kérelem-triggert vagy webhookot hívja meg. A port bezárása vagy blokkolása megakadályozza a HTTP-hívásokat a logikai alkalmazásokhoz kérelem-eseményindítókkal.  |
| Logikai alkalmazás futtatási előzményei | Bejövő | 443 | Internet | VirtualNetwork | Annak a számítógépnek az IP-címe, amelyről meg kell tekinteni a logikai alkalmazás futtatási előzményeit. Bár a port bezárása vagy blokkolása nem akadályozza meg a futtatási előzmények megtekintését, a futtatási előzményekben nem tekintheti meg az egyes lépések bemeneteit és kimeneteit. |
| Kapcsolatok kezelése | Kimenő | 443 | VirtualNetwork  | Internet | |
| Diagnosztikai naplók közzététele & metrikák | Kimenő | 443 | VirtualNetwork  | AzureMonitor | |
| Kommunikáció az Azure Traffic Manager | Bejövő | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps Designer – dinamikus tulajdonságok | Bejövő | 454 | Internet  | VirtualNetwork | A kérelmek az [adott régióban a Logic apps hozzáférési végpont bejövő IP-címeiből](../logic-apps/logic-apps-limits-and-config.md#inbound)származnak. |
| App Service felügyeleti függőség | Bejövő | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Összekötő üzembe helyezése | Bejövő | 454 & 3443 | Internet  | VirtualNetwork | Összekötők üzembe helyezéséhez és frissítéséhez szükséges. A port bezárása vagy blokkolása esetén az ISE-telepítések sikertelenek lesznek, és meggátolják az összekötők frissítését és javítását. |
| Azure SQL-függőség | Kimenő | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Kimenő | 1886 | VirtualNetwork | Internet | Állapot közzétételének Resource Health |
| API Management felügyeleti végpont | Bejövő | 3443 | APIManagement  | VirtualNetwork | |
| Függőség a naplótól az Event hub-házirendbe és a figyelési ügynökbe | Kimenő | 5672 | VirtualNetwork  | EventHub | |
| Azure cache elérése Redis-példányok között szerepkör-példányok között | Bejövő <br>Kimenő | 6379-6383 | VirtualNetwork  | VirtualNetwork | Emellett ahhoz, hogy az ISE működjön az Azure cache-sel az Redis-hez, meg kell nyitnia ezeket [a kimenő és bejövő portokat az Azure cache Redis – gyakori kérdések című témakörben](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Bejövő | * | AzureLoadBalancer | VirtualNetwork |  |
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

   | Tulajdonság | Kötelező | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés-neve*> | A környezetéhez használni kívánt Azure-előfizetés |
   | **Erőforráscsoport** | Igen | <*Azure-resource-group-name*> | Az Azure-erőforráscsoport, amelyben létre szeretné hozni a környezetet |
   | **integrációs szolgáltatási környezet neve** | Igen | <*környezet – név*> | A környezetnek megfelelő név |
   | **Location** | Igen | <*Azure-datacenter-region*> | Az Azure-adatközpont régiója, ahol üzembe helyezheti a környezetet |
   | **Termékváltozat** | Igen | **Prémium** vagy **fejlesztői (SLA nélkül)** | A létrehozandó és használandó ISE SKU. Az adatsku-változatok közötti különbségekért lásd: [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-i. |
   | **További kapacitás** | Prémium szintű <br>Igen <p><p>Developer: <br>Nem alkalmazható | Prémium szintű <br>0 – 10 <p><p>Developer: <br>Nem alkalmazható | Az ISE-erőforráshoz használandó további feldolgozási egységek száma. A kapacitás létrehozás utáni hozzáadásával kapcsolatban lásd: [ISE-kapacitás hozzáadása](#add-capacity). |
   | **Virtuális hálózat** | Igen | <*Azure-virtual-network-name*> | Az Azure-beli virtuális hálózat, ahová be szeretné szúrni a környezetét, hogy a környezetében a logikai alkalmazások hozzáférhessenek a virtuális hálózathoz. Ha nem rendelkezik hálózattal, [először hozzon létre egy Azure-beli virtuális hálózatot](../virtual-network/quick-create-portal.md). <p>**Fontos**: Ezt az injekciót *csak* akkor hajthatja végre, amikor létrehozza az ISE-t. |
   | **Alhálózatok** | Igen | <*subnet-resource-list*> | Az ISE négy *üres* alhálózatot igényel ahhoz, hogy erőforrásokat hozzon létre és helyezzen üzembe a környezetben. Az egyes alhálózatok létrehozásához [kövesse az ebben a táblázatban szereplő lépéseket](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Alhálózat létrehozása**

   Ahhoz, hogy erőforrásokat hozzon létre és helyezzen üzembe a környezetében, az ISE-nek négy olyan *üres* alhálózatra van szüksége, amely nem delegál semmilyen szolgáltatást. A környezet létrehozása után ezeket az alhálózati címeket *nem* módosíthatja. Minden alhálózatnak meg kell felelnie a következő feltételeknek:

   * Olyan névvel rendelkezik, amely alfabetikus karakterrel vagy aláhúzással kezdődik, és nem rendelkezik a következő karakterekkel `<`: `>` `%`, `&` `\\` `?`,,,,,`/`

   * Az [osztály nélküli Inter-domain Routing (CIDR) formátumot](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) és egy B osztályú címtartományt használ.

   * Legalább egy `/27` címet használ a Címterület számára, mert minden alhálózatnak *legalább 32-* es címnek kell lennie *.* Példa:

     * `10.0.0.0/27`32-es címmel rendelkezik, mert 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32.

     * `10.0.0.0/24`256-es címmel rendelkezik, mert 2<sup>(32-24)</sup> 2<sup>8</sup> vagy 256.

     * `10.0.0.0/28`a csak 16 címet tartalmaz, és túl kicsi, mert 2<sup>(32-28)</sup> 2<sup>4</sup> vagy 16.

     A címek kiszámításával kapcsolatos további tudnivalókért lásd: [IPv4-CIDR blokkok](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Ha a [ExpressRoute](../expressroute/expressroute-introduction.md)-t használja, ne feledje, hogy [hozzon létre egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely a következő útvonalat tartalmazza, és csatolja a táblázatot az ISE által használt összes alhálózathoz:

     **Név**: <*útvonal neve*><br>
     **Címzési előtag**: 0.0.0.0/0<br>
     **Következő ugrás**: Internet

   1. Az alhálózatok listában válassza az **alhálózat konfigurációjának kezelése**lehetőséget.

      ![Alhálózati konfiguráció kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Az alhálózatok ablaktáblán válassza az **alhálózat**lehetőséget.

      ![Alhálózat felvétele](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Az **alhálózat hozzáadása** panelen adja meg ezt az információt.

      * **Név**: Az alhálózat neve
      * **Címtartomány (CIDR-blokk)** : Az alhálózat tartománya a virtuális hálózaton és CIDR formátumban

      ![Alhálózat adatainak hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Ha elkészült, kattintson **az OK gombra**.

   1. Ismételje meg ezeket a lépéseket három további alhálózatra.

      > [!NOTE]
      > Ha a létrehozni kívánt alhálózatok nem érvényesek, a Azure Portal egy üzenetet jelenít meg, de nem blokkolja a folyamat állapotát.

1. Miután az Azure sikeresen ellenőrizte az ISE-információkat, válassza a **Létrehozás**lehetőséget, például:

   ![Sikeres ellenőrzés után válassza a létrehozás lehetőséget.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Az Azure elindítja a környezet üzembe helyezését, de a folyamat befejezése előtt akár két órával *is* eltarthat. A központi telepítés állapotának megtekintéséhez az Azure eszköztárán válassza az értesítések ikont, amely megnyitja az értesítések panelt.

   ![Központi telepítés állapotának keresése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha a telepítés sikeresen befejeződött, az Azure megjeleníti ezt az értesítést:

   ![A telepítés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Ellenkező esetben kövesse az üzembe helyezés hibaelhárításához Azure Portal utasításokat.

   > [!NOTE]
   > Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure akár egy órát is igénybe vehet az alhálózatok felszabadítása előtt. Ez azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználja ezeket az alhálózatokat egy másik ISE-ben.
   >
   > Ha törli a virtuális hálózatot, az Azure általában akár két órával az alhálózatok felszabadítása előtt is eltarthat, de ez a művelet hosszabb időt is igénybe vehet. 
   > A virtuális hálózatok törlésekor győződjön meg arról, hogy egyetlen erőforrás sincs még csatlakoztatva. 
   > Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Ha szeretné megtekinteni a környezetét, válassza az **Ugrás az erőforráshoz** lehetőséget, ha az Azure nem automatikusan a környezetbe lép az üzembe helyezés befejeződése után.  

Az alhálózatok létrehozásával kapcsolatos további információkért lásd: [virtuális hálózati alhálózat hozzáadása](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Logikai alkalmazás létrehozása – ISE

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza a **Logic apps** > **Hozzáadás**elemet.

   ![Új logikai alkalmazás hozzáadása az ISE-hez](./media/connect-virtual-network-vnet-isolated-environment/add-logic-app-to-ise.png)

   – vagy –

   Az Azure fő menüjében válassza az **erőforrás** > létrehozása**integrációs** > **logikai alkalmazás**lehetőséget.

1. Adja meg a logikai alkalmazáshoz használni kívánt nevet, Azure-előfizetést és Azure-erőforráscsoportot (új vagy meglévő).

1. A **hely** listában az **integrációs szolgáltatási környezetek** szakaszban válassza ki az ISE-t, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-ise.png)

   > [!IMPORTANT]
   > Ha a logikai alkalmazásait egy integrációs fiókkal szeretné használni, akkor ezeknek a logikai alkalmazásoknak és az integrációs fióknak ugyanazt az ISE-t kell használniuk.

1. Folytassa a [logikai alkalmazás létrehozását a szokásos módon](../logic-apps/quickstart-create-first-logic-app-workflow.md).

Az eseményindítók és műveletek működésének, valamint a a globális Logic Apps szolgáltatással összehasonlított ISE használata esetén a következő témakörben talál további információt: [elkülönített versus Global az ISE áttekintésében](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Integrációs fiók létrehozása – ISE

A létrehozáskor kiválasztott [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) alapján az ISE külön díj nélkül tartalmazza az integrációs fiók használatát. Az integrációs szolgáltatási környezetekben (ISE) található logikai alkalmazások csak olyan integrációs fiókokra hivatkozhatnak, amelyek ugyanabban az ISE-ban találhatók. Tehát ahhoz, hogy egy integrációs fiók működjön a Logic apps szolgáltatással egy ISE-ben, az integrációs fióknak és a Logic apps-nek *ugyanazt a környezetet* kell használnia, mint a helyük. Az integrációs fiókokkal és a ISEs kapcsolatos további [információkért lásd: integrációs fiókok és ISE.](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
)

ISE-t használó integrációs fiók létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg és nyissa meg az ISE-t, ha még nincs megnyitva. Az ISE menü **Beállítások**területén válassza az integrációs **fiókok** > **Hozzáadás**elemet.

   ![Új integrációs fiók hozzáadása az ISE-hez](./media/connect-virtual-network-vnet-isolated-environment/add-integration-account-to-ise.png)

   – vagy –

   Az Azure fő menüjében válassza az **erőforrás** > létrehozása**integrációs** > **integrációs fiók**lehetőséget.

1. Adja meg az integrációs fiókjához használni kívánt nevet, Azure-előfizetést, Azure-erőforráscsoportot (új vagy meglévő) és díjszabási szintet.

1. A **hely** lista **integrációs szolgáltatási környezetek** szakaszában válassza ki ugyanazt az ISE-t, amelyet a Logic apps használ, például:

   ![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

1. [A szokásos módon folytassa az integrációs fiók létrehozását](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-kapacitás hozzáadása

A prémium ISE alapegység rögzített kapacitással rendelkezik, így ha több átviteli sebességre van szüksége, akkor akár a létrehozáskor, akár utána több skálázási egységet is hozzáadhat. A teljesítmény mérőszámai alapján vagy számos további feldolgozó egységen alapuló autoskálázást használhat. Ha mérőszámok alapján választja ki az automatikus skálázást, különböző feltételek közül választhat, és megadhatja a feltételek teljesítésének küszöbértékét. A fejlesztői SKU nem tartalmazza a méretezési egységek hozzáadásának képességét.

1. A Azure Portal keresse meg az ISE-t.

1. Az ISE használati és teljesítmény-metrikáinak áttekintéséhez az ISE főmenüjében válassza az **Áttekintés**lehetőséget.

   ![Az ISE használatának megtekintése](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Az automatikus skálázás beállításához a **Beállítások**területen válassza a horizontális **felskálázás**lehetőséget. A **Konfigurálás** lapon válassza az **autoskálázás engedélyezése**lehetőséget.

   ![Automatikus skálázás bekapcsolása](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Az **autoskálázási beállítás neve**mezőben adja meg a beállítás nevét.

1. Az **alapértelmezett** szakaszban válassza a **skála alapján mérőszámot** vagy a skálázást **egy adott példányszámra**.

   * Ha a példány-alapú lehetőséget választja, adja meg a 0 és 10 közötti feldolgozási egységek számát.

   * Ha a metrika-alapú elemet választja, kövesse az alábbi lépéseket:

     1. A **szabályok** szakaszban válassza a **szabály hozzáadása**elemet.

     1. A **skálázási szabály** ablaktáblán állítsa be a feltételek és a művelet végrehajtását, ha a szabály eseményindítót végez.

     1. Ha elkészült, válassza a **Hozzáadás**lehetőséget.

1. Ha elkészült az autoskálázási beállításokkal, mentse a módosításokat.

## <a name="next-steps"></a>További lépések

* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
