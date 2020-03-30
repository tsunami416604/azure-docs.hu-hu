---
title: Azure forgalomelemzés | Microsoft dokumentumok
description: Ismerje meg, hogyan elemezheti az Azure hálózati biztonsági csoport folyamatnaplóit a forgalomelemzéssel.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: 83164a615cacc067e5f1ea6a1dd6ce0f0fd9d540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298849"
---
# <a name="traffic-analytics"></a>Forgalmi elemzések

A Traffic Analytics egy felhőalapú megoldás, amely betekintést nyújt a felhőhálózatok felhasználói és alkalmazási tevékenységébe. A Traffic Analytics elemzi a Network Watcher hálózati biztonsági csoport (NSG) folyamatnaplóit, hogy betekintést nyújtson az Azure-felhőben a forgalom áramlásába. A forgalomelemzéssel a következőkre tehet ki:

- Vizualizálja a hálózati tevékenységet az Azure-előfizetései között, és azonosítsa a forró pontokat.
- Azonosítsa a hálózatot fenyegető biztonsági fenyegetéseket, és biztosítsa a hálózatot olyan információkkal, mint a nyílt portok, az internet-hozzáférést megkísérelő alkalmazások és a tisztességtelen hálózatokhoz csatlakozó virtuális gépek.Identify security threats, and secure your network, with information such as open-ports, applications attempting internet access, and virtual machines (VM) connect ing to rogue networks.
- Ismerje meg az Azure-régiók és az internet közötti forgalomáramlási mintákat, hogy optimalizálja a hálózati telepítést a teljesítmény és a kapacitás érdekében.
- A hálózat helytelen konfigurációinak pontos anameddig, ami a hálózat sikertelen kapcsolataihoz vezet.

> [!NOTE]
> A Traffic Analytics mostantól támogatja az NSG flow naplók adatainak nagyobb gyakorisággal, 10 mins-ben történő gyűjtését

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Miért a forgalomelemzés?

A kompromisszumok nélküli biztonság, megfelelőség és teljesítmény érdekében elengedhetetlen a saját hálózatának figyelése, kezelése és megismerése. Ismerve a saját környezet rendkívül fontos, hogy megvédje és optimalizálja azt. Gyakran ismernie kell a hálózat aktuális állapotát, a csatlakozást, a csatlakozást, az internetre nyitva álló portokat, a várható hálózati viselkedést, a szabálytalan hálózati viselkedést és a forgalom hirtelen emelkedését.

A felhőhálózatok eltérnek a helyszíni vállalati hálózatoktól, ahol netflow vagy azzal egyenértékű protokollképes útválasztók és kapcsolók vannak, amelyek lehetővé teszik az IP-hálózati forgalom gyűjtését, amikor belép vagy kilép a hálózati adapterből. A forgalomáramlási adatok elemzésével elemezheti a hálózati forgalom áramlását és mennyiségét.

Az Azure virtuális hálózatok NSG-folyamatnaplókkal rendelkeznek, amelyek az egyes hálózati adapterekhez, virtuális gépekhez vagy alhálózatokhoz társított hálózati biztonsági csoporton keresztül biztosítinformációt a be- és kimenő IP-forgalomról. A nyers NSG-folyamatnaplók elemzésével, valamint a biztonság, a topológia és a földrajzi ismeretek intelligenciájának beszúrásával a forgalomelemzés betekintést nyújthat a környezet forgalomáramlásába. A Traffic Analytics olyan információkat nyújt, mint a legtöbb kommunikáló állomás, a legtöbb kommunikáló alkalmazásprotokoll, a legtöbb beszélgető állomáspár, az engedélyezett/blokkolt forgalom, a bejövő/kimenő forgalom, a nyitott internetes portok, a legtöbb blokkoló szabály, az Azure-adatközpontonkénti forgalomelosztás, a virtuális hálózat, az alhálózatok vagy az engedélyezetlen hálózatok.

## <a name="key-components"></a>A legfontosabb összetevők

