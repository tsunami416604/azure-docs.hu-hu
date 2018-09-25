---
title: Kezelheti az Azure DDoS Protection szabványos az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure DDoS Protection normál telemetriai az Azure monitorban támadás.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: jdial
ms.openlocfilehash: 59cfcc72abee100b95cf17033083827fbb30f9f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986693"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Kezelheti az Azure DDoS Protection szabványos az Azure portal használatával

Ismerje meg, hogyan engedélyezheti és letilthatja az elosztott szolgáltatásmegtagadásos (DDoS-) elleni és telemetriát használja az Azure DDoS Protection standard szintű DDoS-támadások elhárításához. Azure-erőforrások, például virtuális gépek, a terheléselosztók és az alkalmazásátjáróknál, amelyeknél az Azure DDoS Protection Standard védi [nyilvános IP-cím](virtual-network-public-ip-address.md) rendelve. A DDoS Protection Standard vagy képességeivel kapcsolatos további információkért lásd: [DDoS Protection Standard áttekintése](ddos-protection-overview.md).

Mielőtt bármelyik befejezése lépéseket ebben az oktatóanyagban, jelentkezzen be az Azure Portalra a https://portal.azure.com rendelt fiókkal a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely hozzá van rendelve a megfelelő a felsorolt műveletek [engedélyek](#permissions).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-ddos-protection-plan"></a>A DDoS protection-díjcsomag létrehozása

