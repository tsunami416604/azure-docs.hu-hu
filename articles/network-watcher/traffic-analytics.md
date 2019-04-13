---
title: Az Azure traffic analytics |} A Microsoft Docs
description: Ismerje meg, hogyan elemezheti az Azure-beli hálózati biztonsági csoport folyamatnaplóinak traffic Analytics.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: yagup;jdial
ms.openlocfilehash: cb61366a672a48cdc84e14f40d889e646e0e23b8
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545399"
---
# <a name="traffic-analytics"></a>Forgalmi elemzések

A TRAFFIC Analytics egy felhőalapú megoldás, amely a felhőbeli hálózatok felhasználói és alkalmazástevékenységekbe rálátást biztosít a rendszer. A TRAFFIC analytics elemzi a Network Watcher hálózati biztonsági csoport (NSG) folyamatnaplóit, amelyek az Azure-felhőben adatforgalmat betekintést nyújtanak. A traffic analytics segítségével:

- Hálózati tevékenység vizualizációja az Azure-előfizetések között, és azonosíthatja a hotspotok.
- Biztonsági kockázatok azonosítása, és tegye biztonságossá hálózatát, például a nyitott portok, internet-hozzáférés és a virtual machines (VM) hálózatok támadó csatlakozik megkísérlő alkalmazások adatokkal.
- Ismerje meg, flow forgalmat az Azure-régiók és az internethez, hogy a saját hálózati telepítéséhez a teljesítmény és a kapacitás optimalizálása.
- Azonosíthatja a hálózati konfigurációs hibák és sikertelen kapcsolatokat a hálózaton.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Miért érdemes a traffic analytics?

Elengedhetetlen figyelése, kezelése és a saját hálózati tanúsítványlánchoz biztonsági, megfelelőségi és teljesítményt tudja. Döntő védelemmel való ellátásához és optimalizálhatja azt, hogy a saját környezetben van. Gyakran ismernie kell a hálózathoz, aki csatlakozik, aktuális állapotát, ahol azok kapcsolódik, azoknak a portoknak meg nyitva, az internethez, várt hálózati probléma, rendszertelen hálózati probléma, és az adatforgalom hirtelen nő.

Felhőalapú hálózatokhoz eltérnek a helyi vállalati hálózatokkal, netflow vagy azzal egyenértékű protokollt támogató útválasztók és kapcsolók, adja meg annak az IP-hálózati forgalom gyűjtése, lép vagy kilép egy hálózati adapter esetében. Forgalom folyamat adatok elemzésével hozhat létre hálózati forgalmat, és mennyiségi elemzése.

Azure virtuális hálózatokkal rendelkezik NSG-Folyamatnaplók, bejövő információkat nyújtanak, és a kimenő IP-forgalom – a hálózati biztonsági csoport társított egyes hálózati adapterek, virtuális gépek és alhálózatok. Nyers folyamatnaplóinak elemzésével és Beszúrás, intelligencia, biztonság, a topológiát és a földrajzi hely, forgalom analytics is tartalmaznak információkat, az adatforgalmat a környezetben. A TRAFFIC Analytics leggyakrabban kommunikáló állomások, leggyakrabban kommunikáló alkalmazásprotokollok, leginkább a beszélgetésben részt vevő gazdagép párok, engedélyezett/letiltott forgalom, bejövő/kimenő forgalom, a portok megnyitása az internet, leginkább blokkoló szabályokat, forgalom vonatkozó információkat tartalmaz Azure-adatközpontban, virtuális hálózat, alhálózat, megoszlása vagy, a támadó a hálózatokat.

## <a name="key-components"></a>A legfontosabb összetevők