- **Hálózati biztonsági csoport (NSG)**: Az Azure virtuális hálózathoz csatlakoztatott erőforrások hálózati forgalmát engedélyező vagy megtagadó biztonsági szabályok listáját tartalmazza. Az NSG-k társíthatóak alhálózatokhoz, egyedi virtuális gépekhez (klasszikus) vagy virtuális gépekhez (Resource Manager) kapcsolt hálózati adapterekhez (NIC). További információt a [Hálózati biztonsági csoport – áttekintés című témakörben talál.](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- **Hálózati biztonsági csoport (NSG) folyamatnaplói:** Lehetővé teszi a hálózati biztonsági csoporton keresztül a be- és kimenő IP-forgalommal kapcsolatos információk megtekintését. Az NSG-folyamatnaplók json formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamatáltal alkalmazott hálózati adaptert, a folyamatöthangú információkat (forrás/cél IP-cím, forrás/cél port és protokoll), valamint azt, hogy a forgalmat engedélyezték vagy megtagadták-e. Az NSG-folyamatnaplókról további információt az [NSG-folyamatnaplók című témakörben talál.](network-watcher-nsg-flow-logging-overview.md)
- **Log Analytics:** Egy Azure-szolgáltatás, amely adatokfigyelési adatokat gyűjt, és tárolja az adatokat egy központi tárházban. Ezek az adatok eseményeket, teljesítményadatokat vagy az Azure API-n keresztül biztosított egyéni adatokat tartalmazhatnak. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. Az olyan alkalmazások figyelése, mint a hálózati teljesítményfigyelő és a forgalomelemzés az Azure Monitor naplói alapján épülnek fel. További információ: [Azure Monitor logs](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics-munkaterület:** Az Azure Monitor-naplók egy példánya, ahol az Azure-fiókra vonatkozó adatok tárolódnak. A Log Analytics-munkaterületekről további információt a [Log Analytics-munkaterület létrehozása című témakörben talál.](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- **Network Watcher**: Olyan regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása hálózati forgatókönyv szintjén az Azure-ban. Az NSG-folyamatnaplókat a Network Watcher segítségével kapcsolhatja be és ki. További információ: [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>A forgalomelemzés működése

A traffic analytics megvizsgálja a nyers NSG-folyamatnaplókat, és rögzíti a csökkentett naplókat az azonos forrás IP-cím, a cél IP-cím, a cél-port és a protokoll közötti közös folyamatok összesítésével. Például az 1. A csökkentett napló nak egy bejegyzése van, hogy a Host 1 & Host 2 1óra alatt 100 alkalommal kommunikált a *80-as* port és a *HTTP*protokoll használatával, ahelyett, hogy 100 bejegyzéssel rendelkezne. A csökkentett naplók földrajzi, biztonsági és topológiai információkkal vannak kijavítva, majd egy Log Analytics-munkaterületen tárolódnak. Az alábbi képen az adatfolyam látható:

![Adatfolyam az NSG-folyamatnaplók feldolgozásához](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Támogatott régiók: NSG 

Az NSG-k forgalomelemzését az alábbi támogatott régiók bármelyikében használhatja:

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
* Dél-Korea középső régiója
* Közép-India
* Dél-India
* Kelet-Japán 
* Nyugat-Japán
* USA-beli államigazgatás – Virginia
* Kína Keleti 2

## <a name="supported-regions-log-analytics-workspaces"></a>Támogatott régiók: Log Analytics-munkaterületek

A Log Analytics-munkaterületnek a következő régiókban kell léteznie:
* Közép-Kanada
* USA nyugati középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA északi középső régiója
* USA déli középső régiója
* USA középső régiója
* USA nyugati régiója
* USA nyugati régiója, 2.
* USA középső régiója
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
* Dél-Korea középső régiója
* Közép-India
* Kelet-Japán
* USA-beli államigazgatás – Virginia
* Kína Keleti 2

## <a name="prerequisites"></a>Előfeltételek

### <a name="user-access-requirements"></a>Felhasználói hozzáférési követelmények

A fióknak az alábbi [Azure-szerepkörök](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)egyikének kell lennie:

|Üzemi modell   | Szerepkör                   |
|---------          |---------               |
|Resource Manager   | Tulajdonos                  |
|                   | Közreműködő            |
|                   | Olvasó                 |
|                   | Hálózati közreműködő    |

Ha a fiók nincs hozzárendelve a beépített szerepkörök egyikéhez, akkor az előfizetés szintjén a következő műveletekhez rendelt [egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) kell hozzárendelni:

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
- "Microsoft.Network/expressRouteCircuits/read"

A felhasználói hozzáférési engedélyek ellenőrzéséről a Forgalomelemzés – [gyakori kérdések című témakörben talál további információt.](traffic-analytics-faq.md)

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

A forgalom elemzéséhez rendelkeznie kell egy meglévő hálózati figyelővel, vagy engedélyeznie kell [egy hálózati figyelőt](network-watcher-create.md) minden olyan régióban, ahol nsg-k vannak, amelyek esetében elemezni szeretné a forgalmat. A forgalomelemzés engedélyezhető a támogatott régiók bármelyikében üzemeltetett [NSG-k esetében.](#supported-regions-nsg)

### <a name="select-a-network-security-group"></a>Hálózati biztonsági csoport kijelölése

Az NSG-folyamatnaplózás engedélyezése előtt rendelkeznie kell egy hálózati biztonsági csoporttal a folyamatok naplózásához. Ha nem rendelkezik hálózati biztonsági csoporttal, olvassa el a [Hálózati biztonsági csoport létrehozása](../virtual-network/manage-network-security-group.md#create-a-network-security-group) létrehozása című témakört.

Az Azure Portalon nyissa meg **a Hálózati figyelő**t, és válassza az **NSG-folyamatnaplók lehetőséget.** Válassza ki azt a hálózati biztonsági csoportot, amelyhez nsg-folyamatnaplót szeretne engedélyezni, ahogy az az alábbi képen látható:

![Az NSG-k kiválasztása, amelyek az NSG-folyamatnapló engedélyezését igénylik](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Ha a [támogatott régióktól](#supported-regions-nsg)eltérő régióban üzemeltetett NSG-k forgalomelemzését próbálja engedélyezni, "Nem található" hibaüzenet jelenik meg.

## <a name="enable-flow-log-settings"></a>Folyamatnapló-beállítások engedélyezése

A folyamatnapló beállításainak engedélyezése előtt a következő feladatokat kell elvégeznie:

Regisztrálja az Azure Insights-szolgáltatót, ha még nincs regisztrálva az előfizetéséhez:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Ha még nem rendelkezik egy Azure Storage-fiók tárolására NSG-folyamat naplók, létre kell hoznia egy tárfiókot. Létrehozhat egy tárfiókot a következő paranccsal. A parancs futtatása `<replace-with-your-unique-storage-account-name>` előtt cserélje le egy olyan névre, amely az összes Azure-helyen egyedi, 3–24 karakter hosszúságú, csak számokat és kisbetűket használva. Szükség esetén módosíthatja az erőforráscsoport nevét is.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A képen látható módon adja meg a következő beállításokat:

1. *Be* jelölés **az Állapothoz**
2. Válassza *a 2-es verzió* lehetőséget a Flow **Logs verzióhoz.** A 2-es verzió folyamatmunkamenet-statisztikákat tartalmaz (bájt és csomagok)
3. Válasszon ki egy meglévő tárfiókot a folyamatnaplók tárolásához. Ha az adatokat örökre tárolni szeretné, állítsa az értéket *0*értékre. A tárfiókért Azure Storage-díjat kell fizetnie. Győződjön meg arról, hogy a tároló nem rendelkezik a "Data Lake Storage Gen2 hierarchikus névtér engedélyezve" érték igaz.
4. Állítsa be az **adatmegőrzést** az adatok tárolásához kívánt napok számára.
5. *Válassza a Be* lehetőséget a **Forgalomelemzési állapot hoz.**
6. Adja meg a feldolgozási időközt. A választás alapján a folyamatnaplókat a tárfiókból gyűjtjük, és a Traffic Analytics feldolgozza. Választhat feldolgozási időköz minden 1 óra vagy 10 perc alatt. 
7. Jelöljön ki egy meglévő Log Analytics (OMS) munkaterületet, vagy új munkaterület **létrehozásához** válassza az Új munkaterület létrehozása lehetőséget. A Traffic Analytics a Log Analytics-munkaterületet az összesített és indexelt adatok tárolására használja, amelyeket aztán az elemzés létrehozásához használnak. Ha egy meglévő munkaterületet választ, annak a [támogatott régiók](#supported-regions-log-analytics-workspaces) egyikében kell léteznie, és az új lekérdezési nyelvre frissítve kell lennie. Ha nem kíván frissíteni egy meglévő munkaterületet, vagy nem rendelkezik munkaterülettel egy támogatott régióban, hozzon létre egy újat. A lekérdezési nyelvekről az [Azure Log Analytics új naplókeresésre való frissítés című](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)témakörben talál további információt.

> [!NOTE]
>A forgalomelemzési megoldást és az NSG-ket üzemeltető naplóelemzési munkaterületnek nem kell ugyanabban a régióban lennie. Előfordulhat például, hogy a nyugat-európai régió egyik munkaterületén van forgalomelemzés, míg az USA keleti és nyugati régiójában nsg-k lehetnek. Több NSG konfigurálható ugyanabban a munkaterületen.

8. Kattintson a **Mentés** gombra.

    ![Tárfiók, Log Analytics-munkaterület és Traffic Analytics-engedélyezés kiválasztása](./media/traffic-analytics/ta-customprocessinginterval.png)

Ismételje meg az előző lépéseket minden más NSG-k, amelyek engedélyezni szeretné a forgalom elemzését. A folyamatnaplókból származó adatok at a munkaterületre küldi a rendszer, ezért győződjön meg arról, hogy az ország helyi törvényei és előírásai lehetővé teszik az adattárolást abban a régióban, ahol a munkaterület található. Ha különböző feldolgozási intervallumokat állított be a különböző NSG-khez, az adatokat különböző időközönként gyűjtjük. Például: Engedélyezheti a kritikus virtuális nevek 10 perces feldolgozási időközét, a nem kritikus virtuális nevek esetében pedig 1 órát.

A forgalomelemzést a [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell-parancsmag használatával is konfigurálhatja az Azure PowerShellben. Futtassa `Get-Module -ListAvailable Az` a telepített verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="view-traffic-analytics"></a>Forgalomelemzés megtekintése

A Traffic Analytics megtekintéséhez keresse meg a **Network Watcher kifejezést** a portál keresősávjában. Miután bejutott a Network Watcher befutójába, a forgalomelemzés és annak képességeinek felfedezéséhez válassza a **Traffic Analytics** lehetőséget a bal oldali menüből. 

![A Traffic Analytics irányítópultjának elérése](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Az irányítópult első megjelenése akár 30 percet is igénybe vehet, mert a Traffic Analytics-nek először elegendő adatot kell összesítenie ahhoz, hogy érdemi elemzéseket lehessen létrehozni, mielőtt bármilyen jelentést létrehozhatna.

## <a name="usage-scenarios"></a>Használati forgatókönyvek

A Traffic Analytics teljes konfigurálása után érdemes lehet megszerezni az elemzéseket, a következők:

### <a name="find-traffic-hotspots"></a>Közlekedési hotspotok keresése

**keresni**

- Mely állomások, alhálózatok és virtuális hálózatok küldik vagy fogadják a legnagyobb forgalmat, a maximális kártékony forgalmat átjárva és jelentős folyamatok blokkolásával?
    - Ellenőrizze az állomás, az alhálózat és a virtuális hálózat összehasonlító diagramját. Annak megértése, hogy mely állomások, alhálózatok és virtuális hálózatok küldik vagy fogadják a legnagyobb forgalmat, segíthet azonosítani a legnagyobb forgalmat feldolgozó állomásokat, és azt, hogy a forgalom elosztása megfelelően történik-e.
    - Kiértékelheti, hogy a forgalom mennyisége megfelelő-e egy gazdagép számára. A forgalom mennyisége normális viselkedés, vagy nem érdemel további vizsgálatot?
- Mennyi bejövő/kimenő forgalom van?
    -   Várhatóan az állomás több bejövő forgalmat kap, mint a kimenő, vagy fordítva?
- A blokkolt forgalom statisztikája.
    - Miért blokkolja a gazdagép a jóindulatú forgalom jelentős mennyiségét? Ez a viselkedés további vizsgálatot igényel, és valószínűleg optimalizálása konfiguráció
- A rosszindulatú engedélyezett/blokkolt forgalom statisztikái
  - Miért kap egy gazdagép rosszindulatú forgalmat, és miért engedélyezett a rosszindulatú forrásból érkező folyamatok? Ez a viselkedés további vizsgálatot igényel, és valószínűleg a konfiguráció optimalizálását.

    Válassza **az Összes megtekintése**lehetőséget a **Host**csoportban az alábbi képen látható módon:

    ![Irányítópult, amely a legtöbb forgalmi részletet tartalmazza](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Az alábbi képen az első öt beszélő állomás időfelkapott, valamint az áramlással kapcsolatos részletek (engedélyezett – bejövő/kimenő és megtagadott – bejövő/kimenő folyamatok) láthatók az állomás esetében:

    ![Top öt legtöbbet beszélő fogadó trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**keresni**

- Melyek a leginkább beszélgető fogadó párok?
    - Várt viselkedés, például előtér- vagy háttérkommunikáció vagy szabálytalan viselkedés, például háttérhálózati internetes forgalom.
- Az engedélyezett/blokkolt forgalom statisztikái
    - Miért engedélyezi vagy blokkolja a gazdagép a jelentős forgalmat?
- Leggyakrabban használt alkalmazásprotokoll a legtöbb beszélgető gazdagéppár között:
    - Engedélyezettek ezek az alkalmazások ezen a hálózaton?
    - Megfelelően vannak konfigurálva az alkalmazások? A megfelelő protokollt használják a kommunikációhoz? Válassza a **Gyakori beszélgetés csoport** **Mind megtekintése** lehetőséget az alábbi képen látható módon:

        ![A leggyakoribb beszélgetést bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Az alábbi képen látható az első öt beszélgetés időfelkapott ideje, valamint a folyamattal kapcsolatos részletek, például az engedélyezett és a megtagadott bejövő és kimenő folyamatok egy beszélgetéspárhoz:

    ![Top öt beszédes beszélgetés részleteit és a trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**keresni**

- Melyik alkalmazásprotokollt használják a leggyakrabban a környezetében, és melyik beszélgető állomáspár használja a legjobban az alkalmazásprotokollt?
    - Engedélyezettek ezek az alkalmazások ezen a hálózaton?
    - Megfelelően vannak konfigurálva az alkalmazások? A megfelelő protokollt használják a kommunikációhoz? A várt viselkedés olyan gyakori portok, mint a 80 és a 443. A szabványos kommunikációhoz, ha szokatlan portok jelennek meg, szükség lehet a konfiguráció módosítására. Az alábbi képen válassza az **Alkalmazásport** megtekintése lehetőséget: **Application port**

        ![A legfelső alkalmazásprotokollokat bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- A következő képek az első öt L7 protokoll időtrendét és az áramlással kapcsolatos részleteket (például engedélyezett és megtagadott folyamatokat) mutatják egy L7 protokollesetében:

    ![Top öt réteg 7 protokollok részletek és trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Az alkalmazásprotokoll folyamatrészletei a naplókeresésben](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**keresni**

- A VPN-átjáró kapacitáskihasználtsági tendenciái a környezetben.
    - Minden VPN termékváltozat bizonyos sávszélességet engedélyez. A VPN-átjárók kihasználatlanok?
    - Az átjárók elérik a kapacitást? Érdemes frissíteni a következő magasabb termékváltozatra?
- Melyek a leginkább beszélgető állomások, melyik VPN-átjárón keresztül, melyik porton keresztül?
    - Ez a minta normális? Válassza **az Összes megtekintése** a **VPN-átjáró**alatt lehetőséget, ahogy az az alábbi képen látható:

        ![A legfelső aktív VPN-kapcsolatokat bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Az alábbi képen látható az Azure VPN-átjáró kapacitáskihasználtságának felkapott ideje és a folyamattal kapcsolatos részletek (például az engedélyezett folyamatok és portok):

    ![A VPN-átjáró kihasználtságának trendje és a folyamat részletei](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>A forgalom eloszlásának megjelenítése földrajzi hely szerint

**keresni**

- Adatközpontonkénti forgalomelosztás, például az adatközpontba irányuló forgalom fő forrásai, az adatközponttal beszélgető első osztályú hálózatok és a legfelső beszélgető alkalmazásprotokollok.
  - Ha nagyobb terhelést észlel egy adatközpontban, megtervezheti a forgalom hatékony elosztását.
  - Ha az engedélyezetlen hálózatok beszélgetnek az adatközpontban, akkor javítsa ki az NSG-szabályokat, hogy blokkolja őket.

    Válassza a **Térkép megtekintése** lehetőséget a **Környezet csoportban,** ahogy az az alábbi képen látható:

    ![Forgalomeloszlást bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- A földrajzi térkép a felső menüszalagon látható az olyan paraméterek kiválasztásához, mint például az adatközpontok (Üzembe helyezett/nincs üzembe helyezés/aktív/inaktív/forgalomelemzés engedélyezve/Forgalomelemzés engedélyezve) és a jóindulatú/rosszindulatú forgalmat az aktív telepítéshez hozzájáruló országok/régiók:

    ![Aktív telepítést bemutató földrajzi térképnézet](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- A földrajzi térkép a vele kommunikáló országokból/régiókból és kontinensekről származó adatközpontba irányuló forgalom eloszlását jeleníti meg kék (jóindulatú forgalom) és piros (rosszindulatú forgalom) színű vonalakon:

    ![Földrajzi térkép nézet, amely bemutatja a forgalom eloszlását országok/régiók és kontinensek között](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![A forgalom eloszlásának folyamatrészletei a naplókeresésben](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>A forgalom virtuális hálózatok általi terjesztésének megjelenítése

**keresni**

- A virtuális hálózatonkénti forgalomelosztás, a topológia, a virtuális hálózat ba irányuló forgalom felső forrásai, a virtuális hálózattal beszélgető első osztályú hálózatok és a felső beszélgető alkalmazásprotokollok.
  - Annak ismerete, hogy melyik virtuális hálózat melyik virtuális hálózathoz beszél. Ha a beszélgetés nem várható, akkor kijavítható.
  - Ha az engedélyezetlen hálózatok egy virtuális hálózattal beszélgetnek, kijavíthatja az NSG-szabályokat, hogy blokkolja a csaló hálózatokat.
 
    Válassza **a Virtuálishálózatok megtekintése** lehetőséget a **Környezet**csoportban, ahogy az az alábbi képen látható:

    ![Virtuális hálózati terjesztést bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A virtuális hálózati topológia a virtuális hálózat (virtuális hálózatok közötti kapcsolatok/aktív/inaktív), a külső kapcsolatok, az aktív folyamatok és a rosszindulatú folyamatok közötti paraméterek kiválasztásának felső menüszalagját jeleníti meg.
- A virtuális hálózati topológia előfizetések, munkaterületek, erőforráscsoportok és időintervallum alapján szűrhető. A folyamat megértését segítő további szűrők: Flow Type (InterVNet, IntraVNET, és így tovább), Flow Direction (Bejövő, Kimenő), Folyamat állapota (Engedélyezett, Blokkolva), VNET-ek (célzott és csatlakoztatott), Kapcsolat típusa (Társviszony-létesítés vagy átjáró - P2S és S2S) és NSG. Ezekkel a szűrőkkel a részletesen megvizsgálni kívánt virtuális hálózatokra összpontosíthat.
- A virtuális hálózati topológia megjeleníti a forgalom eloszlását egy virtuális hálózatra a folyamatok (Engedélyezett/Blokkolt/Bejövő/Kimenő/Jóindulatú/Rosszindulatú), az alkalmazásprotokoll és a hálózati biztonsági csoportok tekintetében, például:

    ![Virtuális hálózati topológia, amely bemutatja a forgalom eloszlásának és áramlásának részleteit](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Virtuális hálózati topológia, amely legfelső szintű és több szűrőt mutat be](./media/traffic-analytics/virtual-network-filters.png)

    ![A virtuális hálózati forgalom elosztásának folyamatrészletei a naplókeresésben](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**keresni**

- Az alhálózatonkénti forgalomelosztás, a topológia, az alhálózatba irányuló forgalom felső forrásai, az alhálózattal beszélgető első osztályú hálózatok és a legfelső beszélgető alkalmazásprotokollok.
    - Annak ismerete, hogy melyik alhálózat melyik alhálózathoz beszél. Ha nem várt beszélgetéseket lát, javíthatja a konfigurációt.
    - Ha az engedélyezetlen hálózatok egy alhálózattal beszélgetnek, kijavíthatja azt az NSG-szabályok konfigurálásával, hogy blokkolja az engedélyezetlen hálózatokat.
- Az Alhálózatok topológia az alhálózat felső menüszalagját jeleníti meg olyan paraméterek kiválasztásához, mint az aktív/inaktív alhálózat, a külső kapcsolatok, az aktív folyamatok és az alhálózat rosszindulatú folyamatai.
- Az alhálózati topológia megjeleníti a forgalom eloszlását egy virtuális hálózatra a folyamatok (Engedélyezett/Blokkolt/Bejövő/Kimenő/Jóindulatú/Rosszindulatú), alkalmazásprotokoll és NSG-k tekintetében, például:

    ![A forgalomeloszlást bemutató alhálózati topológia a folyamatok tekintetében egy virtuális hálózati alhálózatot mutat be](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**keresni**

Az alkalmazásátjárónkénti forgalomelosztás & terheléselosztót, a topforgalmi erőforrásokat, az alkalmazásátjáróval & terheléselosztóval beszélgető elsődleges engedélyezetlen hálózatokat, valamint a legfelső beszélgető alkalmazásprotokollokat. 
    
 - Annak ismerete, hogy melyik alhálózat melyik alkalmazásátjáróval vagy terheléselosztóval beszélget. Ha váratlan beszélgetéseket észlel, kijavíthatja a konfigurációt.
 - Ha az engedélyezetlen hálózatok egy alkalmazásátjáróval vagy terheléselosztóval beszélgetnek, javíthatja azt az NSG-szabályok konfigurálásával az engedélyezetlen hálózatok blokkolására. 

    ![alhálózat-topológia-bemutató-forgalom-elosztás-egy alkalmazás-átjáró-alhálózat-a-tekintetében-a-áramlások](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Az internetről forgalmat fogadó portok és virtuális gépek megtekintése

**keresni**

- Mely nyitott portok beszélgetnek az interneten keresztül?
  - Ha nem várt portok találhatók nyitva, a konfigurációt javíthatja:

    ![Az internetforgalmat fogadó és küldő portokat bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Az Azure célportjainak és -állomásainak részletei](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**keresni**

Rosszindulatú forgalom van a környezetében? Honnan származik? Hova rendeltetett?

![A kártékony forgalom részletei a naplókeresésben](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Az NSG/NSG szabályok találatai tendenciáinak megjelenítése

**keresni**

- Mely NSG/NSG-szabályok rendelkeznek a legtöbb találattal az összehasonlító diagramon a folyamatok eloszlásával?
- Melyek a legfontosabb forrás- és célbeszélgetés-párok NSG/NSG-szabályok szerint?

    ![Az NSG-találatok statisztikáit bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Az alábbi képek en az NSG-szabályok lekéréseinek és a hálózati biztonsági csoport forrás-cél folyamatának részleteinek felkapott idejét mutatják:

  - Gyorsan észlelheti, hogy mely NSG-k és NSG-szabályok haladnak át rosszindulatú folyamatokon, és melyek a felhőalapú környezethez hozzáférő rosszindulatú IP-címek.
  - Annak meghatározása, hogy mely NSG/NSG-szabályok engedélyezik/blokkolják a jelentős hálózati forgalmat
  - Válassza ki a legjobb szűrőket az NSG- vagy NSG-szabályok részletes vizsgálatához

    ![Az NSG-szabálytalálatok és a legnépszerűbb NSG-szabályok felkapott idejének bemutatása](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Top NSG szabályok statisztika részleteit naplókeresés](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

A gyakori kérdésekre adott válaszokért olvassa el [a Forgalomelemzés – gyakori kérdések ( Gyakori kérdések) témakört.](traffic-analytics-faq.md)

## <a name="next-steps"></a>További lépések

- A folyamatnaplók engedélyezéséről az [NSG-folyamatnaplózás engedélyezése](network-watcher-nsg-flow-logging-portal.md)című témakörben olvashat.
- A Traffic Analytics séma- és feldolgozási részleteinek megértéséhez olvassa el [a Forgalomelemzési séma című témakört.](traffic-analytics-schema.md)
