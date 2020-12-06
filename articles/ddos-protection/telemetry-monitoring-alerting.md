---
title: DDoS Protection-telemetria megtekintése és konfigurálása
description: Megtudhatja, hogyan tekintheti meg és konfigurálhatja a DDoS Protection telemetria.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 834339a20e369b3835faf05d069f8d4f77385e18
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744859"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS Protection-telemetria megtekintése és konfigurálása

Azure DDoS Protection a standard részletes támadási elemzéseket és vizualizációkat biztosít a DDoS Attack Analytics használatával. A DDoS-támadásokkal szemben a virtuális hálózatokat védő ügyfelek részletesen megtekinthetik a támadási forgalmat, valamint a támadás enyhítésére tett lépéseket a támadásokkal kapcsolatos kockázatcsökkentő jelentésekben & a kockázatcsökkentő folyamat naplófájljait. A gazdag telemetria Azure Monitoron keresztül érhető el, beleértve a DDoS-támadások időtartama alatt részletes mérőszámokat is. A riasztás konfigurálható a DDoS Protection által elérhetővé tett Azure Monitor metrikák bármelyikéhez. A naplózás az [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), a splunk (Azure Event Hubs), a OMS log Analytics és az Azure Storage szolgáltatással is integrálható a Azure monitor diagnosztikai felületen keresztül haladó elemzésekhez.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Riasztások konfigurálása DDoS Protection-mérőszámokhoz
> * A DDoS Protection telemetria használata
> * DDoS-mérséklési szabályzatok megtekintése
> * DDoS Protection-riasztások megtekintése Azure Security Center

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Riasztások konfigurálása DDoS Protection-mérőszámokhoz

