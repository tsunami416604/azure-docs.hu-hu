---
title: Az Azure DDoS Protection Standard kezelése az Azure Portal használatával
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan használhatja az Azure DDoS Protection Standard telemetriai adatokat az Azure Monitorban a támadások csökkentése érdekében.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279727"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Az Azure DDoS Protection Standard kezelése az Azure Portal használatával

Ismerje meg, hogyan engedélyezheti és tilthatja le az elosztott szolgáltatásmegtagadási (DDoS) védelmet, és telemetriai adatok használatával csökkentheti a DDoS-támadásokat az Azure DDoS Protection Standard szolgáltatással. A DDoS Protection Standard védi az Azure-erőforrásokat, például a virtuális gépeket, a terheléselosztókat és az olyan alkalmazásátjárókat, amelyekhez hozzá rendelt egy Azure [nyilvános IP-címet.](virtual-network-public-ip-address.md) Ha többet szeretne megtudni a DDoS Protection Standard ról és annak képességeiről, olvassa el a [DDoS Protection Standard – áttekintés című témakört.](ddos-protection-overview.md)

Az oktatóanyag bármely lépésének végrehajtása előtt jelentkezzen https://portal.azure.com be az Azure Portalra a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz rendelt fiókkal vagy az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez rendelt [egyéni szerepkörrel.](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-ddos-protection-plan"></a>DDoS védelmi terv létrehozása

A DDoS védelmi terv olyan virtuális hálózatok készletét határozza meg, amelyekd rendelkeznek DDoS védelmi szabványsal az előfizetések között. Konfigurálhat egy DDoS-védelmi tervet a szervezethez, és összekapcsolhatja a virtuális hálózatokat több előfizetésből ugyanahhoz a csomaghoz. Maga a DDoS-védelmi terv is társítva van egy előfizetéssel, amelyet a terv létrehozása során választ ki. A DDoS-védelmi csomag régiók és előfizetések között működik. Példa - létrehozhatja a csomagot a régió kelet-usa régióban, és a bérlőben lévő előfizetési #1 hivatkozhat. Ugyanaz a csomag más előfizetésekből származó virtuális hálózatokhoz kapcsolható különböző régiókban, a bérlőn keresztül. Az előfizetés a csomag van társítva a havi ismétlődő számlát a csomag, valamint a kerettúllépési díjak, ha a védett nyilvános IP-címek száma meghaladja a 100. A DDoS-díjszabásról az [árképzésrészleteiben](https://azure.microsoft.com/pricing/details/ddos-protection/)talál további információt.

