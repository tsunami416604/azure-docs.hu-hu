---
title: Azure DDoS Protection standard kezelése a Azure Portal használatával
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan használhatja a Azure Monitor Azure DDoS Protection standard telemetria a támadás enyhítése érdekében.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 786b21e7571ed173d2da90f587a5b76d8c92a13d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279727"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure DDoS Protection standard kezelése a Azure Portal használatával

Megtudhatja, hogyan engedélyezheti és tilthatja le az elosztott szolgáltatásmegtagadási (DDoS) védelmet, és hogyan használhatja a telemetria-t a Azure DDoS Protection standard szintű DDoS-támadások enyhítésére. A DDoS Protection standard védelmet biztosít az Azure-erőforrások, például a virtuális gépek, a terheléselosztó és az olyan Application Gateway-átjárók számára, amelyekhez hozzá van rendelve egy Azure [nyilvános IP-cím](virtual-network-public-ip-address.md) . Ha többet szeretne megtudni a DDoS Protection standard és képességeiről, tekintse meg a [DDoS Protection standard áttekintése](ddos-protection-overview.md)című témakört.

Az oktatóanyag lépéseinek elvégzése előtt jelentkezzen be a Azure Portalba https://portal.azure.com egy olyan fiókkal, amely a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz tartozik, vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyek](#permissions)területen felsorolt megfelelő műveletekhez van rendelve.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-ddos-protection-plan"></a>DDoS elleni védelmi terv létrehozása

A DDoS Protection-csomag olyan virtuális hálózatokat határoz meg, amelyeken engedélyezve van a DDoS Protection standard, az előfizetések között. Beállíthat egy DDoS Protection-tervet a szervezet számára, és a virtuális hálózatokat több előfizetésből ugyanahhoz a csomaghoz kapcsolhatja. Maga a DDoS Protection terv is társítva van egy előfizetéshez, amelyet a csomag létrehozásakor választott ki. A DDoS Protection terv a régiók és az előfizetések között működik. Példa – létrehozhatja a csomagot az USA keleti régiójában, és a bérlőhöz tartozó előfizetési #1 hivatkozását is használhatja. Ugyanez a csomag más előfizetésekhez tartozó virtuális hálózatokhoz is társítható a különböző régiókban, a bérlők között. Az előfizetés, amelyhez a terv társítva van, a csomag havi ismétlődő számlájának, valamint a túlterhelési díjaknak a része, ha a védett nyilvános IP-címek száma meghaladja a 100-ot. A DDoS díjszabásával kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/ddos-protection/).

