---
title: Kezelése Azure DDoS védelem szabványos az Azure portál használatával |} Microsoft Docs
description: 'Útmutató: Azure figyelő Azure DDoS védelem szabványos telemetria segítségével a támadás elhárítása érdekében.'
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
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Kezelése Azure DDoS védelem szabványos az Azure portál használatával

Megtudhatja, hogyan engedélyezése és elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás védelem letiltásához, és telemetriai adatokat a Azure DDoS védelem standard DDoS-támadások mérséklése. DDoS védelem szabványos védelmet nyújt az Azure erőforrások, például a virtuális gépek terheléselosztók és alkalmazásátjárót, amelyek egy Azure [nyilvános IP-cím](virtual-network-public-ip-address.md) rendelve. DDoS védelem szabványos és platformképességei kapcsolatos további információkért lásd: [DDoS védelem szabványos áttekintése](ddos-protection-overview.md).

Az oktatóanyag befejezése bármely lépések, mielőtt jelentkezzen be az Azure portálon, a https://portal.azure.com rendelt fiókkal a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely hozzá van rendelve a megfelelő a felsorolt műveleteket [engedélyek](#permissions).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-ddos-protection-plan"></a>DDoS védelem terv létrehozása

DDoS védelmi tervet DDoS védelem standard előfizetésekhez engedélyezett virtuális hálózatokon álló készletet határoz meg. Beállíthatja, hogy a szervezet és a hivatkozás virtuális hálózatokat több előfizetéssel ugyanahhoz a csomaghoz egy DDoS védelmi tervet. Magát a DDoS védelem tervet is, a terv létrehozása során kiválasztott előfizetés társítva. Az előfizetés a terv társítva azt eredményezi azok háromszorosa a havi ismétlődő számlázási a terv, valamint a keretét díjak, az abban az esetben, ha a védett nyilvános IP-címek száma meghaladja a 100. DDoS árakkal kapcsolatos további információkért lásd: [díjszabása](https://azure.microsoft.com/pricing/details/ddos-protection/).

Egynél több terv létrehozását nincs szükség a legtöbb szervezet számára. A terv nem helyezhető át, előfizetések között. Ha az előfizetés, a terv van módosítani szeretné, hogy [törölje a meglévő csomag](#work-with-ddos-protection-plans) és hozzon létre egy újat.

1. Válassza ki **hozzon létre egy erőforrást** az Azure portál bal felső sarokban.
2. Keresse meg *DDoS*. Ha **DDos védelem terv** megjelenik a keresési eredmények között, jelölje be.
3. Kattintson a **Létrehozás** gombra.
4. Adja meg vagy válassza ki a saját értékeket, vagy adja meg, vagy válassza ki a következő példában szereplő értékeket, és válassza **létrehozása**:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Name (Név)           | myDdosProtectionPlan                              |
    |Előfizetés   | Válassza ki előfizetését.                         |
    |Erőforráscsoport | Válassza ki **hozzon létre új** , és írja be *myResourceGroup* |
    |Hely       | USA keleti régiója                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Új virtuális hálózat DDoS engedélyezése

1. Válassza ki **hozzon létre egy erőforrást** az Azure portál bal felső sarokban.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki a saját értékeit, adjon meg vagy válassza ki a következő példában szereplő értékeket, fogadja el a többi alapértelmezett értéket, és válassza **létrehozása**:

    | Beállítás         | Érték                                                        |
    | ---------       | ---------                                                    |
    | Name (Név)            | myVirtualNetwork                                             |
    | Előfizetés    | Válassza ki előfizetését.                                    |
    | Erőforráscsoport  | Válassza ki **meglévő**, majd válassza ki **myResourceGroup** |
    | Hely        | USA keleti régiója                                                      |
    | DDos-védelem | Válassza ki **szabványos** majd a **DDoS-védelem**, jelölje be **myDdosProtectionPlan**. A terv, akkor válassza ki az ugyanazon vagy másik előfizetést, mint a virtuális hálózati lehetnek, de mindkét előfizetéshez hozzá kell rendelni a azonos Azure Active Directory-bérlő.|

Virtuális hálózat nem helyezhető át másik erőforráscsoportba vagy előfizetésbe, ha DDoS Standard engedélyezve van a virtuális hálózat. Ha át kell helyeznie egy virtuális hálózat DDoS Standard engedélyezve van, először tiltsa le a DDoS Standard, helyezze át a virtuális hálózati és engedélyezze a DDoS standard. Az áthelyezés után minden védett nyilvános IP-címek a virtuális hálózati házirend automatikusan beállított küszöbértékeit állnak vissza.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Meglévő virtuális hálózat DDoS engedélyezése

1. DDoS védelem terv létrehozása a lépések végrehajtásával [DDoS védelem terv létrehozása](#create-a-ddos-protection-plan), ha még nem rendelkezik egy meglévő DDoS védelmi tervet.
2. Válassza ki **hozzon létre egy erőforrást** az Azure portál bal felső sarokban.
3. Adja meg a nevét, amely engedélyezi a DDoS védelem szabványt állít fel a virtuális hálózat a **keresést az erőforrások, a szolgáltatások és a dokumentumok mezőben** a portál felső. Ha a keresési eredmények között megjelenik a virtuális hálózat nevét, válassza ki azt.
4. Válassza ki **DDoS-védelem**a **beállítások**.
5. Válassza ki **szabványos**. A **DDoS védelem terv**, egy meglévő DDoS védelem, vagy a tervet az 1. lépésben létrehozott, majd válassza ki és **mentése**. A terv, akkor válassza ki az ugyanazon vagy másik előfizetést, mint a virtuális hálózati lehetnek, de mindkét előfizetéshez hozzá kell rendelni a azonos Azure Active Directory-bérlő.

## <a name="disable-ddos-for-a-virtual-network"></a>DDoS letiltása egy virtuális hálózat

1. Adja meg a virtuális hálózat DDoS védelem szabvány a letiltani kívánt nevét a **keresést az erőforrások, a szolgáltatások és a dokumentumok mezőben** a portál felső. Ha a keresési eredmények között megjelenik a virtuális hálózat nevét, válassza ki azt.
2. Válassza ki **DDoS-védelem**a **beállítások**.
3. Válassza ki **alapvető** alatt **DDoS védelem terv** , és válassza **mentése**.

## <a name="work-with-ddos-protection-plans"></a>A DDoS védelem tervek

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *DDoS* a a **szűrő** mezőbe. Ha **DDoS védelem tervek** jelennek meg az eredményeket, válassza ki azt.
3. Válassza ki a védelmi terv meg szeretné tekinteni a listából.
4. A csomaggal társított összes virtuális hálózatok vannak felsorolva.
5. Ha törölni szeretné a tervet, először kell megszünteti az összes virtuális hálózatokat. Leválasztja a virtuális hálózati terv, lásd: [DDoS tiltsa le a virtuális hálózat](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS védelem metrikák riasztások konfigurálása

Kiválaszthatja a rendelkezésre álló DDoS védelem mérni kívánt riasztást, ha van egy aktív megoldás, a támadás során bármelyikét Azure figyelő riasztási konfiguráció használatával. A feltételek teljesülnek, a megadott cím figyelmeztető e-mailt kapja:

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Ha **figyelő** megjelenik az eredmények között, jelölje be.
3. Válassza ki **metrikák** alatt **közös szolgáltatások**.
4. Adja meg, vagy válassza ki a saját értékeket, vagy adja meg a következő példában szereplő értékeket, fogadja el a többi alapértelmezett értéket, és válassza **OK**:

    |Beállítás                  |Érték                                                                                               |
    |---------                |---------                                                                                           |
    |Name (Név)                     | myDdosAlert                                                                                        |
    |Előfizetés             | Válassza ki az előfizetést, amely tartalmazza a nyilvános IP-címet szeretne riasztásokat kapni.        |
    |Erőforráscsoport           | Válassza ki az erőforráscsoportot, amely tartalmazza a nyilvános IP-címet szeretne riasztásokat kapni.      |
    |Erőforrás                 | Válassza ki a nyilvános IP-címet, amely tartalmazza a nyilvános IP-címet szeretne riasztásokat kapni. DDoS nyilvános IP-címek a virtuális hálózatban lévő erőforrásokhoz hozzárendelt figyeli. Ha a virtuális hálózat nem rendelkezik nyilvános IP-címekkel rendelkező erőforrásokat, akkor először létre kell hoznia egy erőforrást egy nyilvános IP-címmel. A nyilvános IP-címe Manager használatával telepített erőforrás (klasszikus) felsorolt összes erőforrást is figyelheti [virtuális hálózatot az Azure szolgáltatások](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), kivéve az Azure App Service Environment-környezetek és az Azure VPN Gateway. Ez az oktatóanyag folytatásához is gyorsan létrehozhat egy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.                   |
    |Metrika                   | DDoS alatt támadásokkal vagy sem                                                                            |
    |Küszöbérték                | 1 - **1** azt jelenti, hogy támadás alatt áll. **0** azt jelenti, hogy nem támadás alatt áll.                         |
    |Időszak                   | Válassza ki a bármilyen értéket választja.                                                                   |
    |E-mailben értesíteni         | Jelölje be a jelölőnégyzetet                                                                                  |
    |További rendszergazda | Adja meg az e-mail címet, ha még nem egy e-mailek tulajdonos, közreműködő vagy olvasó az előfizetés. |

    Észlelt támadás, néhány percen belül kap egy e-mailt az alábbi képen hasonló Azure figyelő metrikák:

    ![A támadás riasztás](./media/manage-ddos-protection/ddos-alert.png)


A riasztási egy DDoS a támadás szimulálása, lásd: [érvényesítése DDoS észlelési](#validate-ddos-detection).

Még többet is megtudhat arról [konfigurálása webhookokkal](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [a logic apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) riasztások létrehozásához.

## <a name="configure-logging-for-ddos-protection-metrics"></a>DDoS védelem metrikáihoz naplózásának konfigurálása

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Ha **figyelő** megjelenik az eredmények között, jelölje be.
3. A **beállítások**, jelölje be **diagnosztikai beállítások**.
4. Válassza ki a **előfizetés** és **erőforráscsoport** , amelyik tartalmazza a nyilvános IP-cím bejelentkezik.
5. Válassza ki **nyilvános IP-cím** a **erőforrástípus**, majd válassza ki az adott nyilvános IP-cím metrikáját bejelentkezik.
6. Válassza ki **kapcsolja be a következő adatok gyűjtéséhez diagnosztika** majd válassza ki az alábbi lehetőségek közül a összes kívánt beállítást:

    - **Archív tárfiókba**: adatot ír egy Azure Storage-fiókot. Ez a beállítás kapcsolatos további információkért lásd: [diagnosztikai naplók archiválása](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Az eseményközpontok felé adatfolyam**: lehetővé teszi, hogy a napló fogadó egy Azure Eseményközponthoz használatával a naplókat átvételéhez. Az Event hubs integrációjának engedélyezése az Splunk vagy más SIEM-rendszerekről. Ez a beállítás kapcsolatos további információkért lásd: [adatfolyamként küldje el az eseményközpontba diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Naplóelemzési küldése**: naplók ír az Azure MOBILE Naplóelemzés szolgáltatás. Ez a beállítás kapcsolatos további információkért lásd: [Naplógyűjtéshez Naplóelemzési használható](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ezzel szimulálva a DDoS támadás naplózási érvényesítéséhez, lásd: [érvényesítése DDoS észlelési](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>DDoS-védelem telemetriai adatok használata

A támadás telemetriai valós idejű figyelése Azure keresztül valósul meg. A telemetriai adatok csak a megoldás alatt álló egy nyilvános IP-cím időtartamára érhető el. Telemetria előtt vagy után a támadás elhárítására nem látható.

1. Válassza ki **minden szolgáltatás** a portál bal felső,.
2. Adja meg *figyelő* a a **szűrő** mezőbe. Ha **figyelő** megjelenik az eredmények között, jelölje be.
3. Válassza ki **metrikák**a **közös szolgáltatások**.
4. Válassza ki a **előfizetés** és **erőforráscsoport** , amelyik tartalmazza a nyilvános IP-cím, amelyet a telemetriai adatokat.
5. Válassza ki **nyilvános IP-cím** a **erőforrástípus**, majd válassza ki a kívánt telemetriai adat adott nyilvános IP-cím.
6. Több **elérhető** a képernyő bal oldalán jelennek meg. A metrikák kiválasztásakor van grafikon a a **Azure figyelő metrikák diagram** áttekintése képernyőn.

A mérték nevének jelenlegi különböző csomagtípusok és bájt csomagok, és mindegyik metrikát a címkenevek az alapvető szerkezet az alábbiak szerint:

- **Eldobott címkenév** (például **bejövő eldobott csomagok DDoS**): a DDoS védelem rendszer által eldobott/törlődik csomagok száma.
- **Továbbított címkenév** (például **bejövő továbbított csomagok DDoS**): a DDoS rendszer, hogy a cél VIP – nem szűrt forgalmat továbbított csomagok száma.
- **Nincs címke neve** (például **bejövő csomagok DDoS**): a háttértisztítás rendszerbe – a csomagok összege képviselő kapott csomagok száma dobva, és továbbítja.

Ezzel szimulálva a DDoS támadás telemetriai adatainak érvényesítéséhez, lásd: [érvényesítése DDoS észlelési](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>DDoS megoldás házirendek megtekintése

DDoS védelem szabványos három automatikusan beállított megoldás házirendet alkalmaz (TCP SZIN, TCP és UDP) minden egyes nyilvános IP-cím a védett erőforrás DDoS engedélyezve van a virtuális hálózatban. Kiválasztásával megtekintheti a házirend küszöbértékeket a **bejövő TCP-csomagokat való DDoS megoldás** és **bejövő UDP-csomagok való DDoS megoldás** metrika, az alábbi ábrán látható módon:

![Megoldás irányelvek megtekintése](./media/manage-ddos-protection/view-mitigation-policies.png)

Házirend küszöbértékek, automatikusan konfigurált Azure machine learning-alapú hálózati forgalom profilkészítési keresztül. Csak akkor, ha a házirend küszöbértéket megsérül nem DDoS megoldás fordul elő, az IP-címhez támadás alatt áll.

## <a name="validate-ddos-detection"></a>DDoS észlelési ellenőrzése

Microsoft rendelkezik közösen rendelkező [BreakingPoint felhő](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felület, ahol hozhat létre a forgalom DDoS-védelem engedélyezve nyilvános IP-címek szimulációja ellen. A töréspont felhő szimuláció teszi lehetővé:

- Ellenőrizze, hogy hogyan Microsoft Azure DDoS-védelem védelmet nyújt az Azure-erőforrások DDoS-támadások
- Az incidensekre adott reakciók folyamat DDoS támadás optimalizálása
- A dokumentum DDoS megfelelőségi
- A hálózati biztonsági csoportok képzése

## <a name="permissions"></a>Engedélyek

DDoS védelem csomagok használatához a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                            | Name (Név)                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Olvassa el a védelmi tervet DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Hozzon létre vagy DDoS-védelem csomag frissítése  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS-védelem csomag törlése            |
| Microsoft.Network/ddosProtectionPlans/join/action | Csatlakozás egy DDoS védelmi tervet.              |

Ahhoz, hogy a virtuális hálózat DDoS-védelem, a fiók kell rendelni a megfelelő [műveleteket a virtuális hálózatok](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>További lépések

- Létrehozása és alkalmazása [Azure házirend](policy-samples.md) virtuális hálózatok