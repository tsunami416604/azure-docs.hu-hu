---
title: Az Azure Databricks üzembe helyezése virtuális hálózaton
description: Ez a cikk azt ismerteti, hogyan helyezhetők üzembe Azure Databricks a virtuális hálózatban (más néven VNet Injection).
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 0bb3221c201e6dd4dd17cca8ef7e3ed3331de228
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432661"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Az Azure Databricks üzembe helyezése virtuális hálózaton

Az Azure Databricks alapértelmezett központi telepítése egy teljes körűen felügyelt Azure-beli szolgáltatás: az összes adatsík-erőforrás, beleértve a virtuális hálózatot (VNet) egy zárolt erőforráscsoporthoz van telepítve. Ha azonban hálózati testreszabásra van szüksége, Azure Databricks erőforrásokat telepíthet a saját virtuális hálózatában (más néven VNet Injection), amikor a következőket teszi lehetővé:

* Más Azure-szolgáltatásokhoz (például az Azure Storage-hoz) való Azure Databricks kapcsolódás biztonságosabb módon, a szolgáltatási végpontok használatával.
* A helyszíni adatforrásokhoz való kapcsolódáshoz a Azure Databricks használható, a felhasználó által megadott útvonalak kihasználása mellett.
* Csatlakoztasson Azure Databrickst egy hálózati virtuális berendezéshez az összes kimenő forgalom vizsgálatához, és tegyen lépéseket az engedélyezési és megtagadási szabályoknak megfelelően.
* Azure Databricks konfigurálása egyéni DNS használatára.
* Konfigurálja a hálózati biztonsági csoport (NSG) szabályait a kimenő forgalomra vonatkozó korlátozások megadásához.
* Azure Databricks fürtök üzembe helyezése a meglévő virtuális hálózaton.