A DDoS protection-díjcsomag határozza meg, amelyeken engedélyezve van, előfizetések között a DDoS protection-szabvány virtuális hálózatok. Beállíthatja, hogy a szervezet és a hivatkozás virtuális hálózatok több előfizetést, a csomagot egy DDoS elleni védelmi tervet. A DDoS Protection-Díjcsomag maga is egy előfizetést, a terv létrehozása közben kiválasztott társítva. Az előfizetés a terv társítva a csomag, valamint a kerettúllépési díjfizetést tesz szükségessé, havi ismétlődő számla tekintetében abban az esetben a védett nyilvános IP-címek száma meghaladja a 100. A DDoS-díjszabás további információkért lásd: [díjszabás](https://azure.microsoft.com/pricing/details/ddos-protection/).

Több csomag létrehozása nem kötelező a legtöbb szervezet számára. A csomag nem lehet áthelyezni, előfizetések között. Ha azt szeretné, módosítsa a csomag megtalálható, hogy [törölje a meglévő csomag](#work-with-ddos-protection-plans) , és hozzon létre egy újat.

1. Válassza ki **erőforrás létrehozása** az Azure portal bal felső sarkában található.
2. Keresse meg *DDoS*. Amikor **DDos protection-díjcsomag** megjelenik a keresési eredmények között, válassza ki.
3. Kattintson a **Létrehozás** gombra.
4. Adja meg vagy válassza ki a saját értékeket, vagy adja meg, vagy válassza ki a következő példában szereplő értékeket, és válassza **létrehozás**:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Name (Név)           | myDdosProtectionPlan                              |
    |Előfizetés   | Válassza ki előfizetését.                         |
    |Erőforráscsoport | Válassza ki **új létrehozása** , és adja meg *myResourceGroup* |
    |Hely       | USA keleti régiója                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Új virtuális hálózat DDoS engedélyezése

1. Válassza ki **erőforrás létrehozása** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki a saját értékeit, adjon meg vagy válassza ki a következő példaértékeket, fogadja el a többi alapértelmezett beállítást, és válassza **létrehozás**:

    | Beállítás         | Érték                                                        |
    | ---------       | ---------                                                    |
    | Name (Név)            | myVirtualNetwork                                             |
    | Előfizetés    | Válassza ki előfizetését.                                    |
    | Erőforráscsoport  | Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget. |
    | Hely        | USA keleti régiója                                                      |
    | A DDos protection | Válassza ki **Standard** , majd a **a DDoS protection**válassza **myDdosProtectionPlan**. A kiválasztott csomag lehet, mint a virtuális hálózat azonos vagy eltérő előfizetésben, de mindkét előfizetéshez kell tartoznia ugyanahhoz az Azure Active Directory-bérlőhöz.|

Virtuális hálózat nem helyezhető át egy másik erőforráscsoportba vagy előfizetésbe, ha a virtuális hálózat DDoS Standard engedélyezve van. Ha át kell helyeznie egy virtuális hálózat DDoS standard engedélyezve van, először tiltsa le a DDoS Standard, helyezze át a virtuális hálózat és engedélyeznie kell a DDoS-szabvány. Az áthelyezés után az összes védett nyilvános IP-címek a virtuális hálózatban automatikusan beállított szabályzat küszöbértékeinek állnak vissza.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Meglévő virtuális hálózat DDoS engedélyezése

1. Hozzon létre egy DDoS protection-díjcsomag által ismertetett lépéseket követve [egy DDoS elleni védelmi tervet](#create-a-ddos-protection-plan), ha nem rendelkezik egy meglévő DDoS elleni védelmi tervet.
2. Válassza ki **erőforrás létrehozása** az Azure portal bal felső sarkában található.
3. Adja meg a nevét, amely engedélyezi a DDoS Protection Standard az a virtuális hálózat a **erőforrások, szolgáltatások és a docs mezőben keressen** a portál tetején. Amikor a virtuális hálózat neve megjelenik a keresési eredmények között, kattintson rá.
4. Válassza ki **a DDoS protection**alatt **beállítások**.
5. Válassza ki **Standard**. A **DDoS protection-díjcsomag**, egy meglévő DDoS elleni védelmi tervet, vagy az 1. lépésben létrehozott csomagot, majd válassza ki és **mentése**. A kiválasztott csomag lehet, mint a virtuális hálózat azonos vagy eltérő előfizetésben, de mindkét előfizetéshez kell tartoznia ugyanahhoz az Azure Active Directory-bérlőhöz.

## <a name="disable-ddos-for-a-virtual-network"></a>Egy virtuális hálózat DDoS letiltása

1. Adja meg a virtuális hálózat DDoS protection-szabvány esetében a letiltani kívánt nevét a **erőforrások, szolgáltatások és a docs mezőben keressen** a portál tetején. Amikor a virtuális hálózat neve megjelenik a keresési eredmények között, kattintson rá.
2. Válassza ki **a DDoS protection**alatt **beállítások**.
3. Válassza ki **alapszintű** alatt **DDoS protection-díjcsomag** majd **mentése**.

## <a name="work-with-ddos-protection-plans"></a>DDoS potection-tervek használata

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *DDoS* a a **szűrő** mezőbe. Amikor **DDoS potection-tervek** az eredmények jelennek meg, akkor jelölje be.
3. Válassza ki a védelmi terv meg szeretné tekinteni a listából.
4. A csomaghoz tartozó összes virtuális hálózatok jelennek meg.
5. Ha törölni szeretné a csomagot, a származó összes virtuális hálózatot először kell társítást. Leválasztja a virtuális hálózatról egy tervet, lásd: [DDoS tiltsa le a virtuális hálózat](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>A DDoS protection-metrikák riasztások konfigurálása

Választhat bármelyik DDoS protection elérhető metrikáinak riasztást küldjön, ha van egy aktív kockázatcsökkentést, a támadás során az Azure Monitor riasztási konfiguráció használatával. A feltételek teljesülnek, a megadott cím figyelmeztető e-mailt kapja:

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Amikor **figyelő** megjelenik az eredmények között, válassza ki.
3. Válassza ki **metrikák** alatt **megosztott szolgáltatások**.
4. Adja meg, vagy válassza ki a saját értékeket, vagy adja meg a következő példaértékeket, fogadja el a többi alapértelmezett beállítást, és válassza **OK**:

    |Beállítás                  |Érték                                                                                               |
    |---------                |---------                                                                                           |
    |Name (Név)                     | myDdosAlert                                                                                        |
    |Előfizetés             | Válassza ki az előfizetést, amely tartalmazza a nyilvános IP-címet szeretne riasztásokat kapni.        |
    |Erőforráscsoport           | Válassza ki az erőforráscsoportot, amely tartalmazza a nyilvános IP-címet szeretne riasztásokat kapni.      |
    |Erőforrás                 | Válassza ki a nyilvános IP-címet, amely tartalmazza a nyilvános IP-címet szeretne riasztásokat kapni. DDoS figyeli a virtuális hálózaton belüli erőforrásokhoz hozzárendelt nyilvános IP-címeket. Ha nyilvános IP-címekkel rendelkező erőforrásokat a virtuális hálózat nem rendelkezik, akkor először létre kell hoznia erőforrás nyilvános IP-címmel. Nyomon követheti a Resource Managerrel üzembe helyezett (nem klasszikus) szereplő összes erőforrás nyilvános IP-cím [virtuális hálózatok az Azure-szolgáltatások](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), kivéve az Azure App Service Environment-környezetek és az Azure VPN Gateway átjárón. Ez az oktatóanyag folytatásához, gyorsan létrehozhat egy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.                   |
    |Metrika                   | A DDoS elleni támadás vagy sem                                                                            |
    |Küszöbérték                | 1 – **1** azt jelenti, hogy a támadás alatt áll. **0** azt jelenti, hogy az Ön nem támadás alatt áll.                         |
    |Időszak                   | Válasszon bármilyen értéket választja.                                                                   |
    |Értesítés e-mailen keresztül         | Jelölje be a jelölőnégyzetet                                                                                  |
    |További rendszergazdai | Adja meg az e-mail-címét, ha még nem egy e-mailek tulajdonosi, közreműködői vagy olvasói az előfizetéshez. |

    Támadások észlelése, néhány percen belül kap egy e-mailt az Azure Monitor-metrikák, amely a következő képhez hasonlóan néz ki:

    ![A támadás riasztás](./media/manage-ddos-protection/ddos-alert.png)


A DDoS-támadás ellenőrzése a riasztás szimulálásához lásd: [ellenőrzése DDoS észlelési](#validate-ddos-detection).

Hogy még többet is megtudhat [webhookok konfigurálásáról](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [a logic apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) riasztások létrehozásához.

## <a name="use-ddos-protection-telemetry"></a>DDoS elleni védelem telemetriai használata

A támadás telemetriai adatokat valós időben az Azure Monitor keresztül biztosított. A telemetria csak arra az időtartamra, nyilvános IP-cím van megoldás alatt érhető el. Telemetria előtt vagy után a támadás nem jelenik meg.

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Amikor **figyelő** megjelenik az eredmények között, válassza ki.
3. Válassza ki **metrikák**alatt **megosztott szolgáltatások**.
4. Válassza ki a **előfizetés** és **erőforráscsoport** , amely telemetriai szánt nyilvános IP-cím szerepel.
5. Válassza ki **nyilvános IP-cím** a **erőforrástípus**, majd válassza ki a kívánt telemetriája adott nyilvános IP-cím.
6. Egy sorozatát **rendelkezésre álló metrikák** a képernyő bal oldalán jelennek meg. Ezek a metrikák kiválasztásakor vannak ábrázolt a a **Azure Monitor Metrikadiagram** az Áttekintés képernyő.

A metrikák nevei jelenthet különböző csomagtípusok és bájt csomagokat, és a egy alapvető szerkezet mindegyik metrikát a címke neve a következő:

- **Eldobott címkenév** (például **bejövő csomagok eldobott DDoS**): a DDoS protection rendszer eldobott/törlődik csomagok számát regisztrálja.
- **Továbbított címkenév** (például **bejövő továbbított csomagok DDoS**): a cél virtuális IP-CÍMEK – nem szűrt forgalmat a DDoS-rendszer által továbbított csomagok számát regisztrálja.
- **Nincs címke neve** (például **bejövő csomagok DDoS**): a tisztítási rendszerbe – a csomagok összege jelölő kapott csomagok száma dobni, és továbbítva.

Telemetriai adatok érvényesítéséhez DDoS-támadás szimulálása, lásd: [ellenőrzése DDoS észlelési](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>A DDoS-kockázatcsökkentést szabályzatok megtekintéséhez

A DDoS Protection Standard érvényes három kockázatcsökkentési automatikusan beállított szabályzat (TCP külön, a TCP és UDP) minden egyes nyilvános IP-cím, a védett erőforrások, a virtuális hálózat, amelyen engedélyezve van a DDoS. Kiválasztásával megtekintheti a szabályzat küszöbértékeket a **bejövő TCP-csomagok DDoS elleni védelem aktiválásához** és **bejövő UDP-csomagok DDoS elleni védelem aktiválásához** metrikák, a következő képen látható módon:

![Kockázatcsökkentési szabályzatok megtekintése](./media/manage-ddos-protection/view-mitigation-policies.png)

Házirend küszöbértékek, automatikusan konfigurált az Azure machine learning-alapú hálózati forgalom profilkészítés keresztül. Csak akkor, ha a házirend-küszöbértékének DDoS-támadás kockázatcsökkentése bekövetkezik az IP-cím támadás alatt áll.

## <a name="configure-ddos-attack-analytics"></a>A DDoS-támadás analytics konfigurálása
Standard szintű Azure DDoS Protection a támadás részletes elemzéseket és DDoS-támadás Analytics vizualizációs biztosít. A virtuális hálózat DDoS-támadásokkal szembeni védelme ügyfelek részletesen támadási forgalom és a támadás kockázatcsökkentési jelentések és kockázatcsökkentési Folyamatnaplók a támadás kockázatainak csökkentésére végrehajtott műveletek. 

## <a name="configure-ddos-attack-mitigation-reports"></a>DDoS elleni támadások kockázatait jelentések konfigurálása
A támadás kockázatcsökkentési jelentések összesített értéket jelenít meg az erőforráson a támadással kapcsolatos részletes információkat, amelyek Netflow protokoll adatokat használja. Visszaállít egy nyilvános IP-erőforrásból támadás alatt áll, a jelentéskészítés indul el, amint a kockázatcsökkentési elindul. Nem lesznek egy növekményes jelentést hoz létre 5 percenként és a egy utáni kockázatcsökkentési jelentés a teljes kockázatcsökkentés időszakra vonatkozóan. Ez a ellenőrizze, hogy egy esemény, a DDoS-támadás továbbra is hosszabb időtartamra, megtekintheti a legfrissebb pillanatkép kockázatcsökkentési jelentés 5 percenként fogja és egyszer a elleni támadások kockázatait teljes összefoglaló keresztül. 

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Amikor **figyelő** megjelenik az eredmények között, válassza ki.
3. A **beállítások**válassza **diagnosztikai beállítások**.
4. Válassza ki a **előfizetés** és **erőforráscsoport** , amelyek tartalmazzák a naplózni kívánt nyilvános IP-címet.
5. Válassza ki **nyilvános IP-cím** a **erőforrástípus**, majd válassza ki az adott nyilvános IP-cím metrikáinak bejelentkezik.
6. Válassza ki **engedélyezze a diagnosztikát a DDoSMitigationReports napló gyűjtése** majd válassza az alábbi lehetőségek közül, amennyi szükséges:

    - **Archiválás tárfiókba**: adatok írása egy Azure Storage-fiókot. Ezzel a beállítással kapcsolatos további információkért lásd: [diagnosztikai naplói archiválása](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Az eseményközpontok felé Stream**: lehetővé teszi a naplók használatával egy Azure Eseményközponthoz csomópontmetrikák log fogadó. Az Event hubs integrációjának engedélyezése az Splunk vagy más SIEM-rendszerekkel. Ezzel a beállítással kapcsolatos további információkért lásd: [diagnosztikai naplók egy eseményközpontba Stream](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Küldés a Log Analyticsnek**: írja a naplókat az Azure OMS Log Analytics szolgáltatásba. Ezzel a beállítással kapcsolatos további információkért lásd: [használata a Log Analytics naplóinak összegyűjtése](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Mindkét a növekményes és a támadás utáni kockázatcsökkentési a jelentések a következő mezőket tartalmazzák
- Támadási vektorok
- Forgalom statisztika
- Az eldobott csomagok oka
- Protokollok
- Az első 10 forrás országok vagy régiók
- Első 10 forrás ASN-EK

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS elleni támadások kockázatait Folyamatnaplók konfigurálása
Támadás kockázatcsökkentési Flow naplók lehetővé teszi, hogy tekintse át az eldobott forgalomhoz továbbított forgalom és az egyéb érdekes esetén a közel valós idejű aktív DDoS elleni támadás során. Tölti be a konstans streamet az adatok közel valós idejű figyelés eseményközponton keresztül a SIEM rendszerekhez, lehetséges műveleteket, és oldja meg a defense műveletek szükségességét. 

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Amikor **figyelő** megjelenik az eredmények között, válassza ki.
3. A **beállítások**válassza **diagnosztikai beállítások**.
4. Válassza ki a **előfizetés** és **erőforráscsoport** , amelyek tartalmazzák a naplózni kívánt nyilvános IP-címet.
5. Válassza ki **nyilvános IP-cím** a **erőforrástípus**, majd válassza ki az adott nyilvános IP-cím metrikáinak bejelentkezik.
6. Válassza ki **engedélyezze a diagnosztikát a DDoSMitigationFlowLogs napló gyűjtése** majd válassza az alábbi lehetőségek közül, amennyi szükséges:

    - **Archiválás tárfiókba**: adatok írása egy Azure Storage-fiókot. Ezzel a beállítással kapcsolatos további információkért lásd: [diagnosztikai naplói archiválása](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Az eseményközpontok felé Stream**: lehetővé teszi a naplók használatával egy Azure Eseményközponthoz csomópontmetrikák log fogadó. Az Event hubs integrációjának engedélyezése az Splunk vagy más SIEM-rendszerekkel. Ezzel a beállítással kapcsolatos további információkért lásd: [diagnosztikai naplók egy eseményközpontba Stream](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Küldés a Log Analyticsnek**: írja a naplókat az Azure OMS Log Analytics szolgáltatásba. Ezzel a beállítással kapcsolatos további információkért lásd: [használata a Log Analytics naplóinak összegyűjtése](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
7. Az Azure analytics irányítópulton tekintheti meg a forgalmi naplók adatai, importálhatja a minta-irányítópult https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Forgalmi naplók fog rendelkezni a következő mezőket: 
- Forrás IP-címe
- Cél IP-címe
- Forrásport 
- Célport 
- Protokoll típusa 
- Kockázatcsökkentési során végrehajtott műveletek



## <a name="validate-ddos-detection"></a>A DDoS-észlelési ellenőrzése

A Microsoft közzétegyék az általuk [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felületet, ahol a DDoS Protection-kompatibilis nyilvános IP-címek szimulációkhoz forgalmát hozhat létre. A töréspont felhőalapú szimuláció lehetővé teszi:

- Ellenőrizze, hogy hogyan a Microsoft Azure DDoS Protection megvédi a DDoS-támadások az Azure-erőforrások
- Az incidensmegoldási folyamatba, a DDoS-támadások optimalizálása
- A dokumentum a DDoS-megfelelőség
- A hálózati biztonsági csoportok betanítása

## <a name="permissions"></a>Engedélyek

DDoS potection-tervek dolgozni, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör és a egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                            | Name (Név)                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Olvassa el a DDoS protection-díjcsomag              |
| Microsoft.Network/ddosProtectionPlans/write       | Létrehozni vagy frissíteni a DDoS protection-díjcsomag  |
| Microsoft.Network/ddosProtectionPlans/delete      | A DDoS protection-díjcsomag törlése            |
| Microsoft.Network/ddosProtectionPlans/join/action | Csatlakozzon a DDoS protection-díjcsomag              |

Ahhoz, hogy egy virtuális hálózat DDoS protection, a fióknak is rendelkeznie kell a megfelelő [műveletek a virtuális hálózatokban](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>További lépések

- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok