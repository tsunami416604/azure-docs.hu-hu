---
title: Csatlakozás az Azure virtuális hálózataihoz ISE-vel
description: Integrációs szolgáltatási környezet (ISE) létrehozása, amely az Azure Logic Apps-ből elérheti az Azure virtuális hálózatokat (VNET-ket)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fedc1f6ce8fbaeaf0d2cae3a1b04169192868e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270692"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás az Azure Logic Apps-ből az Azure Logic Apps virtuális hálózataihoz integrációs szolgáltatási környezet (ISE) használatával

Olyan esetekben, amikor a logikai alkalmazások nak és az integrációs fiókoknak hozzáférésre van szükségük egy [Azure virtuális hálózathoz,](../virtual-network/virtual-networks-overview.md)hozzon létre egy [ *integrációs szolgáltatási környezetet* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Az ISE egy elszigetelt környezet, amely dedikált tárolót és más erőforrásokat használ, amelyek elkülönülnek a "globális" több-bérlős Logic Apps szolgáltatástól. Ez a szétválasztás is csökkenti a más Azure-bérlők esetleges hatása az alkalmazások teljesítményét. Az ISE saját statikus IP-címeket is biztosít. Ezek az IP-címek elkülönülnek a statikus IP-címek, amelyek a nyilvános, több-bérlős szolgáltatás logikai alkalmazások által megosztott.

Az ISE létrehozásakor az Azure *befecskendezi* az ISE-t az Azure virtuális hálózatába, amely ezután telepíti a Logic Apps szolgáltatást a virtuális hálózatba. Amikor létrehoz egy logikai alkalmazást vagy integrációs fiókot, válassza ki az ISE-t a helyükként. A logikai alkalmazás vagy az integrációs fiók ezután közvetlenül hozzáférhet a virtuális hálózat erőforrásaihoz, például a virtuális gépekhez (Virtuális gépek), kiszolgálókhoz, rendszerekhez és szolgáltatásokhoz.

![Integrációs szolgáltatáskörnyezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Ahhoz, hogy a logikai alkalmazások és az integrációs fiókok együttműködjenek az ISE-ben, mindkettőnek ugyanazt az *ISE-t* kell használnia, mint a helyüket.

Az ISE megnövelte a futtatási időtartam, a tárhelymegtartás, az átviteli sebesség, a HTTP-kérelem- és válaszidő-túllépés, az üzenetméretek és az egyéni összekötőkérelmek korlátját. További információ: [Korlátok és konfiguráció az Azure Logic Apps.](../logic-apps/logic-apps-limits-and-config.md) Az ite-ekről az [Azure Logic Apps-ből származó Hozzáférés az Azure virtuális hálózati erőforrásokhoz(Access to Azure Logic) (Hozzáférés az Azure Logic Network-erőforrásokhoz) (Access to Azure Virtual Network resources from Azure Logic Apps) (Hozzáférés az Azure Logic Network-alkalmazásokhoz) témakör](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat az Azure Portal használatával:

* Engedélyezze a hozzáférést az ISE-hez.
* Hozza létre az ISE-t.
* További kapacitást adhat az ISE-hez.

Ise-t is létrehozhat a Logic Apps REST API használatával, beleértve az ügyfél által felügyelt kulcsok beállítását:

* [Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps REST API használatával](../logic-apps/create-integration-service-environment-rest-api.md)
* [Ügyfél által kezelt kulcsok beállítása az inaktív félnek lévő adatok titkosításához az ISE-k számára](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > A logikai alkalmazások, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők a fogyasztásalapú díjcsomagtól eltérő díjszabási tervet használnak. Ha meg szeretné tudni, hogyan működik az ise-k díjszabása és számlázása, olvassa el a [Logic Apps díjszabási modelljét.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Az árak, lásd: [Logic Apps árképzés.](../logic-apps/logic-apps-pricing.md)

* [Egy Azure virtuális hálózat](../virtual-network/virtual-networks-overview.md). Ha nem rendelkezik virtuális hálózattal, ismerje meg, hogyan [hozhat létre Azure-beli virtuális hálózatot.](../virtual-network/quick-create-portal.md)

  * A virtuális hálózatnak négy *üres* alhálózattal kell rendelkeznie az ISE-ben lévő erőforrások létrehozásához és üzembe helyezéséhez. Minden alhálózat támogatja az ISE-ben használt különböző Logic Apps-összetevőt. Ezeket az alhálózatokat előre létrehozhatja, vagy megvárhatja, amíg létrehozza az ISE-t, ahol egyidejűleg alhálózatokat hozhat létre. További információ az [alhálózati követelményekről.](#create-subnet)

  * Az alhálózati neveknek betűrendes karakterrel vagy aláhúzásjellel kell `<`kezdődniük, `/`és nem használhatják a következő karaktereket: , `>` `%`, `&`, `\\`, `?`, , . 
  
  * Ha az ISE-t egy Azure Resource Manager-sablonon keresztül szeretné telepíteni, először győződjön meg arról, hogy egy üres alhálózatot delegál a Microsoft.Logic/integrationServiceEnvironment számára. Ezt a delegálást nem kell megtennie, amikor az Azure Portalon keresztül telepíti.

  * Győződjön meg arról, hogy a virtuális hálózat [lehetővé teszi az ISE elérését,](#enable-access) hogy az ISE megfelelően működjön és elérhető maradjon.

  * Ha [az ExpressRoute](../expressroute/expressroute-introduction.md)szolgáltatást használja, amely a kapcsolódási szolgáltató által támogatott, a Microsoft felhőszolgáltatásaihoz privát kapcsolatot biztosít, létre kell [hoznia egy útvonaltáblát,](../virtual-network/manage-route-table.md) amely a következő útvonalat tartalmazza, és a táblát az ISE által használt minden alhálózathoz csatolja:

    **Név**: <*útvonalnév*><br>
    **Címelőtag**: 0.0.0.0/0<br>
    **Következő ugrás**: Internet

* Ha egyéni DNS-kiszolgálókat szeretne használni az Azure virtuális hálózatához, [állítsa be ezeket a kiszolgálókat az alábbi lépések végrehajtásával,](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) mielőtt telepíti az ISE-t a virtuális hálózatra. A DNS-kiszolgáló beállításainak kezeléséről a [Virtuális hálózat létrehozása, módosítása és törlése](../virtual-network/manage-virtual-network.md#change-dns-servers)című témakörben olvashat bővebben.

  > [!NOTE]
  > Ha módosítja a DNS-kiszolgáló vagy a DNS-kiszolgáló beállításait, újra kell indítania az ISE-t, hogy az ISE feltudja venni ezeket a módosításokat. További információt az [ISE újraindítása](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)című témakörben talál.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE-hez való hozzáférés engedélyezése

Ha egy ISE-t Azure virtuális hálózattal használ, gyakori beállítási probléma egy vagy több blokkolt porttal jár. Az ISE és a célrendszerek közötti kapcsolatok létrehozásához használt összekötők saját portkövetelményekkel is rendelkezhetnek. Ha például ftp-összekötővel kommunikál egy FTP-rendszerrel, az FTP-rendszeren használt portnak rendelkezésre kell állnia, például a parancsok küldéséhez szükséges 21-es portnak.

Annak érdekében, hogy az ISE elérhető legyen, és hogy az ISE-ben lévő logikai alkalmazások képesek legyenek kommunikálni a virtuális hálózat minden alhálózatán, [nyissa meg az ebben a táblázatban leírt portokat az egyes alhálózatokhoz.](#network-ports-for-ise) Ha a szükséges portok nem érhetők el, az ISE nem fog megfelelően működni.

* Ha több ISE-példányok, amelyek ip-korlátozásokkal rendelkező más végpontokhoz való hozzáférést, telepíteni egy [Azure tűzfal](../firewall/overview.md) vagy egy [hálózati virtuális berendezés](../virtual-network/virtual-networks-overview.md#filter-network-traffic) a virtuális hálózatba, és a kimenő forgalmat a tűzfalon vagy a hálózati virtuális berendezésen keresztül. Ezután [egyetlen kimenő, nyilvános, statikus és kiszámítható IP-címet állíthat be,](connect-virtual-network-vnet-set-up-single-ip-address.md) amelyet a virtuális hálózat összes ISE-példánya használhat a célrendszerekkel való kommunikációhoz. Így nem kell további tűzfal-nyílásokat beállítania ezeken a célrendszereken az egyes ISE-khez.

   > [!NOTE]
   > Ezt a módszert egyetlen ISE-hez használhatja, ha a forgatókönyv ben korlátozni kell a hozzáférést igénylő IP-címek számát. Fontolja meg, hogy a tűzfal vagy a virtuális hálózati berendezés többletköltségei nek van-e értelme a forgatókönyvhöz. További információ az [Azure Firewall díjszabásáról.](https://azure.microsoft.com/pricing/details/azure-firewall/)

* Ha új, korlátozás nélküli Azure virtuális hálózatot és alhálózatokat hozott létre, nem kell [hálózati biztonsági csoportokat (NSG-ket) beállítania](../virtual-network/security-overview.md#network-security-groups) a virtuális hálózatban az alhálózatok közötti forgalom szabályozásához.

* Meglévő virtuális hálózaton az NSG-ket úgy is *beállíthatja,* hogy [szűri a hálózati forgalmat az alhálózatok között.](../virtual-network/tutorial-filter-network-traffic.md) Ha ezt az útvonalat szeretné használni, vagy ha már nsg-eket használ, győződjön meg arról, hogy [megnyitja a tábla portjait](#network-ports-for-ise) azon a virtuális hálózaton, ahol NSG-k vannak, vagy nsg-ket szeretne beállítani.

  > [!NOTE]
  > [Ha NSG biztonsági szabályokat](../virtual-network/security-overview.md#security-rules)használ, a TCP és az UDP protokollokat *is* használnia kell. Az NSG biztonsági szabályai azokat a portokat írják le, amelyeket meg kell nyitnia azoknak az IP-címeknek, amelyeknek hozzáférésre van szükségük ezekhez a portokhoz. Győződjön meg arról, hogy a végpontok között létező tűzfalak, útválasztók vagy egyéb elemek is biztosítják ezeket a portokat az IP-címek számára.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Az ISE által használt hálózati portok

Ez a táblázat az IsE által használt Azure virtuális hálózat portjait és a portok használatba. A biztonsági szabályok létrehozásakor az összetettség csökkentése érdekében a táblázatban lévő [szolgáltatáscímkék](../virtual-network/service-tags-overview.md) egy adott Azure-szolgáltatás IP-címelőtagainak csoportjait jelölik.

> [!IMPORTANT]
> A forrásportok rövid élettartamúak, ezért `*` ügyeljen arra, hogy az összes szabályhoz állítsa be őket. Ha jelezzük, a belső ISE és a külső ISE az [ISE létrehozásakor kiválasztott végpontra](connect-virtual-network-vnet-isolated-environment.md#create-environment)vonatkozik. További információ: [Endpoint access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Cél | Irány | Célportok | Forrásszolgáltatás címkéje | Cél szolgáltatáscímkéje | Megjegyzések |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Intersubnet kommunikáció a virtuális hálózaton belül | Bejövő & kimenő | * | Az ISE alhálózatait tartalmazó virtuális hálózat címtere | Az ISE alhálózatait tartalmazó virtuális hálózat címtere | A virtuális hálózat alhálózatai *közötti* forgalom áramlásához szükséges. <p><p>**Fontos:** Ahhoz, hogy a forgalom az egyes alhálózatok *összetevői* között folyjon, győződjön meg arról, hogy az egyes alhálózatokon belüli összes portot megnyitja. |
| Kommunikáció a logikai alkalmazással | Bejövő | 443 | Belső ISE: <br>VirtualNetwork <p><p>Külső ISE: <br>Internet <br>**(lásd: Megjegyzések** oszlop) | VirtualNetwork | Az **internetes** szolgáltatáscímke használata helyett megadhatja annak a számítógépnek vagy szolgáltatásnak a forrás IP-címét, amely a logikai alkalmazásban minden kérési eseményindítót vagy webhookot meghívja. <p><p>**Fontos:** A port bezárása vagy blokkolása megakadályozza a http-hívásokat a kérési eseményindítókkal rendelkező logikai alkalmazások hoz. |
| A logikai alkalmazás futtatási előzményei | Bejövő | 443 | Belső ISE: <br>VirtualNetwork <p><p>Külső ISE: <br>Internet <br>**(lásd: Megjegyzések** oszlop) | VirtualNetwork | Az **internetes** szolgáltatáscímke használata helyett megadhatja annak a számítógépnek vagy szolgáltatásnak a forrás IP-címét, amelyből meg szeretné tekinteni a logikai alkalmazás futtatási előzményeit. <p><p>**Fontos:** Bár a port bezárása vagy blokkolása nem akadályozza meg a futtatási előzmények megtekintését, nem tekintheti meg az adott futtatási előzmények minden lépéséhez. |
| Logic Apps Designer – dinamikus tulajdonságok | Bejövő | 454 | LogicAppsManagement | VirtualNetwork | A kérelmek a Logic Apps hozzáférési végpont bejövő IP-címek az adott [régióban](../logic-apps/logic-apps-limits-and-config.md#inbound) érkeznek. |
| Összekötő központi telepítése | Bejövő | 454 | AzureConnectors | VirtualNetwork | Összekötők üzembe helyezéséhez és frissítéséhez szükséges. A port bezárása vagy blokkolása az ISE központi telepítései sikertelenek lesznek, és megakadályozzák az összekötők frissítéseit vagy javítását. |
| Hálózati állapot-ellenőrzés | Bejövő | 454 | LogicApps | VirtualNetwork | A kérelmek a Logic Apps hozzáférési végponttól érkeznek az adott régió [bejövő](../logic-apps/logic-apps-limits-and-config.md#inbound) és [kimenő](../logic-apps/logic-apps-limits-and-config.md#outbound) IP-címeihez. |
| App Service Management-függőség | Bejövő | 454, 455 | AppServiceManagement szolgáltatás | VirtualNetwork | |
| Kommunikáció az Azure Traffic Manager-től | Bejövő | Belső ISE: 454 <p><p>Külső ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| API Management - felügyeleti végpont | Bejövő | 3443 | APIManagement | VirtualNetwork | |
| Összekötő házirend üzembe helyezése | Bejövő | 3443 | APIManagement | VirtualNetwork | Összekötők üzembe helyezéséhez és frissítéséhez szükséges. A port bezárása vagy blokkolása az ISE központi telepítései sikertelenek lesznek, és megakadályozzák az összekötők frissítéseit vagy javítását. |
| Kommunikáció a logikai alkalmazásból | Kimenő | 80, 443 | VirtualNetwork | Az úti céltól függően változik | A külső szolgáltatás végpontjai, amelyekkel a logikai alkalmazásnak kommunikálnia kell. |
| Azure Active Directory | Kimenő | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Kapcsolatkezelés | Kimenő | 443 | VirtualNetwork  | AppService | |
| Diagnosztikai naplók közzététele & metrikák | Kimenő | 443 | VirtualNetwork  | AzureMonitor | |
| Az Azure Storage-függőség | Kimenő | 80, 443, 445 | VirtualNetwork | Storage | |
| Azure SQL-függőség | Kimenő | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Kimenő | 1886 | VirtualNetwork | AzureMonitor | Az állapot erőforrás-állapotban való közzétételéhez szükséges |
| Függőség a Naplótól az Event Hub-házirendig és a figyelési ügynökig | Kimenő | 5672 | VirtualNetwork | EventHub | |
| Access Azure-gyorsítótár a szerepkörpéldányok közötti Redis-példányokhoz | Bejövő <br>Kimenő | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Ahhoz is, hogy az ISE együttműködjön az Azure Cache for Redis szolgáltatással, meg kell nyitnia [ezeket a kimenő és bejövő portokat, amelyeket az Azure Cache for Redis GYIK című kérdésekben ismertetett.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE létrehozása

1. Az [Azure Portalon](https://portal.azure.com)a fő Azure-keresőmezőben adja meg `integration service environments` szűrőként, és válassza az **Integrációs szolgáltatáskörnyezetek**lehetőséget.

   ![Az "Integrációs szolgáltatás környezetei" kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Az **Integrációs szolgáltatás környezetei** ablaktáblán válassza a **Hozzáadás lehetőséget.**

   ![Az "Integrációs szolgáltatás környezetei" kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Adja meg ezeket az adatokat a környezetéhez, majd válassza **a Véleményezés + létrehozás**lehetőséget, például:

   ![Környezeti adatok biztosítása](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés-név*> | A környezetéhez használandó Azure-előfizetés |
   | **Erőforráscsoport** | Igen | <*Azure-erőforrás-csoport név*> | Új vagy meglévő Azure-erőforráscsoport, ahol létre szeretné hozni a környezetet |
   | **Integrációs szolgáltatás környezetének neve** | Igen | <*környezet neve*> | Az ISE-név, amely csak betűket, számokat, kötőjeleket (`-`), aláhúzást (`_`és pont (`.`) tartalmazhat. |
   | **Helyen** | Igen | <*Azure-adatközpont-régió*> | Az Azure adatközpont-régió, ahol a környezetet üzembe helyezheti |
   | **Sku** | Igen | **Prémium** vagy **fejlesztő (Nincs SLA)** | Az ISE termékváltozat létrehozása és használata. A sk-ek közötti különbségekről az [ISE ski-k ben található.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) <p><p>**Fontos**: Ez a beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. |
   | **További kapacitás** | Prémium: <br>Igen <p><p>Fejlesztő: <br>Nem alkalmazható | Prémium: <br>0-tól 10-ig <p><p>Fejlesztő: <br>Nem alkalmazható | Az ISE-erőforráshoz használandó további feldolgozási egységek száma. A létrehozás utáni kapacitás hozzáadásáról az [ISE-kapacitás hozzáadása .Add ISE capacity](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Hozzáférési végpont** | Igen | **Belső** vagy **külső** | Az ISE-hez használandó hozzáférési végpontok típusa. Ezek a végpontok határozzák meg, hogy a kérés vagy a webhook-eseményindítók az ISE-ben lévő logikai alkalmazásokon fogadhatnak-e hívásokat a virtuális hálózaton kívülről. <p><p>A kijelölés azt is befolyásolja, hogy a logikai alkalmazásban hogyan tekintheti meg és érheti el a bemeneteket és kimeneteket. További információ: [ISE végponthozzáférés.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) <p><p>**Fontos**: Ez a beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. |
   | **Virtuális hálózat** | Igen | <*Azure-virtuális hálózat neve*> | Az Azure virtuális hálózat, ahol szeretné beadni a környezetet, így a logikai alkalmazások ebben a környezetben hozzáférhetnek a virtuális hálózathoz. Ha nem rendelkezik hálózattal, [először hozzon létre egy Azure-beli virtuális hálózatot.](../virtual-network/quick-create-portal.md) <p><p>**Fontos:** Ezt az injekciót *csak* akkor tudja beadni, amikor létrehozza az ISE-t. |
   | **Alhálózatok** | Igen | <*alhálózati erőforráslista*> | Az ISE-nek négy *üres* alhálózatra van szüksége az erőforrások létrehozásához és üzembe helyezéséhez a környezetben. Az egyes alhálózatok létrehozásához [kövesse a táblázat lépéseit.](#create-subnet) |
   |||||

   <a name="create-subnet"></a>

   **Alhálózat létrehozása**

   Erőforrások létrehozásához és üzembe helyezéséhez a környezetben, az ISE négy *üres* alhálózatok, amelyek nem delegált egyetlen szolgáltatás. Minden alhálózat támogatja az ISE-ben használt különböző Logic Apps-összetevőt. A környezet létrehozása után ezek az alhálózati címek *nem* módosíthatók. Minden alhálózatnak meg kell felelnie ezeknek a követelményeknek:

   * Neve betűs karakterrel vagy aláhúzásjellel kezdődik (számok nélkül), és `<`nem `>` `%`használja `&` `\\`a `?`következő karaktereket: , , , , , , , , . `/`

   * A [Classless Inter-Domain Routing (CIDR) formátumot](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) és a B osztályú címteret használja.

   * Legalább egy `/27` használatot használ a címtérben, mert minden alhálózatnak legalább 32 címet kell *meghívnia.* Példa:

     * `10.0.0.0/28`csak 16 címmel rendelkezik, és túl kicsi, mert a 2<sup>(32-28)</sup> 2<sup>4</sup> vagy 16.

     * `10.0.0.0/27`32 címmel rendelkezik, mert a 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32.

     * `10.0.0.0/24`256 címmel rendelkezik, mert a 2<sup>(32-24)</sup> 2<sup>8</sup> vagy 256. Azonban több cím nem nyújt további előnyöket.

     A címek kiszámításáról az [IPv4 CIDR-blokkok ban](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)olvashat bővebben.

   * [Ha az ExpressRoute-ot](../expressroute/expressroute-introduction.md)használja, létre kell [hoznia egy útvonaltáblát,](../virtual-network/manage-route-table.md) amely a következő útvonalat és csatolást rendelkezik az ISE által használt minden alhálózattal:

     **Név**: <*útvonalnév*><br>
     **Címelőtag**: 0.0.0.0/0<br>
     **Következő ugrás**: Internet

   1. Az **Alhálózatok** listában válassza az **Alhálózat-konfiguráció kezelése lehetőséget.**

      ![Alhálózati konfiguráció kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Az **Alhálózatok** ablaktáblán válassza az **Alhálózat**lehetőséget.

      ![Négy üres alhálózat hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Az **Alhálózat hozzáadása** ablaktáblán adja meg ezt az információt.

      * **Név**: Az alhálózat neve
      * **Címtartomány (CIDR-blokk):** Az alhálózat tartománya a virtuális hálózatban és CIDR formátumban

      ![Alhálózat részleteinek hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Amikor elkészült, válassza az **OK** lehetőséget.

   1. Ismételje meg ezeket a lépéseket további három alhálózat esetén.

      > [!NOTE]
      > Ha a létrehozni kívánt alhálózatok érvénytelenek, az Azure Portal on egy üzenetet jelenít meg, de nem blokkolja a folyamatot.

   Az alhálózatok létrehozásáról a [Virtuális hálózati alhálózat hozzáadása](../virtual-network/virtual-network-manage-subnet.md)című témakörben talál további információt.

1. Miután az Azure sikeresen érvényesítette az ISE-adatokat, válassza a **Létrehozás**lehetőséget, például:

   ![Sikeres ellenőrzés után válassza a "Létrehozás" lehetőséget](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Az Azure megkezdi a környezet üzembe helyezését, amely általában két órán belül befejeződik. Esetenként a telepítés akár négy órát is igénybe vehet. A központi telepítés állapotának ellenőrzéséhez az Azure-eszköztáron válassza ki az értesítések ikonját, amely megnyitja az értesítések ablaktábláját.

   ![A telepítés állapotának ellenőrzése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha a telepítés sikeresen befejeződik, az Azure megjeleníti ezt az értesítést:

   ![A telepítés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Ellenkező esetben kövesse az Azure Portal on-elhárítási útmutató.

   > [!NOTE]
   > Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure-nak akár egy órába is telhet, mielőtt felszabadítja az alhálózatokat. Ez a késleltetés azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználná ezeket az alhálózatokat egy másik ISE-ben.
   >
   > Ha törli a virtuális hálózatot, az Azure általában akár két órát is igénybe vehet az alhálózatok felszabadítása előtt, de ez a művelet hosszabb időt vehet igénybe. 
   > Virtuális hálózatok törlésekor győződjön meg arról, hogy nincs enek erőforrások csatlakoztatva. 
   > Lásd: [Virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. A környezet megtekintéséhez válassza az **Ugrás az erőforrásra** lehetőséget, ha az Azure nem megy automatikusan a környezetbe a központi telepítés befejezése után.

1. Az ISE hálózati állapotának ellenőrzéséhez olvassa [el az Integrációs szolgáltatás környezetének kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)című témakört.

1. A logikai alkalmazások és egyéb összetevők létrehozásának megkezdéséről az [Erőforrások hozzáadása integrációs szolgáltatáskörnyezethez](../logic-apps/add-artifacts-integration-service-environment-ise.md)című témakörben található.

   > [!IMPORTANT]
   > Az ISE létrehozása után elérhetővé vált felügyelt ISE-összekötők nem jelennek meg automatikusan a Logic App Designer összekötőválasztójában. Az ISE-összekötők használata előtt manuálisan kell [hozzáadnia ezeket az összekötőket az ISE-hez,](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) hogy azok megjelenjenek a Logic App Designerben.

## <a name="next-steps"></a>További lépések

* [Erőforrások hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* További információ az [Azure virtuális hálózatról](../virtual-network/virtual-networks-overview.md)
* További információ [az Azure-szolgáltatások virtuális hálózati integrációjáról](../virtual-network/virtual-network-for-azure-services.md)