Azure Databricks erőforrások saját virtuális hálózatra való üzembe helyezése lehetővé teszi a rugalmas CIDR-tartományok (16-/24 bárhol a virtuális hálózat és az alhálózatok közötti, illetve 18-/26 közötti) kihasználását is.

  > [!NOTE]
  > Meglévő munkaterület virtuális hálózatát nem lehet lecserélni. Ha az aktuális munkaterület nem tudja kielégíteni a szükséges számú aktív fürtöt, hozzon létre egy másik munkaterületet egy nagyobb virtuális hálózatban. Kövesse az [alábbi részletes áttelepítési lépéseket](howto-regional-disaster-recovery.md#detailed-migration-steps) az erőforrások (jegyzetfüzetek, fürtkonfiguráció, feladatok) a régiről az új munkaterületre való másolásához.

## <a name="virtual-network-requirements"></a>Virtuális hálózati követelmények

A Azure Portal Azure Databricks munkaterület telepítési felületének használatával automatikusan konfigurálhat egy meglévő virtuális hálózatot a szükséges alhálózatokkal, a hálózati biztonsági csoporttal és a engedélyezési beállításokkal, vagy használhatja a Azure Resource Manager sablonok a virtuális hálózat konfigurálásához és a munkaterület üzembe helyezéséhez.

A Azure Databricks munkaterületet üzembe helyező virtuális hálózatnak meg kell felelnie a következő követelményeknek:

### <a name="location"></a>Földrajzi egység

A virtuális hálózatnak a Azure Databricks munkaterülettel azonos helyen kell lennie.

### <a name="subnets"></a>Alhálózatok

A virtuális hálózatnak két, Azure Databricks számára dedikált alhálózatot kell tartalmaznia:

   1. Olyan konfigurált hálózati biztonsági csoporttal rendelkező magánhálózati alhálózat, amely lehetővé teszi a fürt belső kommunikációját

   2. Olyan konfigurált hálózati biztonsági csoporttal rendelkező nyilvános alhálózat, amely lehetővé teszi a kommunikációt a Azure Databricks vezérlő síkjával.

### <a name="address-space"></a>Címtér

A virtuális hálózat/16-/24 közötti CIDR-blokk, valamint a magán-és nyilvános alhálózatok között a/18-/26 közötti CIDR-blokk.

### <a name="whitelisting"></a>Engedélyezés

Az alhálózatok és a Azure Databricks-vezérlési sík közötti összes kimenő és bejövő forgalomnak engedélyezési listának kell lennie.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ez a szakasz azt ismerteti, hogyan hozható létre Azure Databricks munkaterület a Azure Portalban, és hogyan telepíthető a saját meglévő virtuális hálózatában. Azure Databricks frissíti a virtuális hálózatot két új alhálózattal és hálózati biztonsági csoporttal, az Ön által biztosított CIDR-tartományokat használva, engedélyezheti a bejövő és kimenő alhálózati forgalmat, és üzembe helyezi a munkaterületet a frissített virtuális hálózaton.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy virtuális hálózattal, amelyre telepíteni fogja a Azure Databricks munkaterületet. Használhat meglévő virtuális hálózatot, vagy létrehozhat egy újat, de a virtuális hálózatnak ugyanabban a régióban kell lennie, mint a létrehozni kívánt Azure Databricks munkaterületnek. A virtuális hálózathoz a/16-/24 közötti CIDR-tartomány szükséges.

  > [!Warning]
  > Egy kisebb virtuális hálózattal rendelkező munkaterület – azaz alacsonyabb CIDR-tartomány – az IP-címek (hálózati terület) gyorsabban futhatnak, mint egy nagyobb virtuális hálózattal rendelkező munkaterület. Egy/24 virtuális hálózattal és/26 alhálózattal rendelkező munkaterületen például legfeljebb 64 csomópont lehet aktív egyszerre, míg egy/20 virtuális hálózattal és/22 alhálózattal rendelkező munkaterület legfeljebb 1024 csomópontot tud elhelyezni.

  Az alhálózatok automatikusan létrejönnek a munkaterület konfigurálásakor, és lehetősége lesz az alhálózatok CIDR-tartományának megadására a konfiguráció során.

## <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

1. A Azure Portal válassza az **+ erőforrás létrehozása > Analytics > Azure Databricks** lehetőséget a Azure Databricks szolgáltatás párbeszédpanel megnyitásához.

2. Kövesse a 2. lépés: Azure Databricks munkaterület létrehozása című témakörben leírt konfigurációs lépéseket a Első lépések útmutatóban, majd válassza az üzembe helyezés Azure Databricks munkaterületet az Virtual Network lehetőségnél.

   ![Azure Databricks szolgáltatás létrehozása](./media/vnet-injection/create-databricks-service.png)

3. Válassza ki a használni kívánt virtuális hálózatot.

   ![Virtuális hálózati beállítások](./media/vnet-injection/select-vnet.png)

4. CIDR-tartományokat adjon meg a/18-/26 két alhálózat közötti blokkban, Azure Databricks:

   * A rendszer egy nyilvános alhálózatot hoz létre egy társított hálózati biztonsági csoporttal, amely lehetővé teszi a kommunikációt a Azure Databricks vezérlő síkjával.
   * A privát alhálózat egy társított hálózati biztonsági csoporttal jön létre, amely lehetővé teszi a fürt belső kommunikációját.

5. A **Létrehozás** gombra kattintva telepítheti az Azure Databricks munkaterületet a virtuális hálózatra.

## <a name="advanced-resource-manager-configurations"></a>Speciális Resource Manager-konfigurációk

Ha nagyobb mértékben szeretné szabályozni a virtuális hálózat konfigurációját – például meglévő alhálózatokat szeretne használni, használja a meglévő hálózati biztonsági csoportokat, vagy hozza létre saját biztonsági szabályait – a következő Azure Resource Manager sablonokat használhatja a a portál virtuális hálózatának konfigurációja és a munkaterület üzembe helyezése.

### <a name="all-in-one"></a>Mindez egy

Ha egy virtuális hálózatot, hálózati biztonsági csoportokat és Azure Databricks munkaterületet szeretne létrehozni, használja az [all-in-One sablont a Databricks VNet befecskendezett munkaterületek számára](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

A sablon használatakor nincs szükség az alhálózati forgalom manuális engedélyezési listájának megadására.

### <a name="network-security-groups"></a>Hálózati biztonsági csoportok

Ha hálózati biztonsági csoportokat szeretne létrehozni egy meglévő virtuális hálózathoz szükséges szabályokkal, használja a [hálózati biztonsági csoport sablonját a Databricks VNet injekcióhoz](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

A sablon használatakor nincs szükség az alhálózati forgalom manuális engedélyezési listájának megadására.

### <a name="virtual-network"></a>Virtuális hálózat

A megfelelő nyilvános és magánhálózati alhálózatokkal rendelkező virtuális hálózatok létrehozásához használja a [Virtual Network sablont a Databricks VNet injekcióhoz](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Ha ezt a sablont a hálózati biztonsági csoportok sablonjának használata nélkül használja, manuálisan kell hozzáadnia a engedélyezési szabályokat a virtuális hálózattal használt hálózati biztonsági csoportokhoz.

### <a name="azure-databricks-workspace"></a>Azure Databricks munkaterület

Ha Azure Databricks-munkaterületet szeretne üzembe helyezni egy olyan meglévő virtuális hálózaton, amely nyilvános és magánhálózati alhálózatokkal rendelkezik, és a megfelelően konfigurált hálózati biztonsági csoportokat már beállította, használja a [Databricks VNet-befecskendezés munkaterület-sablonját](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Ha ezt a sablont a hálózati biztonsági csoportok sablonjának használata nélkül használja, manuálisan kell hozzáadnia a engedélyezési szabályokat a virtuális hálózattal használt hálózati biztonsági csoportokhoz.

## <a name="whitelisting-subnet-traffic"></a>Alhálózati forgalom engedélyezési listája

Ha nem használja a hálózati biztonsági csoportok létrehozásához [Azure Portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) vagy [Azure Resource Manager sablonokat](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) , manuálisan kell megadnia a következő forgalmat az alhálózatokon.

|Irány|Protocol (Protokoll)|Forrás|Forrásport|Cél|Célport|
|---------|--------|------|-----------|-----------|----------------|
|Bejövő|\*|VirtualNetwork|\*|\*|\*|
|Bejövő|\*|Vezérlési sík NAT IP-címe|\*|\*|22|
|Bejövő|\*|Vezérlési sík NAT IP-címe|\*|\*|5557|
|Kimenő|\*|\*|\*|WebApp IP-címe|\*|
|Kimenő|\*|\*|\*|SQL (szolgáltatás címkéje)|\*|
|Kimenő|\*|\*|\*|Storage (szolgáltatás címkéje)|\*|
|Kimenő|\*|\*|\*|VirtualNetwork|\*|

Az alhálózati forgalom engedélyezési listája a következő IP-címek használatával. Az SQL (metaadattár) és a Storage (összetevő-és naplózási tároló) esetében az SQL és a Storage [szolgáltatás címkéit](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)kell használnia.

|Azure Databricks régió|Szolgáltatás|Nyilvános IP-cím|
|-----------------------|-------|---------|
|USA keleti régiója|Vezérlési sík NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA 2. keleti régiója|Vezérlési sík NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA északi középső régiója|Vezérlési sík NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA középső régiója|Vezérlési sík NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA déli középső régiója|Vezérlési sík NAT </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA nyugati régiója|Vezérlési sík NAT </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA 2. nyugati régiója|Vezérlési sík NAT </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Közép-Kanada|Vezérlési sík NAT </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kelet-Kanada|Vezérlési sík NAT </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Egyesült Királyság nyugati régiója|Vezérlési sík NAT </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Egyesült Királyság déli régiója|Vezérlési sík NAT </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Nyugat-Európa|Vezérlési sík NAT </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Észak-Európa|Vezérlési sík NAT </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Közép-India|Vezérlési sík NAT </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Dél-India|Vezérlési sík NAT </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Nyugat-India|Vezérlési sík NAT </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Délkelet-Ázsia|Vezérlési sík NAT </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Kelet-Ázsia|Vezérlési sík NAT </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Ausztrália keleti régiója|Vezérlési sík NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Délkelet-Ausztrália|Vezérlési sík NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Ausztrália középső régiója|Vezérlési sík NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Ausztrália 2. középső régiója|Vezérlési sík NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Kelet-Japán|Vezérlési sík NAT </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Nyugat-Japán|Vezérlési sík NAT </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Hibakeresés

### <a name="workspace-launch-errors"></a>Munkaterület indítási hibái

Ha a munkaterületet egy egyéni virtuális hálózatban indítja el, a Azure Databricks bejelentkezési képernyőjén a következő hibaüzenet jelenik meg: **"hiba történt a munkaterület létrehozásakor. Győződjön meg arról, hogy az egyéni hálózati konfiguráció helyes, és próbálkozzon újra. "**

Ezt a hibát egy olyan hálózati konfiguráció okozta, amely nem felel meg a követelményeknek. A munkaterület létrehozásakor ellenőrizze, hogy követte-e a témakör utasításait.

### <a name="cluster-creation-errors"></a>Fürtök létrehozásával kapcsolatos hibák

**A példányok nem érhetők el: az erőforrások nem érhetők el SSH-n keresztül.**

Lehetséges ok: a vezérlési síkon a dolgozók felé irányuló forgalom blokkolva van. Javítsa ki, hogy a bejövő biztonsági szabályok megfelelnek-e a követelményeknek. Ha a helyszíni hálózathoz csatlakoztatott meglévő virtuális hálózatra telepíti a szolgáltatást, tekintse át a telepítőt a Azure Databricks munkaterület csatlakoztatása a helyszíni hálózathoz című témakörben leírtak szerint.

**Váratlan indítási hiba: váratlan hiba történt a fürt beállítása közben. Ha a probléma továbbra is fennáll, próbálkozzon újra és lépjen kapcsolatba Azure Databricks. Belső hibaüzenet: időtúllépés a csomópont elhelyezése közben.**

Lehetséges ok: a feldolgozók és az Azure Storage-végpontok felé irányuló forgalom blokkolva van. Javítsa ki, hogy a kimenő biztonsági szabályok megfelelnek-e a követelményeknek. Ha egyéni DNS-kiszolgálókat használ, ellenőrizze a virtuális hálózat DNS-kiszolgálóinak állapotát is.

**A felhőalapú szolgáltató indítási hibája: a fürt beállítása során hiba történt A felhőalapú szolgáltatónál. További információért tekintse meg a Azure Databricks útmutatót. Azure-hibakód: AuthorizationFailed/InvalidResourceReference.**

Lehetséges ok: a virtuális hálózat vagy az alhálózatok már nem léteznek. Győződjön meg arról, hogy a virtuális hálózat és az alhálózatok léteznek.

**A fürt leállt. Ok: Spark indítási hiba: a Spark nem tudott elindulni az időben. Ezt a problémát az okozhatja, hogy hibás Hive-metaadattár, érvénytelen Spark-konfigurációk vagy hibás inicializálási parancsfájlok találhatók. A probléma elhárításához tekintse meg a Spark-illesztőprogram naplófájljait, és lépjen kapcsolatba a Databricks, ha a probléma továbbra is fennáll. Belső hibaüzenet: a Spark nem indult el: az illesztőprogram nem indult el időben.**

Lehetséges ok: a tároló nem tud kommunikálni az üzemeltetési példánnyal vagy a DBFS-fiókkal. Javítsa ki az egyéni útvonalat az alhálózatokhoz a DBFS Storage-fiókhoz a következő ugrás az internetre való hozzáadásával.

### <a name="notebook-command-errors"></a>A notebook parancs hibái

**A parancs nem válaszol**

Lehetséges ok: a feldolgozók közötti kommunikáció le van tiltva. Javítsa ki, hogy a bejövő biztonsági szabályok megfelelnek-e a követelményeknek.

**A jegyzetfüzet-munkafolyamat sikertelen, a kivétel: com. databricks. WorkflowException: org. Apache. http. Conn. ConnectTimeoutException**

Lehetséges ok: a dolgozóktól a Azure Databricks WebApp felé irányuló forgalom blokkolva van. Javítsa ki, hogy a kimenő biztonsági szabályok megfelelnek-e a követelményeknek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)