A legtöbb szervezet esetében nem szükséges több csomag létrehozása. A csomag nem helyezhető át az előfizetések között. Ha módosítani szeretné a csomag előfizetését, [törölnie kell a meglévő csomagot](#work-with-ddos-protection-plans) , és létre kell hoznia egy újat.

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
2. Keressen rá a *DDoS*kifejezésre. Ha a **DDos Protection-csomag** megjelenik a keresési eredmények között, válassza ki.
3. Kattintson a **Létrehozás** gombra.
4. Adja meg vagy válassza ki a saját értékeit, vagy írja be, vagy válassza ki a következő példában szereplő értékeket, majd válassza a **Létrehozás**lehetőséget:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Név           | myDdosProtectionPlan                              |
    |-előfizetés   | Válassza ki előfizetését.                         |
    |Erőforráscsoport | Válassza az **új létrehozása** elemet, és adja meg a *myResourceGroup* |
    |Hely       | USA keleti régiója                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>A DDoS engedélyezése új virtuális hálózathoz

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki a saját értékeit, vagy válassza ki a következő példákat, fogadja el a fennmaradó alapértelmezett értékeket, majd válassza a **Létrehozás**elemet:

    | Beállítás         | Érték                                                        |
    | ---------       | ---------                                                    |
    | Név            | myVirtualNetwork                                             |
    | -előfizetés    | Válassza ki előfizetését.                                    |
    | Erőforráscsoport  | Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget. |
    | Hely        | USA keleti régiója                                                      |
    | DDos-védelem | Válassza a **standard** lehetőséget, majd a **DDoS Protection**területen válassza a **myDdosProtectionPlan**lehetőséget. A kiválasztott csomag a virtuális hálózattal megegyező vagy eltérő előfizetésben is lehet, de mindkét előfizetéshez ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.|

A virtuális hálózat nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe, ha a DDoS standard engedélyezve van a virtuális hálózathoz. Ha a virtuális hálózatot a DDoS standard használatával kell áthelyeznie, először tiltsa le a DDoS standardot, helyezze át a virtuális hálózatot, majd engedélyezze a DDoS standard használatát. Az áthelyezést követően a rendszer alaphelyzetbe állítja a virtuális hálózatban lévő összes védett nyilvános IP-cím automatikusan beállított szabályzatának küszöbértékeit.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>A DDoS engedélyezése meglévő virtuális hálózathoz

1. Hozzon létre egy DDoS Protection-tervet a [DDoS Protection-terv létrehozása](#create-a-ddos-protection-plan)című cikk lépéseinek végrehajtásával, ha nem rendelkezik meglévő DDoS Protection-csomaggal.
2. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
3. Adja meg annak a virtuális hálózatnak a nevét, amely számára engedélyezni szeretné a DDoS Protection a standard értéket a portál tetején található **erőforrások, szolgáltatások és dokumentumok keresése mezőbe** . Ha a virtuális hálózat neve megjelenik a keresési eredmények között, válassza ki.
4. A **Beállítások**területen válassza a **DDoS Protection**lehetőséget.
5. Válassza a **standard**lehetőséget. A **DDoS elleni védelmi terv**területen válasszon ki egy meglévő DDoS Protection-csomagot, vagy az 1. lépésben létrehozott csomagot, majd kattintson a **Mentés**gombra. A kiválasztott csomag a virtuális hálózattal megegyező vagy eltérő előfizetésben is lehet, de mindkét előfizetéshez ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.

**Parancsok** 
- Azure CLI: [az Network DDoS-Protection Create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- PowerShell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>A DDoS letiltása virtuális hálózat esetén

1. Adja meg annak a virtuális hálózatnak a nevét, amelyre le szeretné tiltani a DDoS Protection-szabványt a portál tetején található **erőforrások, szolgáltatások és dokumentumok keresése mezőbe** . Ha a virtuális hálózat neve megjelenik a keresési eredmények között, válassza ki.
2. A **Beállítások**területen válassza a **DDoS Protection**lehetőséget.
3. Válassza az **alapszintű** **DDoS Protection-csomag** lehetőséget, majd kattintson a **Mentés**gombra.

**Parancsok** 
- Azure CLI: [az Network DDoS-Protection delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- PowerShell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>A DDoS Protection-csomagok használata

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *DDoS* értéket. Ha a **DDoS Protection-csomagok** megjelennek az eredmények között, válassza ki.
3. Válassza ki a listából a megtekinteni kívánt védelmi tervet.
4. A csomaghoz társított összes virtuális hálózat megjelenik.
5. Ha törölni szeretne egy csomagot, először el kell távolítania az összes virtuális hálózatot. Ha egy virtuális hálózatról szeretne leválasztani egy tervet, tekintse meg a [DDoS letiltása virtuális hálózathoz](#disable-ddos-for-a-virtual-network)című témakört.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Riasztások konfigurálása DDoS Protection-mérőszámokhoz

A rendelkezésre álló DDoS-védelmi mérőszámok bármelyikét kiválaszthatja, ha riasztást szeretne kapni, ha a támadás során aktív megoldás van a Azure Monitor riasztás konfigurációjának használatával. Ha a feltételek teljesülnek, a megadott cím riasztási e-mailt kap:

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. Válassza a **metrikák** lehetőséget a **megosztott szolgáltatások**területen.
4. Adja meg vagy válassza ki a saját értékeit, vagy adja meg a következő példákat, fogadja el a fennmaradó alapértelmezett értékeket, majd kattintson az **OK gombra**:

    |Beállítás                  |Érték                                                                                               |
    |---------                |---------                                                                                           |
    |Név                     | myDdosAlert                                                                                        |
    |-előfizetés             | Válassza ki azt az előfizetést, amely a nyilvános IP-címet tartalmazza, amelyhez riasztásokat szeretne kapni.        |
    |Erőforráscsoport           | Válassza ki azt az erőforráscsoportot, amely tartalmazza azt a nyilvános IP-címet, amelyhez riasztásokat szeretne kapni.      |
    |Resource                 | Válassza ki azt a nyilvános IP-címet, amely tartalmazza azt a nyilvános IP-címet, amelyhez riasztásokat szeretne kapni. A DDoS figyeli a virtuális hálózaton belüli erőforrásokhoz rendelt nyilvános IP-címeket. Ha nem rendelkezik nyilvános IP-címmel rendelkező erőforrásokkal a virtuális hálózaton, először létre kell hoznia egy nyilvános IP-címmel rendelkező erőforrást. Az [Azure-szolgáltatásokhoz tartozó Virtual Network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)(nem klasszikus) Resource Managerrel telepített összes erőforrás nyilvános IP-címét a Azure app Service környezetek és az Azure VPN Gateway kivételével figyelheti. Az oktatóanyag folytatásához gyorsan létrehozhat egy Windows vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.                   |
    |Metrika                   | DDoS-támadás alatt vagy nem                                                                            |
    |Küszöbérték                | 1 – **1** azt jelenti, hogy támadás alatt áll. **0** azt jelenti, hogy nem támadás alatt áll.                         |
    |Időszak                   | Válassza ki a választott értéket.                                                                   |
    |Értesítés e-mailben         | Jelölje be a jelölőnégyzetet                                                                                  |
    |További rendszergazda | Adja meg az e-mail-címét, ha Ön nem e-mail-tulajdonos, közreműködő vagy olvasó az előfizetéshez. |

    A támadások észlelése néhány percen belül egy e-mailt kap Azure Monitor metrikától, amely az alábbi képhez hasonlóan néz ki:

    ![Támadási riasztás](./media/manage-ddos-protection/ddos-alert.png)


A DDOS-támadások szimulálása a riasztás ellenőrzéséhez lásd: a [DDoS-észlelés ellenőrzése](#validate-ddos-detection).

További információ: [webhookok](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [logikai alkalmazások](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konfigurálása riasztások létrehozásához.

## <a name="use-ddos-protection-telemetry"></a>A DDoS Protection telemetria használata

A támadás telemetria valós időben Azure Monitor biztosítjuk. A telemetria csak arra az időtartamra érhető el, ameddig a nyilvános IP-cím mérséklés alatt áll. A telemetria nem jelenik meg a támadás előtt vagy után.

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. Válassza a **metrikák**lehetőséget a **megosztott szolgáltatások**területen.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a telemetria kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt a megadott nyilvános IP-címet, amelyet telemetria szeretne.
6. A képernyő bal oldalán a **rendelkezésre álló metrikák** egy sorozata jelenik meg. Ezek a metrikák, ha ki vannak jelölve, az áttekintő képernyő **Azure monitor metrikák diagramján** vannak ábrázolva.
7. Válassza ki az **összesítési** típust **Max** .

A metrikák nevei különböző típusú csomagokat és bájtokat, illetve csomagokat, a címkék neveinek alapszintű összeállításával az egyes mérőszámokban az alábbiak szerint:

- **Eldobott címke neve** (például a **bejövő csomagok eldobott DDoS**): a DDoS Protection rendszer által eldobott/kihagyott csomagok száma.
- **Továbbított címke neve** (például **bejövő csomagok továbbított DDoS**): a DDoS rendszer által továbbított csomagok száma a célként megadott VIP – a nem szűrt forgalom.
- **Nincs címke neve** (például a **bejövő csomagok DDoS**): a kimosó rendszerbe érkezett csomagok teljes száma, amely az eldobott és továbbított csomagok összegét jelöli.

A DDOS-támadások szimulálása a telemetria ellenőrzéséhez: a [DDoS-észlelés ellenőrzése](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>DDoS-mérséklési szabályzatok megtekintése

DDoS Protection a standard három automatikusan hangolt kockázatcsökkentő házirendet alkalmaz (TCP SYN, TCP & UDP) a védett erőforrás minden nyilvános IP-címéhez, a DDoS-t engedélyező virtuális hálózatban. A házirend küszöbértékeit úgy tekintheti meg, ha kiválasztja a **Bejövő TCP-csomagokat a DDoS-mérséklés** **és a** bejövő UDP-csomagok kiválasztásával, hogy a **DDoS-mérséklési** metrikákat "Max" értékkel aktiválja, ahogy az alábbi képen látható:

![Kockázatcsökkentő házirendek megtekintése](./media/manage-ddos-protection/view-mitigation-policies.png)

A házirend-küszöbértékek automatikusan konfigurálva vannak az Azure Machine learning-alapú hálózati forgalom profilkészítése révén. A támadási IP-cím esetében csak a házirend küszöbértékének megszegése esetén a DDoS-elhárítás történik.

## <a name="configure-ddos-attack-analytics"></a>A DDoS Attack Analytics konfigurálása
Azure DDoS Protection a standard részletes támadási elemzéseket és vizualizációkat biztosít a DDoS Attack Analytics használatával. A DDoS-támadásokkal szemben a virtuális hálózatokat védő ügyfelek részletesen megtekinthetik a támadási forgalmat, valamint a támadás enyhítésére tett lépéseket a támadásokkal kapcsolatos kockázatcsökkentő jelentésekben & a kockázatcsökkentő folyamat naplófájljait. 

## <a name="configure-ddos-attack-mitigation-reports"></a>A DDoS elleni támadás mérséklési jelentéseinek konfigurálása
A támadás-elhárítási jelentések a Netflow protokoll azon adatait használják, amelyek összesítése az erőforrás támadásával kapcsolatos részletes információk biztosítására szolgál. Ha a nyilvános IP-cím a támadás alatt áll, a jelentés létrehozása azonnal elindul, amint a megoldás elindul. Az 5 percenként generált növekményes jelentés és a teljes kockázatcsökkentő időszakra vonatkozó utólagos jelentés. Így biztosíthatja, hogy a DDoS-támadás hosszabb ideig továbbra is fennmaradjon, és 5 percenként megtekintheti a kockázatcsökkentő jelentés legfrissebb pillanatképét, valamint egy teljes összegzést, amint a támadás feloldható. 

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. A **Beállítások**területen válassza a **diagnosztikai beállítások**elemet.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a NAPLÓZNI kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt a nyilvános IP-címet, amelyre vonatkozóan naplózni kívánja a metrikákat.
6. **A DDoSMitigationReports-napló bekapcsolásához válassza a diagnosztika bekapcsolása** lehetőséget, majd az alábbi lehetőségek közül választhat:

    - **Archiválás egy Storage-fiókba**: az adatok egy Azure Storage-fiókba íródnak. Ha többet szeretne megtudni erről a lehetőségről, olvassa el a [diagnosztikai naplók archiválása](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.
    - **Stream az Event hub**-ba: lehetővé teszi, hogy a naplók egy Azure Event hub használatával vegyenek fel naplókat. Az Event hubok lehetővé teszik az integrációt a splunk vagy más SIEM-rendszerekkel. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [stream diagnosztikai naplók az Event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)szolgáltatásban című témakört.
    - **Küldés log Analyticsba**: a naplók beírása a Azure monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [naplók összegyűjtése Azure monitor naplókban való használatát](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető témakört.

A növekményes & a támadás utáni kockázatcsökkentő jelentések mind a következő mezőket tartalmazzák
- Támadási vektorok
- Forgalmi statisztika
- Eldobott csomagok oka
- Érintett protokollok
- Az első 10 forrás ország vagy régió
- A 10 legjobb forrás ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>A DDoS elleni támadás mérséklési folyamat naplófájljainak konfigurálása
A támadás-elhárítási folyamat naplói lehetővé teszik, hogy a közel valós időben egy aktív DDoS-támadás során áttekintse az eldobott forgalmat, a továbbított forgalmat és az egyéb érdekes datapoints. Az adatok állandó streamjét a SIEM-rendszerbe az Event hub segítségével, közel valós idejű figyeléssel, a lehetséges műveletek elvégzésével és a védelmi műveletek igényének kielégítésével végezheti el. 

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. A **Beállítások**területen válassza a **diagnosztikai beállítások**elemet.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a NAPLÓZNI kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt a nyilvános IP-címet, amelyre vonatkozóan naplózni kívánja a metrikákat.
6. **A DDoSMitigationFlowLogs-napló bekapcsolásához válassza a diagnosztika bekapcsolása** lehetőséget, majd az alábbi lehetőségek közül választhat:

    - **Archiválás egy Storage-fiókba**: az adatok egy Azure Storage-fiókba íródnak. Ha többet szeretne megtudni erről a lehetőségről, olvassa el a [diagnosztikai naplók archiválása](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.
    - **Stream az Event hub**-ba: lehetővé teszi, hogy a naplók egy Azure Event hub használatával vegyenek fel naplókat. Az Event hubok lehetővé teszik az integrációt a splunk vagy más SIEM-rendszerekkel. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [stream diagnosztikai naplók az Event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)szolgáltatásban című témakört.
    - **Küldés log Analyticsba**: a naplók beírása a Azure monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [naplók összegyűjtése Azure monitor naplókban való használatát](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető témakört.
1. Ha meg szeretné tekinteni a folyamat naplófájljainak adatait az Azure Analytics irányítópultján, importálhatja a minta irányítópultot https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

A flow-naplók a következő mezőket fogják tartalmazni: 
- Forrás IP-címe
- Cél IP-címe
- Forrásport 
- Célport 
- Protokoll típusa 
- A mérséklés során végrehajtott művelet



## <a name="validate-ddos-detection"></a>DDoS-észlelés ellenőrzése

A Microsoft a BreakingPoint- [felhővel](https://www.ixiacom.com/products/breakingpoint-cloud) együttműködve olyan felületet hoz létre, amely a szimulációk számára DDoS Protection-kompatibilis nyilvános IP-címekkel való adatforgalmat is létrehoz. A töréspontok Felhőbeli szimulációja a következőket teszi lehetővé:

- Annak ellenőrzése, hogy a Microsoft Azure DDoS Protection megvédheti Azure-erőforrásait a DDoS-támadásoktól
- Optimalizálja az incidensek megválaszolásának folyamatát a DDoS-támadás alatt
- Dokumentum DDoS-megfelelősége
- A hálózati biztonsági csapatok betanítása

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>DDoS Protection-riasztások megtekintése Azure Security Center

Azure Security Center a [biztonsági riasztások](/azure/security-center/security-center-managing-and-responding-alerts)listáját tartalmazza, és információt nyújt a problémák kivizsgálásához és javításához. Ezzel a funkcióval egységes nézetet kap a riasztásokról, többek között a DDoS-támadásokkal kapcsolatos riasztásokról, valamint a támadásoknak a közeljövőben történő enyhítésére tett műveletekről.
A DDoS-támadások észlelésének és enyhítésének két konkrét riasztása jelenik meg:

- **DDoS-támadás észlelhető a nyilvános IP-címek esetében**: Ez a riasztás akkor jön létre, ha a DDoS Protection szolgáltatás észleli, hogy az egyik nyilvános IP-cím a DDoS-támadás célpontja.
- **DDoS-támadás a nyilvános IP-címek esetében**: Ez a riasztás akkor jön létre, ha a nyilvános IP-cím támadása le van tiltva.
A riasztások megtekintéséhez nyissa meg **Security Center** a Azure Portalban. A **veszélyforrások védelme**területen válassza a **biztonsági riasztások**lehetőséget. Az alábbi képernyőképen egy példa látható a DDoS-támadások riasztására.

![DDoS-riasztás a Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

A riasztások a támadás, a Geo és a veszélyforrások felderítésére vonatkozó információk, valamint a Szervizelési lépések általános információit tartalmazzák a nyilvános IP-címekről.

## <a name="permissions"></a>Engedélyek

A DDoS elleni védelmi tervekkel való együttműködéshez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Művelet                                            | Név                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS Protection-csomag beolvasása              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS elleni védelmi terv létrehozása vagy frissítése  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS elleni védelmi terv törlése            |
| Microsoft.Network/ddosProtectionPlans/join/action | A DDoS elleni védelmi terv csatlakoztatása              |

Ha engedélyezni szeretné a DDoS Protectiont egy virtuális hálózat számára, a fióknak hozzá kell rendelnie a [virtuális hálózatok megfelelő műveleteit](manage-virtual-network.md#permissions)is.

## <a name="next-steps"></a>További lépések

- [Azure](policy-samples.md) -szabályzat létrehozása és alkalmazása virtuális hálózatokhoz