A legtöbb szervezet esetében nincs szükség egynél több terv létrehozására. Egy csomag nem helyezhető át az előfizetések között. Ha módosítani szeretné, hogy egy csomag melyik előfizetésben van, törölnie kell [a meglévő csomagot,](#work-with-ddos-protection-plans) és létre kell hoznia egy újat.

1. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában.
2. Keresse meg a *DDoS*. Amikor **a DDos védelmi terv** megjelenik a keresési eredmények között, jelölje ki azt.
3. Kattintson a **Létrehozás** gombra.
4. Írja be vagy jelölje ki saját értékeit, vagy írja be a következő példaértékeket, és válassza a **Létrehozás**lehetőséget:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Név           | myDdosProtectionPlan                              |
    |Előfizetés   | Válassza ki előfizetését.                         |
    |Erőforráscsoport | Válassza **az Új létrehozása lehetőséget,** és írja be a *MyResourceGroup parancsot.* |
    |Hely       | USA keleti régiója                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>DDoS engedélyezése új virtuális hálózathoz

1. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki a saját értékeit, írja be vagy jelölje ki a következő példaértékeket, fogadja el a fennmaradó alapértelmezett értékeket, majd válassza a **Létrehozás lehetőséget:**

    | Beállítás         | Érték                                                        |
    | ---------       | ---------                                                    |
    | Név            | myVirtualNetwork                                             |
    | Előfizetés    | Válassza ki előfizetését.                                    |
    | Erőforráscsoport  | Válassza **a Meglévő használata**lehetőséget, majd a **MyResourceGroup** lehetőséget. |
    | Hely        | USA keleti régiója                                                      |
    | DDos védelem | Válassza **a Normál** lehetőséget, majd a **DDoS-védelem**csoportban válassza a **myDdosProtectionPlan lehetőséget.** A kiválasztott csomag lehet ugyanabban a vagy eltérő előfizetésben, mint a virtuális hálózat, de mindkét előfizetést ugyanahhoz az Azure Active Directory-bérlőhöz kell társozni.|

Virtuális hálózat nem helyezhető át egy másik erőforráscsoportba vagy előfizetésbe, ha a DDoS Standard engedélyezve van a virtuális hálózathoz. Ha át kell helyeznie egy virtuális hálózatot, ha a DDoS Standard engedélyezve van, először tiltsa le a DDoS Standardot, helyezze át a virtuális hálózatot, majd engedélyezze a DDoS szabványt. Az áthelyezés után a virtuális hálózat összes védett nyilvános IP-címének automatikusan behangzott házirend-küszöbértékei alaphelyzetbe állnak.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>DDoS engedélyezése meglévő virtuális hálózathoz

1. Hozzon létre egy DDoS védelmi tervet a [DDoS-védelmi csomag létrehozása](#create-a-ddos-protection-plan)című lépés végrehajtásával, ha nem rendelkezik meglévő DDoS védelmi tervvel.
2. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában.
3. Adja meg annak a virtuális hálózatnak a nevét, amelyhez engedélyezni szeretné a DDoS Protection Standard szolgáltatást a portál tetején található **Keresési erőforrások, szolgáltatások és dokumentumok mezőben.** Amikor a virtuális hálózat neve megjelenik a keresési eredmények között, jelölje ki azt.
4. Válassza a **DDoS-védelem**lehetőséget a **BEÁLLÍTÁSOK csoportban.**
5. Válassza a **Normál**lehetőséget. A **DDoS védelmi terv**csoportban válasszon ki egy meglévő DDoS védelmi tervet, vagy az 1. **Save** A kiválasztott csomag lehet ugyanabban a vagy eltérő előfizetésben, mint a virtuális hálózat, de mindkét előfizetést ugyanahhoz az Azure Active Directory-bérlőhöz kell társozni.

**Parancsok** 
- Azure CLI: [az hálózati ddos-protection létrehozása](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [Új-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>DDoS letiltása virtuális hálózaton

1. Írja be annak a virtuális hálózatnak a nevét, amelyhez le szeretné tiltani a DDoS védelmi szabványt a portál tetején található **Keresés erőforrások, szolgáltatások és dokumentumok mezőben.** Amikor a virtuális hálózat neve megjelenik a keresési eredmények között, jelölje ki azt.
2. Válassza a **DDoS-védelem**lehetőséget a **BEÁLLÍTÁSOK csoportban.**
3. Válassza **az Alapszintű** lehetőséget a **DDoS védelmi terv csoportban,** majd a **Mentés**lehetőséget.

**Parancsok** 
- Azure CLI: [az hálózati ddos-védelem törlése](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>DDoS védelmi tervek kel való kapcsolat

1. Válassza a Portál tól balra lévő Minden **szolgáltatás** lehetőséget.
2. Írja be a *DDoS értéket* a **Szűrő** mezőbe. Amikor **DDoS védelmi tervek** jelennek meg az eredményekközött, jelölje ki azt.
3. Válassza ki a listából a megtekinteni kívánt védelmi tervet.
4. A tervhez társított összes virtuális hálózat fel van sorolva.
5. Ha törölni szeretne egy tervet, először el kell választania az összes virtuális hálózatot. Ha el szeretne választani egy tervet egy virtuális hálózattól, olvassa el [a DDoS letiltása virtuális hálózaton](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Riasztások konfigurálása a DDoS védelmi mérőszámokhoz

Az Azure Monitor riasztási konfigurációjának használatával kiválaszthatja az elérhető DDoS-védelmi metrikák bármelyikét, amelyek figyelmeztetik, ha egy támadás során aktív kockázatcsökkentés érhető el. Ha a feltételek teljesülnek, a megadott cím e-mailt kap:

1. Válassza a Portál tól balra lévő Minden **szolgáltatás** lehetőséget.
2. Írja be a *Figyelő* értéket a **Szűrő** mezőbe. Amikor a **Monitor** megjelenik az eredmények között, jelölje ki azt.
3. Válassza a **Metrikák lehetőséget a** **MEGOSZTOTT SZOLGÁLTATÁSOK csoportban.**
4. Írja be vagy válassza ki a saját értékeit, vagy adja meg a következő példaértékeket, fogadja el a fennmaradó alapértelmezett értékeket, majd válassza az **OK gombot:**

    |Beállítás                  |Érték                                                                                               |
    |---------                |---------                                                                                           |
    |Név                     | myDdosAlert                                                                                        |
    |Előfizetés             | Válassza ki azt az előfizetést, amely tartalmazza azt a nyilvános IP-címet, amelyhez értesítéseket szeretne kapni.        |
    |Erőforráscsoport           | Válassza ki azt az erőforráscsoportot, amely azt a nyilvános IP-címet tartalmazza, amelyről értesítéseket szeretne kapni.      |
    |Erőforrás                 | Válassza ki azt a nyilvános IP-címet, amely tartalmazza azt a nyilvános IP-címet, amelyről értesítéseket szeretne kapni. A DDoS a virtuális hálózaton belüli erőforrásokhoz rendelt nyilvános IP-címeket figyeli. Ha nem rendelkezik nyilvános IP-címekkel rendelkező erőforrásokkal a virtuális hálózatban, először létre kell hoznia egy nyilvános IP-címmel rendelkező erőforrást. Figyelheti az [Azure-szolgáltatások virtuális hálózatában](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)felsorolt (nem klasszikus) erőforrás-kezelőn keresztül üzembe helyezett összes erőforrás nyilvános IP-címét, kivéve az Azure App Service-környezeteket és az Azure VPN-átjárót. Az oktatóanyag folytatásához gyorsan létrehozhat egy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.                   |
    |Metrika                   | A DDoS támadás vagy nem                                                                            |
    |Küszöbérték                | 1 - **1** azt jelenti, hogy támadás alatt áll. **A 0** azt jelenti, hogy nem támadnak meg.                         |
    |Időszak                   | Válassza ki a választott értéket.                                                                   |
    |Értesítés e-mailben         | Jelölje be a jelölőnégyzetet                                                                                  |
    |További rendszergazda | Adja meg e-mail címét, ha nem e-mail tulajdonosa, közreműködője vagy olvasója az előfizetéshez. |

    A támadások észlelésétől számított néhány percen belül kap egy e-mailt az Azure Monitor metrikáiból, amely az alábbi hoz hasonló:

    ![Támadási riasztás](./media/manage-ddos-protection/ddos-alert.png)


A riasztás érvényesítéséhez DDoS-támadás szimulálása a [DDoS-észlelés ellenőrzése (DDoS-észlelés) (DDoS-észlelés ellenőrzése) (DDoS-észlelés ellenőrzése) (DDoS-támadás) témakörben](#validate-ddos-detection)látható.

A webhookok és [a logikai alkalmazások](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) riasztások [létrehozásához történő konfigurálásáról](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) is többet tudhat meg.

## <a name="use-ddos-protection-telemetry"></a>DDoS-védelem telemetriája

A támadás telemetriai adatai valós időben érhetőek el az Azure Monitoron keresztül. A telemetriai adatok csak a nyilvános IP-cím csökkentés alatt álló időtartamra érhető el. A támadás előtti vagy utáni telemetriai adatok at nem látja.

1. Válassza a Portál tól balra lévő Minden **szolgáltatás** lehetőséget.
2. Írja be a *Figyelő* értéket a **Szűrő** mezőbe. Amikor a **Monitor** megjelenik az eredmények között, jelölje ki azt.
3. Válassza **a Metrikák**lehetőséget a **Megosztott szolgáltatások csoportban.**
4. Válassza ki az **előfizetés és** az **erőforrás csoportot,** amely tartalmazza a nyilvános IP-címet, amelyt telemetriai adatokat szeretne.
5. Válassza a **Nyilvános IP-cím** **erőforrástípushoz**lehetőséget, majd válassza ki azt a nyilvános IP-címet, amelyhez telemetriai adatokat szeretne.
6. **Az elérhető mérőszámok** sorozata jelenik meg a képernyő bal oldalán. Ezek a metrikák, ha kiválasztva, az **azure monitor metrikák diagram** az áttekintő képernyőn.
7. Válassza ki az **összesítés** típusát **max.**

A metrikanevek különböző csomagtípusokat és bájtokat és csomagokat mutatnak be, és az egyes mutatókon a címkenevek alapvető szerkezete a következő:

- **Eldobott címkenév** (például **eldobott bejövő csomagok DDoS):** A DDoS védelmi rendszer által eldobott/törölt csomagok száma.
- **Továbbított címke neve** (például **bejövő csomagok továbbított DDoS):** A DDoS rendszer által a cél VIP- forgalom, amely nem szűrt, a csomagok száma.
- **Nincs címkenév** (például **Bejövő csomagok DDoS):** A súrolórendszerbe érkező csomagok teljes száma – ami az eldobott és továbbított csomagok összegét jelöli.

DDoS-támadás szimulálása a telemetriai adatok érvényesítéséhez: [DDoS-észlelés ellenőrzése.](#validate-ddos-detection)

## <a name="view-ddos-mitigation-policies"></a>DDoS-kockázatcsökkentési házirendek megtekintése

A DDoS Protection Standard három automatikusan hangolt kockázatcsökkentési házirendet (TCP SYN, TCP & UDP) alkalmaz a védett erőforrás minden nyilvános IP-címére, a DDoS-t engedélyező virtuális hálózatban. A házirend-küszöbértékeket úgy tekintheti meg, hogy a Bejövő TCP-csomagok kiválasztásával elindítja a **DDoS-kockázatcsökkentést** és a bejövő UDP-csomagokat, hogy az **aggregálási** típusú **DDoS-kockázatcsökkentési** metrikákat "Max" típusúvá tegye, ahogy az az alábbi képen látható:

![Kockázatcsökkentési irányelvek megtekintése](./media/manage-ddos-protection/view-mitigation-policies.png)

A házirend-küszöbértékek automatikusan konfigurálva vannak az Azure gépi tanuláson alapuló hálózati forgalom profilozásán keresztül. Csak a házirend-küszöbérték megszegése esetén történik DDoS-kockázatcsökkentés a támadás alatt lévő IP-cím.

## <a name="configure-ddos-attack-analytics"></a>DDoS-támadás elemzésének konfigurálása
Az Azure DDoS Protection szabvány részletes támadási elemzéseket és vizualizációt biztosít a DDoS Attack Analytics segítségével. A virtuális hálózataikat a DDoS-támadásokkal szemben védő ügyfelek részletes encika-forgalmat és a támadásmérséklési jelentéseken keresztüli támadáscsökkentési jelentéseken keresztül idozni a támadások csökkentésére irányuló műveleteket, & a kockázatcsökkentési folyamatnaplókat. 

## <a name="configure-ddos-attack-mitigation-reports"></a>DDoS-támadáscsökkentési jelentések konfigurálása
A támadáscsökkentési jelentések a Netflow protokoll adatait használja, amelyek összesítve részletes információkat nyújtanak az erőforrás elleni támadásról. Ha egy nyilvános IP-erőforrás támadás alatt áll, a jelentés létrehozása elindul, amint a kockázatcsökkentés elindul. 5 percenként növekményes jelentés, a teljes mérséklési időszakra vonatkozó kockázatcsökkentési jelentés jön létre. Ez biztosítja, hogy abban az esetben, ha a DDoS-támadás hosszabb ideig folytatódik, 5 percenként megtekintheti a legújabb pillanatképet a kockázatcsökkentési jelentésről, és egy teljes összegzést, ha a támadás enyhítése véget ért. 

1. Válassza a Portál tól balra lévő Minden **szolgáltatás** lehetőséget.
2. Írja be a *Figyelő* értéket a **Szűrő** mezőbe. Amikor a **Monitor** megjelenik az eredmények között, jelölje ki azt.
3. A **BEÁLLÍTÁSOK csoportban**válassza a **Diagnosztikai beállítások lehetőséget.**
4. Válassza ki a naplózni kívánt nyilvános IP-címet tartalmazó **Előfizetés** és **erőforrás csoportot.**
5. Válassza a **Nyilvános IP-cím** **az erőforrás típushoz**lehetőséget, majd válassza ki azt a nyilvános IP-címet, amelyhez naplózni szeretné a metrikákat.
6. Válassza **a Diagnosztika bekapcsolása lehetőséget a DDoSMitigationReports napló összegyűjtéséhez,** majd válassza ki az alábbi lehetőségek közül a szükséges lehetőségeket:

    - **Archiválás tárfiókba:** Az adatok egy Azure Storage-fiókba íródnak. Erről a beállításról az [Archiválási diagnosztikai naplók](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben olvashat bővebben.
    - **Streamelés egy eseményközpontba:** Lehetővé teszi, hogy a naplófogadó az Azure Event Hub használatával vegye fel a naplókat. Az eseményközpontok lehetővé teszik a Splunk vagy más SIEM rendszerekkel való integrációt. Erről a beállításról a [Diagnosztikai naplók streamelése egy eseményközpontba](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben olvashat bővebben.
    - **Küldés a Log Analytics szolgáltatásba:** Naplók at ír az Azure Monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, [olvassa el a Naplók gyűjtése az Azure Monitor naplóiban való használatra.](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Mind a növekményes & a támadás utáni kockázatcsökkentési jelentések a következő mezőket tartalmazzák:
- Támadási vektorok
- Forgalmi statisztikák
- Az eldobott csomagok oka
- Az érintett jegyzőkönyvek
- A 10 legfontosabb ország vagy régió
- Top 10 forrás ASNs

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS-támadáskockázat-csökkentő folyamatnaplók konfigurálása
A támadáscsökkentési folyamatnaplók lehetővé teszik az eldobott forgalom, a továbbított forgalom és más érdekes adatpontok közel valós idejű áttekintését egy aktív DDoS-támadás során. Ezek az adatok folyamatos streamelése a SIEM-rendszerek en keresztül eseményközpont közel valós idejű figyelés, a lehetséges műveletek et, és a védelmi műveletek igényének kezelése. 

1. Válassza a Portál tól balra lévő Minden **szolgáltatás** lehetőséget.
2. Írja be a *Figyelő* értéket a **Szűrő** mezőbe. Amikor a **Monitor** megjelenik az eredmények között, jelölje ki azt.
3. A **BEÁLLÍTÁSOK csoportban**válassza a **Diagnosztikai beállítások lehetőséget.**
4. Válassza ki a naplózni kívánt nyilvános IP-címet tartalmazó **Előfizetés** és **erőforrás csoportot.**
5. Válassza a **Nyilvános IP-cím** **az erőforrás típushoz**lehetőséget, majd válassza ki azt a nyilvános IP-címet, amelyhez naplózni szeretné a metrikákat.
6. Válassza **a Diagnosztika bekapcsolása lehetőséget a DDoSMitigationFlowLogs napló összegyűjtéséhez,** majd válasszon annyi tavan alábbi lehetőséget, amennyire szüksége van:

    - **Archiválás tárfiókba:** Az adatok egy Azure Storage-fiókba íródnak. Erről a beállításról az [Archiválási diagnosztikai naplók](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben olvashat bővebben.
    - **Streamelés egy eseményközpontba:** Lehetővé teszi, hogy a naplófogadó az Azure Event Hub használatával vegye fel a naplókat. Az eseményközpontok lehetővé teszik a Splunk vagy más SIEM rendszerekkel való integrációt. Erről a beállításról a [Diagnosztikai naplók streamelése egy eseményközpontba](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben olvashat bővebben.
    - **Küldés a Log Analytics szolgáltatásba:** Naplók at ír az Azure Monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, [olvassa el a Naplók gyűjtése az Azure Monitor naplóiban való használatra.](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
1. A folyamatnaplók adatainak megtekintéséhez az Azure Analytics irányítópultján importálhatja a minta irányítópultot ahttps://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

A folyamatnaplók a következő mezőkkel rendelkeznek: 
- Forrás IP-címe
- Cél IP-címe
- Forrásport 
- Célport 
- Protokoll típusa 
- A mérséklés során végrehajtott intézkedések



## <a name="validate-ddos-detection"></a>DDoS-észlelés ellenőrzése

A Microsoft a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) vállalattal együttműködve olyan felületet hozhat létre, ahol a DDoS Protection-kompatibilis nyilvános IP-címek ellen forgalmat hozhat létre szimulációkhoz. A BreakPoint Cloud szimuláció lehetővé teszi, hogy:

- Annak ellenőrzése, hogy a Microsoft Azure DDoS Protection hogyan védi az Azure-erőforrásokat a DDoS-támadásokellen
- Optimalizálja az incidensválasz-folyamatot a DDoS-támadás alatt
- Dokumentum DDoS-megfelelőség
- A hálózati biztonsági csapatok betanítása

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>DDoS-védelmi riasztások megtekintése az Azure Security Centerben

Az Azure Security Center a [biztonsági riasztások](/azure/security-center/security-center-managing-and-responding-alerts)listáját tartalmazza, amely ekkel segít a problémák kivizsgálásához és elhárításához. Ezzel a funkcióval egységes nézetet kap a riasztásokról, beleértve a DDoS-támadással kapcsolatos riasztásokat és a támadás közel időben idoszat csökkentésére tett műveleteket.
A DDoS-támadások észlelése és enyhítése esetén két konkrét riasztás jelenik meg:

- **Nyilvános IP-címhez észlelt DDoS-támadás:** Ez a riasztás akkor jön létre, amikor a DDoS védelmi szolgáltatás észleli, hogy az egyik nyilvános IP-cím egy DDoS-támadás célpontja.
- **DDoS-támadás a nyilvános IP:** Ez a riasztás akkor jön létre, ha a nyilvános IP-cím elleni támadás mérséklésre került.
A riasztások megtekintéséhez nyissa meg a **Security Center** az Azure Portalon. A **Veszélyforrások elleni védelem**csoportban válassza a Biztonsági **riasztások**lehetőséget. A következő képernyőképen egy példa a DDoS támadásriasztások.

![DDoS-riasztás az Azure Security Centerben](./media/manage-ddos-protection/ddos-alert-asc.png)

A riasztások általános információkat tartalmaznak a nyilvános IP-cím, amely támadás alatt áll, a földrajzi és fenyegetésfelderítési információk, valamint a szervizelési lépéseket.

## <a name="permissions"></a>Engedélyek

A DDoS védelmi tervekkel való munkához a fiókot hozzá kell rendelni a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Műveletek                                            | Név                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS védelmi terv elolvasása              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS védelmi terv létrehozása vagy frissítése  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS védelmi terv törlése            |
| Microsoft.Network/ddosProtectionTervek/csatlakozás/művelet | Csatlakozás DDoS védelmi tervhez              |

A virtuális hálózat DDoS-védelmének engedélyezéséhez a fiókhoz hozzá kell rendelni a [virtuális hálózatokhoz tartozó](manage-virtual-network.md#permissions)megfelelő műveleteket is.

## <a name="next-steps"></a>További lépések

- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra