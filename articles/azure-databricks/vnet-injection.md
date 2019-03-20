---
title: A virtuális hálózaton (előzetes verzió) üzembe helyezése az Azure Databricks
description: Ez a cikk ismerteti, hogyan helyezhet üzembe az Azure Databricks a virtuális hálózathoz, más néven a virtuális hálózatok közötti injektálási.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: c29d2e1df0979481c0c8a1e1f2cd4d22b013212a
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227627"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>A virtuális hálózaton (előzetes verzió) üzembe helyezése az Azure Databricks

Az alapértelmezett üzembe helyezés az Azure Databricks egy olyan teljes körűen felügyelt szolgáltatás az Azure-ban: zárolt erőforráscsoportban üzembe helyezett összes adatsík erőforrások egy virtuális hálózatot (VNet), beleértve. Ha hálózati testreszabási van szükség, azonban is telepítheti az Azure Databricks-erőforrások a saját virtuális hálózaton (is nevű VNet injektálás), amikor teszi lehetővé:

* Az Azure Databricks csatlakozhat más Azure-szolgáltatások (például az Azure Storage) Szolgáltatásvégpontok használata biztonságosabb módon.
* Csatlakozhat a helyszíni adatok források használható az Azure Databricks, kihasználhatja a felhasználó által megadott útvonalakat.
* Csatlakozás az Azure Databricks műveletek a következők szerint minden kimenő forgalom vizsgálata és a egy hálózati virtuális berendezésre engedélyezési és megtagadási szabályoknak.
* Az Azure Databricks használata az egyéni DNS konfigurálása.
* Konfigurálja a hálózati biztonsági csoport (NSG) szabályai kimenő forgalom korlátozások megadásához.
* A meglévő virtuális hálózatot az Azure Databricks-fürtök üzembe helyezése.