A rendelkezésre álló DDoS-védelmi mérőszámok bármelyikét kiválaszthatja, ha riasztást szeretne kapni, ha a támadás során aktív megoldás van a Azure Monitor riasztás konfigurációjának használatával. Ha a feltételek teljesülnek, a megadott cím riasztási e-mailt kap:

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. Válassza a **metrikák** lehetőséget a **megosztott szolgáltatások** területen.
4. Adja meg vagy válassza ki a saját értékeit, vagy adja meg a következő példákat, fogadja el a fennmaradó alapértelmezett értékeket, majd kattintson az **OK gombra**:

    |Beállítás                  |Érték                                                                                               |
    |---------                |---------                                                                                           |
    |Név                     | Adja meg a _MyDdosAlert_.                                                                                |
    |Előfizetés             | Válassza ki azt az előfizetést, amely a nyilvános IP-címet tartalmazza, amelyhez riasztásokat szeretne kapni.        |
    |Erőforráscsoport           | Válassza ki azt az erőforráscsoportot, amely tartalmazza azt a nyilvános IP-címet, amelyhez riasztásokat szeretne kapni.      |
    |Erőforrás                 | Válassza ki azt a nyilvános IP-címet, amely tartalmazza azt a nyilvános IP-címet, amelyhez riasztásokat szeretne kapni. A DDoS figyeli a virtuális hálózaton belüli erőforrásokhoz rendelt nyilvános IP-címeket. Ha nem rendelkezik nyilvános IP-címmel rendelkező erőforrásokkal a virtuális hálózaton, először létre kell hoznia egy nyilvános IP-címmel rendelkező erőforrást. Az [Azure-szolgáltatások virtuális hálózata](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (beleértve az Azure Load balancert, ahol a háttérbeli virtuális gépek a virtuális hálózaton vannak), a Resource Managerrel telepített összes erőforrás nyilvános IP-címét nyomon követheti, kivéve Azure app Service környezetekben és az Azure VPN Gateway. Az oktatóanyag folytatásához gyorsan létrehozhat egy Windows vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.                   |
    |Metrika                   | Válassza **a DDoS Attack vagy a not (nem**) lehetőséget.                                                                |
    |Küszöbérték                | 1 – **1** azt jelenti, hogy támadás alatt áll. **0** azt jelenti, hogy nem támadás alatt áll.                         |
    |Időszak                   | Válassza ki a választott értéket.                                                                   |
    |Értesítés e-mailben         | Jelölje be a jelölőnégyzetet.                                                                                 |
    |További rendszergazda | Adja meg az e-mail-címét, ha Ön nem e-mail-tulajdonos, közreműködő vagy olvasó az előfizetéshez. |

    A támadások észlelése néhány percen belül egy e-mailt kap Azure Monitor metrikától, amely az alábbi képhez hasonlóan néz ki:

    ![Támadási riasztás](./media/manage-ddos-protection/ddos-alert.png)


A DDOS-támadások szimulálása a riasztás ellenőrzéséhez lásd: a [DDoS-észlelés ellenőrzése](test-through-simulations.md).

További információ: [webhookok](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [logikai alkalmazások](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konfigurálása riasztások létrehozásához.

## <a name="use-ddos-protection-telemetry"></a>A DDoS Protection telemetria használata

A támadás telemetria valós időben Azure Monitor biztosítjuk. A telemetria csak arra az időtartamra érhető el, ameddig a nyilvános IP-cím mérséklés alatt áll. A telemetria nem jelenik meg a támadás előtt vagy után.

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. Válassza a **metrikák** lehetőséget a **megosztott szolgáltatások** területen.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a telemetria kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt a megadott nyilvános IP-címet, amelyet telemetria szeretne.
6. A képernyő bal oldalán a **rendelkezésre álló metrikák** egy sorozata jelenik meg. Ezek a metrikák, ha ki vannak jelölve, az áttekintő képernyő **Azure monitor metrikák diagramján** vannak ábrázolva.
7. Válassza ki az **összesítési** típust **Max** .

A metrikák nevei különböző típusú csomagokat és bájtokat, illetve csomagokat, a címkék neveinek alapszintű összeállításával az egyes mérőszámokban az alábbiak szerint:

- **Eldobott címke neve** (például a **bejövő csomagok eldobott DDoS**): a DDoS Protection rendszer által eldobott/kihagyott csomagok száma.
- **Továbbított címke neve** (például **bejövő csomagok továbbított DDoS**): a DDoS rendszer által továbbított csomagok száma a célként megadott VIP – a nem szűrt forgalom.
- **Nincs címke neve** (például a **bejövő csomagok DDoS**): a kimosó rendszerbe érkezett csomagok teljes száma, amely az eldobott és továbbított csomagok összegét jelöli.

Ez a [Azure Monitor riasztási szabály](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) egy egyszerű lekérdezést futtat, amely azt vizsgálja, hogy mikor történt aktív DDoS-csökkentés. A DDOS-támadások szimulálása a telemetria ellenőrzéséhez: a [DDoS-észlelés ellenőrzése](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>DDoS-mérséklési szabályzatok megtekintése

DDoS Protection a standard három automatikusan hangolt kockázatcsökkentő házirendet alkalmaz (TCP SYN, TCP & UDP) a védett erőforrás minden nyilvános IP-címéhez, a DDoS-t engedélyező virtuális hálózatban. A házirend küszöbértékeit úgy tekintheti meg, ha kiválasztja a **Bejövő TCP-csomagokat a DDoS-mérséklés** **és a** bejövő UDP-csomagok kiválasztásával, hogy a **DDoS-mérséklési** metrikákat "Max" értékkel aktiválja, ahogy az alábbi képen látható:

![Kockázatcsökkentő házirendek megtekintése](./media/manage-ddos-protection/view-mitigation-policies.png)

A házirend-küszöbértékek automatikusan konfigurálva vannak az Azure Machine learning-alapú hálózati forgalom profilkészítése révén. A támadási IP-cím esetében csak a házirend küszöbértékének megszegése esetén a DDoS-elhárítás történik.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>DDoS Protection-riasztások megtekintése Azure Security Center

Azure Security Center a [biztonsági riasztások](../security-center/security-center-managing-and-responding-alerts.md)listáját tartalmazza, és információt nyújt a problémák kivizsgálásához és javításához. Ezzel a funkcióval egységes nézetet kap a riasztásokról, többek között a DDoS-támadásokkal kapcsolatos riasztásokról, valamint a támadásoknak a közeljövőben történő enyhítésére tett műveletekről.
A DDoS-támadások észlelésének és enyhítésének két konkrét riasztása jelenik meg:

- **DDoS-támadás észlelhető a nyilvános IP-címek esetében**: Ez a riasztás akkor jön létre, ha a DDoS Protection szolgáltatás észleli, hogy az egyik nyilvános IP-cím a DDoS-támadás célpontja.
- **DDoS-támadás a nyilvános IP-címek esetében**: Ez a riasztás akkor jön létre, ha a nyilvános IP-cím támadása le van tiltva.
A riasztások megtekintéséhez nyissa meg **Security Center** a Azure Portalban. A **veszélyforrások védelme** területen válassza a **biztonsági riasztások** lehetőséget. Az alábbi képernyőképen egy példa látható a DDoS-támadások riasztására.

![DDoS-riasztás a Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

A riasztások a támadás, a Geo és a veszélyforrások felderítésére vonatkozó információk, valamint a Szervizelési lépések általános információit tartalmazzák a nyilvános IP-címekről.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

- Riasztások konfigurálása DDoS Protection-mérőszámokhoz
- A DDoS Protection telemetria használata
- DDoS-mérséklési szabályzatok megtekintése
- DDoS Protection-riasztások megtekintése Azure Security Center

Ha meg szeretné tudni, hogyan konfigurálhatja a támadás-elhárítási jelentéseket és a folyamat naplóit, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [DDoS-támadások mérséklési jelentéseinek és forgalomnaplóinak konfigurálása](reports-and-flow-logs.md)