- **Hálózati biztonsági csoport (NSG)**: A biztonsági szabályok, amelyek engedélyezik vagy megtagadják a hálózati forgalmat egy Azure virtuális hálózathoz csatlakozó erőforrások listáját tartalmazza. Az NSG-k társíthatóak alhálózatokhoz, egyedi virtuális gépekhez (klasszikus) vagy virtuális gépekhez (Resource Manager) kapcsolt hálózati adapterekhez (NIC). További információkért lásd: [hálózati biztonsági csoportok áttekintése](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Hálózati biztonsági csoport (NSG) folyamatnaplóit**: Lehetővé teszi keresztül a hálózati biztonsági csoport bejövő és kimenő IP-forgalom kapcsolatos információk megtekintéséhez. NSG-folyamat naplók json formátumban íródtak, és a hálózati Adaptert a folyamat vonatkozik egy szabály alapon, bejövő és kimenő folyamatok megjelenítése, 5-ször több információt a folyamat (forrás és a cél IP-cím forrás és a cél-port és protokoll), és ha a forgalmat a rendszer engedélyezte vagy az elutasított. NSG-Folyamatnaplók kapcsolatos további információkért lásd: [NSG-Folyamatnaplók](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Azure-szolgáltatás figyelési adatait gyűjti és tárolja az adatokat egy központi tárházban. Ezek az adatok lehetnek események, teljesítményadatok vagy az Azure API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. Alkalmazások figyelése, mint például a hálózati teljesítmény figyelése és a traffic analytics felhasználásával történik az Azure Monitor naplóira alapjaként. További információkért lásd: [naplózza az Azure Monitor](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics-munkaterület**: Az Azure Monitor-naplók, Azure-fiókra vonatkozó adatok tárolására egy példányát. További információ a Log Analytics-munkaterületek: [hozzon létre egy Log Analytics-munkaterület](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: Egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet az Azure-beli hálózati forgatókönyvek szintjén. NSG-Folyamatnaplók kapcsolja ki és a Network Watcher kapcsolhatja be. További információkért lásd: [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Forgalmi elemzések működése

A TRAFFIC analytics megvizsgálja a nyers NSG-Folyamatnaplók és a csökkentett naplók rögzíti többek között a azonos forrás IP-címe, cél IP-cím, céloldali port és protokoll közös folyamatok összesítésével. For example, Host 1 (IP address: 10.10.10.10) kommunikál a gazdagépen 2 (IP-cím: 10.10.20.10), 100-szor 1 óra (például 80-as) és protokollt (például http) használatával egy adott időszakban. A csökkentett napló rendelkezik, amelyek 100-szor egy adott időszakban 1 óra port használatával kommunikálni gazdagép 1. és 2 gazdagép egy bejegyzést *80-as* és protokoll *HTTP*, 100 bejegyzések nem. Csökkentett naplók javult a földrajzi hely, a biztonság és a topológiára vonatkozó információkkal, és a Log Analytics-munkaterületen tárolja. Az alábbi képen látható, az adatfolyam:

![NSG-Folyamatnaplók feldolgozása folyamatábrája](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Támogatott régiók

A traffic analytics használhat NSG-k, a következő támogatott régiók egyikében:

* Közép-Kanada
* USA nyugati középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA északi középső régiója
* USA déli középső régiója
* USA középső régiója
* USA nyugati régiója
* USA nyugati régiója, 2.
* Közép-Franciaország
* Nyugat-Európa
* Észak-Európa
* Dél-Brazília
* Az Egyesült Királyság nyugati régiója
* Az Egyesült Királyság déli régiója
* Kelet-Ausztrália
* Délkelet-Ausztrália
* Kelet-Ázsia
* Délkelet-Ázsia
* Korea középső régiója
* Közép-India
* Dél-India
* Kelet-Japán 
* Nyugat-Japán
* USA-beli államigazgatás – Virginia

A Log Analytics-munkaterület léteznie kell a következő régióban:
* Közép-Kanada
* USA nyugati középső régiója
* USA nyugati régiója, 2.
* USA keleti régiója
* Közép-Franciaország
* Nyugat-Európa
* Az Egyesült Királyság déli régiója
* Délkelet-Ausztrália
* Délkelet-Ázsia
* Korea középső régiója
* Közép-India
* Kelet-Japán
* USA-beli államigazgatás – Virginia

## <a name="prerequisites"></a>Előfeltételek

### <a name="user-access-requirements"></a>Felhasználói hozzáférési követelmények

A fióknak kell lennie, a következő Azure egyik tagjának [beépített szerepkörök](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Üzemi modell   | Szerepkör                   |
|---------          |---------               |
|Resource Manager   | Tulajdonos                  |
|                   | Közreműködő            |
|                   | Olvasó                 |
|                   | Hálózati közreműködő    |

Ha a fiók nincs hozzárendelve a beépített szerepkörök egyike, akkor hozzá kell rendelni egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) rendelt, az előfizetés szintjén a következő műveleteket:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

Felhasználói hozzáférési engedélyek ellenőrzésére vonatkozó információkért lásd: [Traffic analytics – gyakori kérdések](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Elemezze az adatforgalmat, hogy szüksége lesz egy meglévő a network watcher vagy [egy network watcher engedélyezése](network-watcher-create.md) minden régióban, amely rendelkezik NSG-ket, hogy az elemezni kívánt forgalmi a. A TRAFFIC analytics valamelyikében lévő üzemeltetett NSG-k esetén is engedélyezhető a [támogatott régiók](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Regisztrálja újra a hálózati erőforrás-szolgáltató

A traffic analytics használata előtt újra kell regisztrálni a hálózati erőforrás-szolgáltató. Kattintson a **Kipróbálom** a következő kód mezőbe az Azure Cloud Shell megnyitásához. A Cloud Shell automatikusan naplózza, hogy az Azure-előfizetéshez. A Cloud Shell megnyitása után adja meg az újbóli regisztrációt a hálózati erőforrás-szolgáltató a következő parancsot:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Válassza ki a hálózati biztonsági csoport

Mielőtt engedélyezné az NSG-folyamat, naplózás, rendelkeznie kell egy hálózati biztonsági csoportot a folyamatok bejelentkezni. Ha egy hálózati biztonsági csoport nem rendelkezik, tekintse meg [hozzon létre egy hálózati biztonsági csoport](../virtual-network/manage-network-security-group.md#create-a-network-security-group) hozhat létre egyet.

Az Azure portal bal oldalán válassza ki a **figyelő**, majd **a Network watcher**, majd válassza ki **NSG-Folyamatnaplók**. Válassza ki a hálózati biztonsági csoportot, hogy engedélyezni szeretné egy NSG-folyamat napló, az alábbi ábrán látható módon:

![NSG-k engedélyezése NSG-folyamat napló igénylő kiválasztása](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Ha a traffic analytics engedélyezése NSG-t, amely más, a bármely régióban üzemeltet a [támogatott régiók](#supported-regions), "Nem található" hibaüzenetet kap.

## <a name="enable-flow-log-settings"></a>Flow-log beállítások engedélyezése

Mielőtt engedélyezné a flow naplózási beállításai, a következő feladatokat kell elvégeznie:

Az Azure Insights-szolgáltató regisztrálása a, ha még nincs regisztrálva van az előfizetéséhez:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Ha még nem rendelkezik Azure Storage-fiókot, az NSG-folyamat tárolására bejelentkezik, létre kell hoznia egy tárfiókot. A következő paranccsal is hozzon létre egy tárfiókot. A parancs futtatása előtt cserélje le a `<replace-with-your-unique-storage-account-name>` , egyedi el az összes Azure-helyen 3 – 24 karakter közötti hosszúságú, melynek neve használatával csak számokból és kisbetűkből állhat. Az erőforráscsoport neve, szükség esetén is módosíthatja.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Válassza ki a következő beállításokat, amint a képen látható:

1. Válassza ki *a* a **állapota**
2. Válassza ki *2-es verzió* a **Flow naplók verzió**. 2-es verzió tartalmazza a flow-munkamenet statisztika (bájtok és csomagok)
3. Válasszon ki egy meglévő tárfiókot a folyamat-naplók tárolására. Ha tartja az adatokat tárolni szeretné, állítsa az értékét *0*. Azure Storage-díjak a tárfiók díjak.
4. Állítsa be **megőrzési** adatait tárolni kívánt napok száma.
5. Válassza ki *a* a **Traffic Analytics állapota**.
6. Válasszon ki egy meglévő Log Analytics (OMS) munkaterületet, vagy válasszon **új munkaterület létrehozása** hozzon létre egy újat. Log Analytics-munkaterületet a Traffic Analytics használják majd létrehozására szolgál az analytics összesített és indexelt adatok tárolására. Ha egy meglévő munkaterületet, kell-e találhatók, amelyek a [támogatott régiók](#supported-regions) és frissítettek az új lekérdezési nyelvre. Ha nem szeretne frissíteni egy meglévő munkaterületet, vagy nem rendelkezik egy munkaterületet egy támogatott régióban, hozzon létre egy újat. Lekérdezési nyelvet kapcsolatos további információkért lásd: [Azure Log Analytics új naplókeresésre frissítési](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    A log analytics-munkaterületet a traffic analytics megoldás és az NSG-t üzemeltető nem kell ugyanabban a régióban. Például előfordulhat, a traffic analytics-munkaterületen a Nyugat-európai régióban bár előfordulhat, hogy az NSG-ket az USA keleti RÉGIÓJA és USA nyugati RÉGIÓJA. Több NSG-k ugyanazon a munkaterületen konfigurálhatók.
7. Kattintson a **Mentés** gombra.

    ![Kijelölés tárfiókba, Log Analytics-munkaterület és a Traffic Analytics engedélyezése](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement-nsg-flowlogs-v2.png)

Ismételje meg az előző lépéseket bármely más NSG-k, amelyhez hozzá szeretné a traffic analytics engedélyezéséhez. A folyamat-naplók továbbítja a munkaterület, ezért győződjön meg arról, hogy a helyi jogszabályoknak és előírásoknak, az Ön országában lehetővé teszik az adattárolás a régióban, ahol a munkaterület létezik.

Traffic analytics segítségével is konfigurálhatja a [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) az Azure PowerShell PowerShell-parancsmagot. Futtatás `Get-Module -ListAvailable Az` a telepített verzió azonosításához. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="view-traffic-analytics"></a>A traffic analytics megtekintése

Válassza a bal oldali a portál, **minden szolgáltatás**, majd adja meg *figyelő* a a **szűrő** mezőbe. Amikor **figyelő** megjelenik a keresési eredmények között, válassza ki. A traffic analytics és a készülék képességeinek felfedezése indításához válassza **a Network watcher**, majd **Traffic Analytics**.

![A Traffic Analytics irányítópult elérése](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Az irányítópult jelenik meg először a Traffic Analytics kell először adatokat tudnak összesíteni elegendő hozzájut jelentéssel bíró, mielőtt azt minden olyan jelentéseket hozhat létre, mert legfeljebb 30 percet is igénybe vehet.

## <a name="usage-scenarios"></a>Felhasználási területek

Az insights után a Traffic Analytics teljes körűen konfigurálva van, így érdemes néhány következő:

### <a name="find-traffic-hotspots"></a>Legaktívabb keresése

**keresni**

- Mely állomások, alhálózatok és virtuális hálózatok vannak küldésére vagy a legtöbb forgalmat fogadó, áthaladó legnagyobb rosszindulatú forgalmat és blokkolja a jelentős folyamatok?
    - Ellenőrizze a gazdagép, alhálózat és virtuális hálózati összehasonlító diagram. Tudnivalók a gazdagépek, alhálózatok és virtuális hálózatok küldi, vagy a legtöbb forgalmat fogadó segítségével azonosíthatja a gazdagépeket, amelyek a legtöbb forgalmat olyan feldolgozási és az adatforgalom eloszlása e a megfelelően történik.
    - A forgalom mennyisége megfelelő gazdagép-e ki. A kötet forgalmat a normál működés, vagy nem azt érdemelnek további vizsgálat?
- Mennyi bejövő/kimenő forgalom van?
    -   A gazdagép várt kapnak több bejövő forgalom, kimenő, vagy fordítva?
- A letiltott forgalom statisztikája.
    - Miért nem blokkolja a gazdagép jóindulatú forgalom jelentős mennyiségű? Ez a viselkedés igényel további vizsgálat, és valószínűleg optimalizálási konfiguráció
- Az engedélyezett/letiltott rosszindulatú forgalom statisztika
  - Miért érdemes egy gazdagép fogad rosszindulatú forgalom van, és miért rosszindulatú forrásból folyamatok? Ez a viselkedés a vizsgálat, és valószínűleg optimalizálási konfiguráció további szükséges.

    Válassza ki **összes**alatt **gazdagép**, ahogy az alábbi képen is látható:

    ![Irányítópult, amely a gazdagép a legtöbb forgalom részletei](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Az alábbi képen látható, hogy egy gazdagép a leggyakoribb öt vendégem gazdagépek és a flow kapcsolatos részleteket (engedélyezett – bejövő/kimenő és letiltott – bejövő/kimenő forgalom) trendek idő:

    ![Az öt legaktívabb legtöbb legtöbbet beszélgető gazdagép trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**keresni**

- Melyek a leginkább a beszélgetésben részt vevő gazdagép párok?
    - Előtér- és háttér-kommunikáció vagy szabálytalan viselkedés, például a háttérbeli internetes forgalom várt viselkedést.
- Az engedélyezett/letiltott forgalom statisztika
    - Miért egy gazdagép engedélyezése vagy letiltása jelentős adatforgalma
- A leggyakrabban használt protokoll a legtöbb a beszélgetésben részt vevő gazdagép párok között:
    - Ezek az alkalmazások engedélyezettek a hálózaton?
    - Megfelelően van beállítva az alkalmazásokat? Akkor használja a megfelelő protokoll-kommunikációhoz? Válassza ki **összes** alatt **gyakran használják a beszélgetés**, az alábbi képen látható:

        ![Irányítópult, amely a leggyakoribb beszélgetés](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Az alábbi képen látható az idő az első öt beszélgetés trendek és a flow kapcsolatos részleteket például engedélyezett és letiltott a bejövő és kimenő folyamatok egy beszélgetés párhoz:

    ![TOP 5 leghosszabb beszélgetés részletei és -trendek](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**keresni**

- Melyik protokoll legtöbb alkalmaznak az adott környezetben, és mely beszélgetésben részt vevő gazdagép párokat használ az alkalmazásprotokoll a legtöbb?
    - Ezek az alkalmazások engedélyezettek a hálózaton?
    - Megfelelően van beállítva az alkalmazásokat? Akkor használja a megfelelő protokoll-kommunikációhoz? Várt működése közös portokon, például a 80-as és 443-as porton. A standard szintű kommunikációhoz szokatlan portokat jelennek meg, ha egy konfigurációmódosítás szükséges. Válassza ki **összes** alatt **alkalmazásport**, a következő képen látható:

        ![Irányítópult, amely a leggyakrabban használt alkalmazásprotokollok](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Az alábbi képek idő megjelenítése egy 7. rétegbeli protokoll az öt legaktívabb 7. rétegbeli protokollok és a flow kapcsolatos információt (például engedélyezett és letiltott a folyamatok) népszerű:

    ![Leggyakoribb öt 7 protokollok részletei és trendek. réteg](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Folyamat részletei a naplókeresésben protokoll](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**keresni**

- Kapacitás kihasználtsága trendjeit a VPN-átjáró, a környezetben.
    - Minden egyes VPN Termékváltozatra lehetővé teszi a sávszélesség egy bizonyos mennyiségű. A VPN-átjárók kihasználva?
    - Az átjárók, eléri a kapacitása? Frissítsen a következő magasabb szintű termékváltozatra?
- Melyek a leginkább beszélgetésben résztvevő gazdagépek, amelyek VPN-átjárót, melyik porton keresztül?
    - Ez a minta normál van? Válassza ki **összes** alatt **VPN-átjáró**, ahogy az alábbi képen is látható:

        ![Irányítópult, amely a legaktívabb VPN-kapcsolatok](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Az alábbi képen látható, ideje a kapacitás kihasználtságát az Azure VPN Gateway és a flow kapcsolatos részleteket (például engedélyezett folyamatok és portok) népszerű:

    ![VPN gateway kihasználtsági trendek és a flow részletei](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Földrajzi hely szerint forgalomelosztás megjelenítése

**keresni**

- Az adatforgalom eloszlása a adatközpontban és a felső alkalmazásprotokollok szolgáltatásprogram szolgáltatásprogram felső engedélyezetlen hálózatok forgalomnak egy adatközpontban, a leggyakoribb források például adatközpontonként.
  - Megfigyelte az adatközpontban további terhelése, ha a forgalom hatékony elosztása is megtervezheti.
  - Ha rosszindulatú hálózatokat az adatközpontban vannak szolgáltatásprogram, majd javítsa ki a letiltja az NSG-szabályok.

    Válassza ki **térkép megtekintése** alatt **a környezet**, ahogy az alábbi képen is látható:

    ![Irányítópult listanézetben láthatók az adatforgalom eloszlása](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- A földrajzi térképen jeleníti meg a felső szalagon paraméterek kiválasztásának például az adatközpontokban (telepített/nem – üzembe helyezés/Active/Inactive/Traffic Analytics engedélyezve/nincs engedélyezve a Traffic Analytics) és az aktív Benign/rosszindulatú adatforgalmat lebonyolított országok telepítés:

    ![Georedundáns térképnézet, amely azt mutatja be az aktív központi telepítés](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- A földrajzi térképen jeleníti meg, más országokból és kontinensen kommunikál a sorok színes a kék (jóindulatú forgalom) és vörös (rosszindulatú forgalom), az adatforgalom eloszlása egy adatközpontba:

    ![Amely azt mutatja be az adatforgalom eloszlása országban és kontinensen földrajzi nézet](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Folyamat részletei az adatforgalom eloszlása a naplókeresésben](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Virtuális hálózatok forgalomelosztás megjelenítése

**keresni**

- Forgalom megoszlása / virtuális hálózat, a Kiszolgálótopológia, a leggyakoribb források forgalmat a virtuális hálózathoz, a virtuális hálózathoz, a szolgáltatásprogram alkalmazásprotokollok felső szolgáltatásprogram felső engedélyezetlen hálózatok.
  - Azt a virtuális hálózatot, hogy melyik virtuális hálózathoz szolgáltatásprogram. Ha nem várt a beszélgetést, javítható.
  - Rosszindulatú hálózatok vannak szolgáltatásprogram egy virtuális hálózathoz, ha az NSG-szabályok blokkolja a rosszindulatú hálózatok javíthatja.
 
    Válassza ki **nézet virtuális hálózatok** alatt **a környezet**, ahogy az alábbi képen is látható:

    ![Irányítópult, amely a virtuális hálózatok eloszlása](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A virtuális hálózati topológia látható a felső szalagon kiválasztása hasonlóan a virtuális hálózat (belső virtuális hálózati kapcsolatok/Active/Inactive) paraméterek, a külső kapcsolatokat, aktív forgalommal és rosszindulatú forgalmat a virtuális hálózat.
- A virtuális hálózati topológiát, előfizetések, munkaterületek, erőforráscsoportok és időintervallum alapján szűrhetők. További szűrőket, amelyek segítenek megérteni, a folyamat vannak: Flow típusa (virtuális hálózatok közötti IntraVNET és így tovább), a folyamat iránya (a bejövő, kimenő), a folyamat állapota (engedélyezett, a blokkolt), virtuális hálózatok (a megcélzott és a csatlakoztatott), kapcsolattípus (társviszony-létesítés vagy átjáró - P2S és S2S), és NSG-t. Ezek a szűrők használatával részletesen vizsgálni kívánt virtuális hálózatok összpontosíthat.
- A virtuális hálózati topológia például egy virtuális hálózathoz (engedélyezett/letiltott/bejövő/kimenő/Benign/kártékony) folyamatokat, protokoll és hálózati biztonsági csoportok, érdemes forgalom elosztását mutatja:

    ![Virtuális hálózati topológiától, amely a forgalom terjesztési és a folyamat részletei](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Virtuális hálózati topológiától, amely a legfelső szintű és további szűrők](./media/traffic-analytics/virtual-network-filters.png)

    ![Folyamat részletei a virtuális hálózati adatforgalom eloszlása a naplókeresésben](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**keresni**

- Forgalom megoszlása / alhálózat, a topológia, a leggyakoribb források forgalmat az alhálózathoz, az alhálózathoz, és a szolgáltatásprogram alkalmazásprotokollok felső szolgáltatásprogram felső engedélyezetlen hálózatok.
    - Hogy mely alhálózat mely alhálózathoz szolgáltatásprogram. Ha váratlan beszélgetések, javíthatja a konfigurációt.
    - Ha a támadó hálózatok vannak szolgáltatásprogram egy alhálózathoz, is tudja javítsa ki az NSG-szabályok blokkolja a rosszindulatú hálózatok konfigurálásával.
- Az alhálózatok topológia látható a felső szalagon paraméterek Active/Inactive alhálózat, a külső kapcsolatokat, aktív forgalommal és az alhálózat rosszindulatú folyamatok például a kiválasztásához.
- Az alhálózat topológiája például egy virtuális hálózathoz tartományállapot folyamatokat (engedélyezett/letiltott/bejövő/kimenő/Benign/kártékony), a protokoll és az NSG-k, forgalom elosztását mutatja:

    ![Alhálózat topológiája, amely az adatforgalom eloszlása a virtuális hálózat alhálózatának tartományállapot folyamatok](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**keresni**

Az Application gateway és a Load Balancer, a topológia forgalom megoszlása, forgalom, felső leggyakoribb források támadó az Application gateway & Load Balancer és felső alkalmazásprotokollok szolgáltatásprogram szolgáltatásprogram hálózatok. 
    
 - Hogy mely alhálózat mely az Application gateway és a Load Balancer szolgáltatásprogram. Ha megfigyelte váratlan beszélgetések, kijavíthatja a konfigurációt.
 - Ha a támadó hálózatok vannak szolgáltatásprogram egy Application gateway, vagy a Load Balancer, is tudja javítsa ki az NSG-szabályok blokkolja a rosszindulatú hálózatok konfigurálásával. 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Portok és az internetről forgalmat fogadó virtuális gépek megtekintése

**keresni**

- Nyitott portok vannak szolgáltatásprogram az interneten keresztül?
  - Ha váratlan portok nyitva talál, javíthatja a konfigurációt:

    ![Irányítópult, amely portok fogadását, és az internetre forgalmat továbbító](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Az Azure-beli célportok és az állomások részletei](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**keresni**

Rendelkezik a rosszindulatú adatforgalom a környezetében? Hol van, a származó? Hol van, az felé irányuló?

![Részletes naplók keresése a rosszindulatú forgalom](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>A trendekkel NSG-t vagy NSG-szabályok találatok megjelenítése

**keresni**

- Melyik NSG-t vagy NSG-szabályok a legtöbb megkeresése a folyamatok terjesztési összehasonlító diagram van?
- Mik a leggyakoribb forrás és cél beszélgetéspárok egy NSG/NSG-szabályok?

    ![Irányítópult, amely azt mutatja be az NSG-t eléri statisztika](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Az alábbi képek idő megjelenítése a találatok NSG-szabályok és a forrás-cél foly. Részl. egy hálózati biztonsági csoportra vonatkozó trendelemzés:

  - Gyors észlelése, mely az NSG-k és NSG-t szabályok vannak áthaladó rosszindulatú folyamatok, és melyek a leggyakoribb rosszindulatú IP-címek a felhőbeli környezethez hozzáférő
  - Azonosítsa, mely NSG-t vagy NSG-szabályok vannak így/jelentős hálózati forgalom blokkolása
  - Válassza a felső szűri az NSG-t vagy NSG-t a részletes ellenőrzés szabályok

    ![NSG-szabályok találatai és a leggyakoribb NSG-szabályok népszerű listanézetben láthatók idő](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Leggyakoribb NSG-szabályok statisztika részletei a naplókeresésben](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Válaszok a gyakori kérdésekre, lásd: [Traffic analytics – gyakori kérdések](traffic-analytics-faq.md).

## <a name="next-steps"></a>További lépések

- A folyamat-naplók engedélyezésére, lásd: [engedélyezése NSG csoportforgalom naplózása](network-watcher-nsg-flow-logging-portal.md).
- A séma- és a Traffic Analytics-adatok feldolgozása ismertetése: [Traffic analytics séma](traffic-analytics-schema.md).