Üzembe helyezni az Azure Databricks-erőforrásokat a saját virtuális hálózatához is lehetővé teszi, hogy kihasználhatja a rugalmas CIDR-tartományt (bárhol közötti /16-/ 24 a virtuális hálózat és közötti /18-/ 26 alhálózatokra vonatkozó).

  > [!NOTE]
  > A virtuális hálózatot egy meglévő munkaterületet nem cserélhető le. Ha az aktuális munkaterületen nem tud biztosítja a szükséges számú aktív fürtcsomópont, hozzon létre egy másik munkaterületet egy nagyobb méretű virtuális hálózatban. Hajtsa végre a [a részletes áttelepítési lépéseit](howto-regional-disaster-recovery.md#detailed-migration-steps) átmásolása a régi (notebookok, fürtkonfigurációkat, feladatok) erőforrások új munkaterületet.

## <a name="virtual-network-requirements"></a>Virtuális hálózat követelményei

Az Azure Portalon az Azure Databricks munkaterületen üzembe helyezési felület segítségével automatikusan konfigurálja a szükséges alhálózatok, hálózati biztonsági csoport és engedélyezési beállítások meglévő virtuális hálózattal, vagy használhatja az Azure Resource Manager a sablonok a virtuális hálózat konfigurálása és üzembe helyezése a munkaterületen.

A virtuális hálózat úgy, hogy az Azure Databricks-munkaterületen az alábbi követelményeknek kell megfelelnie:

### <a name="location"></a>Hely

A virtuális hálózat az Azure Databricks-munkaterület megegyező helyen kell lennie.

### <a name="subnets"></a>Alhálózatok

A virtuális hálózat két alhálózattal, az Azure databricks dedikált kell tartalmaznia:

   1. Egy privát alhálózatra, amely lehetővé teszi, hogy a fürt belső kommunikációs konfigurált hálózati biztonsági csoporttal

   2. Egy nyilvános alhálózatot, amely lehetővé teszi a kommunikációt az Azure Databricks vezérlősík konfigurált hálózati biztonsági csoporttal.

### <a name="address-space"></a>Címtér

A virtuális hálózat /16 – / 24 és a CIDR-blokk közötti /18-/ 26-os a privát és nyilvános alhálózatok közötti CIDR-blokk.

### <a name="whitelisting"></a>Engedélyezés

Minden kimenő és bejövő forgalmat az alhálózatok és az Azure Databricks vezérlősík között kell lennie az engedélyezési listán.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ez a szakasz ismerteti az Azure Portalon az Azure Databricks-munkaterület létrehozása és üzembe helyezni a saját meglévő virtuális hálózattal. Az Azure Databricks frissíti a virtuális hálózat két új alhálózatokra és hálózati biztonsági csoportok segítségével az Ön által megadott CIDR-tartományt, listáinak bejövő és kimenő alhálózata forgalmat, és a munkaterület üzembe helyezi a frissített virtuális hálózaton.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy virtuális hálózatot, amelyre központilag telepíti az Azure Databricks-munkaterület. Használjon egy meglévő virtuális hálózatot, vagy hozzon létre egy új, de a virtuális hálózat és az Azure Databricks-munkaterületet, amelyet szeretne létrehozni ugyanabban a régióban kell lennie. A CIDR-tartományt /16-/24 között szükség a virtuális hálózat.

  > [!Warning]
  > Egy munkaterületet, és a kisebb virtuális hálózat –, alacsonyabb CIDR-tartományt – is elfogy a (hálózati hely) IP-címek gyorsabban egy munkaterületet, és a nagyobb virtuális hálózat. Például egy munkaterületet, és a egy/24 virtuális hálózatot és/26-os alhálózatok rendelkezhet egy legfeljebb 64 csomópontot aktív egyszerre, míg a munkaterület egy /20 virtuális hálózat és /22 alhálózatok legfeljebb 1024 csomópontok is tartalmazhat.

  Az alhálózatok automatikusan létrejön, amikor konfigurálja a munkaterülethez, és konfigurálása során az alhálózatokat a CIDR-tartományt adja meg az állomástelepítéshez.

## <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása > Analytics > az Azure Databricks** az Azure Databricks szolgáltatás párbeszédpanel megnyitásához.

2. 2. lépésben ismertetett konfigurációs lépéseket követve: Az első lépések útmutató az Azure Databricks-munkaterület létrehozása és üzembe helyezése az Azure Databricks-munkaterületen válassza ki a virtuális hálózati beállítást.

   ![Az Azure Databricks szolgáltatás létrehozása](./media/vnet-injection/create-databricks-service.png)

3. Válassza ki a virtuális hálózat létrehozásához használni szeretne.

   ![Virtuális hálózati beállítások](./media/vnet-injection/select-vnet.png)

4. Adjon meg egy blokk közötti /18-/ 26-os két alhálózattal, dedikált Azure databricks a CIDR-tartományt:

   * Egy nyilvános alhálózatot egy társított hálózati biztonsági csoportot, amely lehetővé teszi a kommunikációt az Azure Databricks vezérlősík lesz létrehozva.
   * Egy társított hálózati biztonsági csoportot, amely lehetővé teszi, hogy a fürt belső kommunikációs egy privát alhálózatra lesz létrehozva.

5. Kattintson a **létrehozás** , a virtuális hálózaton üzembe helyezni az Azure Databricks-munkaterület.

## <a name="advanced-resource-manager-configurations"></a>Speciális resource manager-konfigurációk

Ha azt szeretné, hogy jobban szabályozhatja a virtuális hálózat beállításait – például azt szeretné, a meglévő alhálózatot használja, használja a meglévő hálózati biztonsági csoportok vagy hozzon létre egy saját biztonsági szabályok – a következő Azure Resource Manager-sablonok helyett használhatja a Portál virtuális hálózati konfiguráció és a munkaterület üzembe helyezése.

### <a name="all-in-one"></a>Minden együtt

Minden együtt egy virtuális hálózatot a hálózati biztonsági csoportok és az Azure Databricks-munkaterület létrehozásához használja a [teljes körű sablon Databricks VNet beszúrta munkaterületek](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Ez a sablon használatakor nem kell tennie minden olyan alhálózat forgalmának manuális alkalmazásátjáróra.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Hálózati biztonsági csoportokat hoz létre a meglévő virtuális hálózat szükséges szabályokat, használja a [hálózati biztonsági csoport sablon Databricks VNet injektálási](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Ez a sablon használatakor nem kell tennie minden olyan alhálózat forgalmának manuális alkalmazásátjáróra.

### <a name="virtual-network"></a>Virtuális hálózat

A megfelelő nyilvános és privát alhálózattal rendelkező virtuális hálózat létrehozásához használja a [virtuális hálózati sablont a Databricks VNet injektálási](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Ha ez a sablon a hálózati biztonsági csoportok sablon nélkül is használ, manuálisan kell hozzáadnia engedélyezési szabályok a hálózati biztonsági csoportok használata a virtuális hálózat.

### <a name="azure-databricks-workspace"></a>Az Azure Databricks-munkaterület

Egy meglévő virtuális hálózatot, amelynek a nyilvános és privát-alhálózatok és a megfelelően konfigurált hálózati biztonsági csoportok már beállított egy Azure Databricks-munkaterület központi telepítéséhez használja a [munkaterület sablon Databricks VNet injektálási](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Ha ez a sablon a hálózati biztonsági csoportok sablon nélkül is használ, manuálisan kell hozzáadnia engedélyezési szabályok a hálózati biztonsági csoportok használata a virtuális hálózat.

## <a name="whitelisting-subnet-traffic"></a>Engedélyezési alhálózat forgalmának

Ha nem használja a [az Azure portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) vagy [Azure Resource Manager-sablonok](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) szeretne létrehozni a hálózati biztonsági csoportok, manuálisan kell engedélyezett a következő forgalmat az alhálózatok.

|Irány|Protokoll|Forrás|Forrásport|Cél|Célport|
|---------|--------|------|-----------|-----------|----------------|
|Bejövő|\*|VirtualNetwork|\*|\*|\*|
|Bejövő|\*|Vezérlési sík NAT IP|\*|\*|22|
|Bejövő|\*|Vezérlési sík NAT IP|\*|\*|5557|
|Kimenő|\*|\*|\*|WebApp IP|\*|
|Kimenő|\*|\*|\*|SQL (szolgáltatáscímke)|\*|
|Kimenő|\*|\*|\*|Storage (szolgáltatáscímke)|\*|
|Kimenő|\*|\*|\*|VirtualNetwork|\*|

Engedélyezési lista alhálózat forgalmának használatával a következő IP-címek. SQL (metaadattár) és a Storage (összetevő és a naplók tárolásához) kell használnia az Sql és a Storage [szolgáltatáscímkéket](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Az Azure Databricks-régió|Szolgáltatás|Nyilvános IP-cím|
|-----------------------|-------|---------|
|USA keleti régiója|Vezérlősík NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA 2. keleti régiója|Vezérlősík NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA északi középső régiója|Vezérlősík NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA középső régiója|Vezérlősík NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA déli középső régiója|Vezérlősík NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA nyugati régiója|Vezérlősík NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA nyugati régiója, 2.|Vezérlősík NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Közép-Kanada|Vezérlősík NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kelet-Kanada|Vezérlősík NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Az Egyesült Királyság nyugati régiója|Vezérlősík NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Az Egyesült Királyság déli régiója|Vezérlősík NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Nyugat-Európa|Vezérlősík NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Észak-Európa|Vezérlősík NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Közép-India|Vezérlősík NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Dél-India|Vezérlősík NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Nyugat-India|Vezérlősík NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Délkelet-Ázsia|Vezérlősík NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Kelet-Ázsia|Vezérlősík NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Kelet-Ausztrália|Vezérlősík NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Délkelet-Ausztrália|Vezérlősík NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Ausztrália középső régiója|Vezérlősík NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Ausztrália 2. középső régiója|Vezérlősík NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Kelet-Japán|Vezérlősík NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Nyugat-Japán|Vezérlősík NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="workspace-launch-errors"></a>Munkaterület indítási hibák

Egy egyéni virtuális hálózatban munkaterület indítása meghiúsul, az Azure Databricks a bejelentkezési képernyőn a következő hiba miatt: **"Korábban hibát észleltünk a munkaterület létrehozását. Ellenőrizze, hogy az egyéni hálózati beállítások helyességét, és próbálkozzon újra."**

Ez a hiba oka a hálózati konfiguráció nem felel meg a követelményeknek. Győződjön meg arról, hogy elvégezte a jelen témakörben található utasításokat a munkaterület létrehozása során.

### <a name="cluster-creation-errors"></a>Fürt létrehozási hibák

**A példány nem érhető el: A rendszer nem SSH-n keresztül elérhető erőforrásokat.**

Lehetséges ok: feldolgozók vezérlősík forgalmát le van tiltva. Javítsa ki úgy, hogy a bejövő biztonsági szabályok követelményeinek. Ha telepíti, akkor a helyszíni hálózatához csatlakoztatott meglévő virtuális hálózattal, tekintse át a telepítés, a helyszíni hálózathoz való csatlakozás az Azure Databricks-munkaterület megadott információk segítségével.

**Váratlan indítása sikertelen: Váratlan hiba történt a fürt beállítása. Próbálkozzon újra, és ha a probléma tartósan fennáll, lépjen kapcsolatba az Azure Databricks. Belső hiba jelenik meg: Időtúllépés a csomópont elhelyezhet.**

Lehetséges ok: a feldolgozó Azure Storage végpontokra érkező forgalom le van tiltva. Javítsa ki úgy, hogy a kimenő biztonsági szabályok követelményeinek. Ha egyéni DNS-kiszolgálókat használ, emellett a virtuális hálózat DNS-kiszolgálók állapotának ellenőrzéséhez.

**Felhőbeli szolgáltató indítása sikertelen: A felhőbeli szolgáltató hiba történt a fürt beállítása. További információ az Azure Databricks útmutatójában talál. Az Azure hibakód: AuthorizationFailed/InvalidResourceReference.**

Lehetséges ok: a virtuális hálózat vagy alhálózatok már nem létezik. Győződjön meg arról, hogy a virtuális hálózat és alhálózat létezik.

**Fürt leállítása. Indoklás: A Spark a sikertelen indítás: A Spark nem tudta időben elindítani. A probléma okozhatja egy hibás Hive-metaadattár, érvénytelen Spark-konfigurációk vagy nem megfelelően működő init parancsfájlok. A Spark-illesztőprogram naplókat a probléma elhárításához tekintse meg, és ha a probléma tartósan fennáll, forduljon a Databricks. Belső hiba jelenik meg: A Spark nem tudott elindulni: Illesztőprogram-kezelő nem tudta időben elindítani.**

Lehetséges ok: Tároló nem tud kommunikálni az üzemeltető példány vagy DBFS-tárfiókot. Javítsa ki az alhálózatok a DBFS tárfiók Internet folyamatban a következő ugrás egy egyéni útvonalon hozzá.

### <a name="notebook-command-errors"></a>Notebook parancs hibák

**A parancs nem válaszol**

Lehetséges ok: worker-feldolgozó kommunikációja blokkolva van. Javítsa ki a azáltal, hogy a bejövő biztonsági szabályok az igényeknek.

**Notebook munkafolyamat azért sikertelen, a kivétel miatt: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Lehetséges ok: az Azure Databricks-Webapp feldolgozók érkező forgalom le van tiltva. Javítsa ki a azáltal, hogy a kimenő biztonsági szabályok az igényeknek.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)
