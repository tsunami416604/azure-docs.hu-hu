---
title: Függőségi vizualizáció a Azure Migrateban | Microsoft Docs
description: Áttekintést nyújt az értékelési számításokról a Azure Migrate Server Assessment Service-ben
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 33594e09778b9a629645e12357e6bafe561ad35e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202914"
---
# <a name="dependency-visualization"></a>Függőségek képi megjelenítése

Azure Migrate: A kiszolgáló értékelése kiértékeli a helyszíni gépek csoportjait az Azure-ba való áttelepítéshez. Csoportok létrehozásához használhatja a kiszolgáló értékelése függőségi vizualizáció funkcióját. Ez a cikk a szolgáltatással kapcsolatos információkat tartalmaz.

> [!NOTE]
> A függőségi vizualizáció funkció Azure Governmentban nem érhető el.

## <a name="overview"></a>Áttekintés

A kiszolgáló értékelése során a függőségi vizualizáció lehetővé teszi, hogy nagy megbízhatóságú csoportokat hozzon létre az áttelepítési felmérésekhez. A függőségi vizualizáció használatával megtekintheti a gépek hálózati függőségeit, és azonosíthatja azokat a kapcsolódó gépeket, amelyeket az Azure-ba kell migrálni. Ez a funkció olyan esetekben hasznos, amikor nem teljesen ismeri az alkalmazást alkotó gépeket, és az Azure-ba kell migrálni.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Kiszolgáló-értékelési eszköz.
- Győződjön meg arról, hogy felderítette a gépeket a Azure Migrate; Ezt úgy teheti meg, hogy a [VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md)Azure Migrate berendezését állítja be. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld a Azure Migratenak: Server Assessment. [További információk](migrate-appliance.md).

## <a name="how-does-it-work"></a>Hogyan működik?

A Azure Migrate a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldást használja [Azure monitor naplókban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.
- A függőségi vizualizáció kihasználása érdekében hozzá kell rendelnie egy Log Analytics munkaterületet, amely új vagy meglévő, egy Azure Migrate projekttel.
- Csak abban az előfizetésben hozhat létre vagy csatolhat munkaterületet, ahol a Azure Migrate projektet létrehozták.
- Log Analytics munkaterület csatolása egy projekthez:
    1. A **kiszolgálók** lap **Azure Migratejában: Kiszolgáló-** értékelési csempe, kattintson az **Áttekintés**elemre.
    2. Az **Áttekintés**területen kattintson a lefelé mutató nyílra az **alapvető**erőforrások kibontásához.
    3. A **OMS**munkaterületen kattintson a **Konfigurálás szükséges**elemre.
    4. A **munkaterület konfigurálása**lapon adja meg, hogy új munkaterületet kíván-e létrehozni, vagy egy meglévőt szeretne használni:
    
    ![Munkaterület hozzáadása](./media/how-to-create-group-machine-dependencies/workspace.png)

- A munkaterület társítása során új munkaterületet kell létrehoznia, vagy egy meglévőt csatlakoztatni:
  - Amikor új munkaterületet hoz létre, meg kell adnia a munkaterület nevét. Kiválaszthatja azt a [régiót](https://azure.microsoft.com/global-infrastructure/regions/) , amelyben a munkaterület létre lesz hozva.
  - Ha meglévő munkaterületet csatol, az összes elérhető munkaterületet az áttelepítési projekttel megegyező előfizetésben is kiválaszthatja. Vegye figyelembe, hogy csak azok a munkaterületek vannak felsorolva, amelyek olyan régióban lettek létrehozva, ahol a [Service Map támogatott](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). A munkaterület csatolásához győződjön meg arról, hogy van "olvasó" hozzáférése a munkaterülethez.

  > [!NOTE]
  > Miután csatolt egy munkaterületet egy projekthez, később nem módosítható.

  > [!NOTE]
  > Azure Migrate jelenleg támogatja a OMS-munkaterület létrehozását az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában. Ha a munkaterületet a Azure Migrateon kívül más régióban hozza létre, akkor jelenleg nem rendelhető hozzá Azure Migrate projekthez. 

- A társított munkaterület a kulcs áttelepítési **projektje**és az érték **projekt neve**alapján van címkézve, amely a Azure Portal való kereséshez használható.
- A projekthez társított munkaterületre való belépéshez nyissa meg a projekt áttekintő oldalának **Essentials (alapvető** ) szakaszát, és nyissa meg a munkaterületet

    ![Log Analytics munkaterület navigálása](./media/concepts-dependency-visualization/oms-workspace.png)

A függőségek vizualizációjának használatához ügynököket kell letöltenie és telepítenie minden olyan helyszíni gépre, amelyet elemezni szeretne.  

- A [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) szolgáltatást minden gépre telepíteni kell. [További](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) információ az MMA-ügynök telepítéséről.
- A [függőségi ügynököt](../azure-monitor/platform/agents-overview.md#dependency-agent) minden gépen telepíteni kell. [További](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) információ a függőségi ügynök telepítéséről.
- Továbbá ha olyan gépekkel rendelkezik, amelyeken nincs internetkapcsolat, ezekre le kell töltenie és telepítenie kell a Log Analytics-átjárót.

Nincs szüksége ezekre az ügynökökre azon gépeken, amelyeket fel szeretne mérni, hacsak nem használ függőségi vizualizációt.

## <a name="do-i-need-to-pay-for-it"></a>Kell fizetnem?

A függőség vizualizációs funkciója díjmentesen elérhető. A kiszolgálók értékelése során a függőségi vizualizáció funkció használatához Service Map szükséges, és a Azure Migrate projekthez hozzá kell rendelnie egy Log Analytics-munkaterületet (új vagy meglévő). A kiszolgáló értékelése során a függőségi vizualizáció funkció az első 180 napra ingyenes.

1. A Log Analytics munkaterület Service Mapon kívüli bármely más megoldás használata esetén a [standard log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) díjat számítjuk fel.
2. Az áttelepítési forgatókönyvek további költségek nélkül történő támogatásához a Service Map megoldás nem számít fel díjat az Log Analytics munkaterület Azure Migrate projekthez való társításának napjától számított első 180 nap után. 180 nap után a standard Log Analytics díjak érvényesek lesznek.

Amikor ügynököt regisztrál a munkaterületre, használja a projekt által megadott azonosítót és kulcsot az ügynök telepítése lépések oldalon.

A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem lesz ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjéhez hasonlóan lesznek felszámítva.

> [!NOTE]
> A függőségi vizualizáció funkció a Service Map Log Analytics munkaterületen keresztül használja. 2018. február 28-án, a Azure Migrate általános elérhetőségének bejelentésével a szolgáltatás díjmentesen elérhető. Létre kell hoznia egy új projektet, amely az ingyenes használati munkaterület használatát teszi elérhetővé. A meglévő munkaterületek az általános elérhetőség előtt továbbra is díjkötelesek, ezért javasoljuk, hogy váltson át egy új projektre.

További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

## <a name="how-do-i-manage-the-workspace"></a>Hogyan felügyeli a munkaterületet?

A Log Analytics munkaterületet Azure Migraten kívül is használhatja. Ha törli azt a Azure Migrate projektet, amelyben létrehozták, nem törli a rendszer. Ha már nincs szüksége a munkaterületre, [törölje](../azure-monitor/platform/manage-access.md) manuálisan.

Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.

## <a name="next-steps"></a>További lépések
- [Gépek csoportosítása számítógép-függőségek használatával](how-to-create-group-machine-dependencies.md)
- [További](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) információ a függőségi vizualizációval kapcsolatos gyakori kérdésekért.
