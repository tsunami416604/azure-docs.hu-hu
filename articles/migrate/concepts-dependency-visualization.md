---
title: Az Azure Migrate függőségmegjelenítés |} A Microsoft Docs
description: Értékelési számítások az Azure Migrate szolgáltatás áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8df587db7655e2aafd876d80581f3296c8c99fbf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004092"
---
# <a name="dependency-visualization"></a>Függőségek vizualizációja

A [Azure Migrate](migrate-overview.md) szolgáltatások felméri a helyszíni gépek áttelepítése az Azure-bA a csoportjait. A függőségek képi megjelenítésének funkcióival az Azure Migrate segítségével hozzon létre csoportokat. Ez a cikk a szolgáltatásról.

> [!NOTE]
> A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.

## <a name="overview"></a>Áttekintés

Az Azure Migrate függőségmegjelenítés nagy megbízhatóságú csoportokat az áttelepítési felmérések létrehozását teszi lehetővé. Függőségek képi megjelenítésének megtekintheti a hálózati gépek függőségeit, és együtt kell áttelepíteni az Azure-hoz szükséges kapcsolódó gépek azonosítására szolgáló használatával. Ez a funkció akkor hasznos, forgatókönyvekben, ahol Ön nem teljesen tisztában a gépek, amelyek az alkalmazás jelent, és együtt kell áttelepíteni az Azure-bA.

## <a name="how-does-it-work"></a>Hogyan működik?

Azure Migrate az a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldás [naplózza az Azure Monitor](../log-analytics/log-analytics-overview.md) a függőségek képi megjelenítéséről.
- Kihasználhatja a függőségek képi megjelenítésével, hozzá kell rendelni egy Log Analytics-munkaterületet, vagy új vagy meglévő, az Azure Migrate-projektet.
- Csak létrehozása vagy csatolása ugyanahhoz az előfizetéshez egy munkaterületet, ahol a migrálási projekt létrejön.
- Log Analytics-munkaterület csatolása a projekthez, lépjen a **Essentials** projekt **áttekintése** lapot, és kattintson **konfigurálást igényel**

    ![Log Analytics-munkaterület társítása](./media/concepts-dependency-visualization/associate-workspace.png)

- Munkaterület társítása, miközben kap arra, hozzon létre egy új munkaterületet, vagy csatlakoztasson egy már meglévőt:
  - Amikor létrehoz egy új munkaterületet, adja meg a munkaterület nevét kell. A munkaterületen létrejön ugyanabban a régióban [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) a migrálási projektet.
  - Ha csatlakoztat egy meglévő munkaterületet, a rendelkezésre álló munkaterületek ugyanahhoz az előfizetéshez tartozik, mint a migrálási projekt közül választhat. Vegye figyelembe, hogy csak azokat a munkaterületeket egy régióban létrehozott szerepel ahol [Service Map támogatott](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Tudják csatolása egy munkaterületet, győződjön meg arról, hogy a munkaterület "Olvasó" elérhető lesz.

  > [!NOTE]
  > Miután egy projektet egy munkaterületet van csatolva, később nem módosítható.

- A kapcsolódó munkaterület a kulccsal van megjelölve **Migrálási projekt**, és érték **projektnév**, amelyek segítségével az Azure Portalon keresse.
- Nyissa meg a munkaterületet a projekthez kapcsolódó, nyissa meg a **Essentials** projekt **áttekintése** lapon és a munkaterület eléréséhez

    ![Keresse meg a Log Analytics-munkaterület](./media/concepts-dependency-visualization/oms-workspace.png)

Függőségmegjelenítést használ, meg kell töltse le és telepítse az ügynököt minden olyan elemezni szeretné a helyszíni gépen.  

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) minden gépen kell telepíteni.
- A [függőségi ügynök](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) minden gépen kell telepíteni.
- Ezenkívül ha internetkapcsolat nélküli gépek, meg kell töltse le és telepítse őket a Log Analytics-átjáró.

Nem kell ezeket az ügynököket, kivéve, ha függőségmegjelenítést használ értékelni kívánt gépeken.

## <a name="do-i-need-to-pay-for-it"></a>Kell fizetni?

Az Azure Migrate díjmentesen érhető el. A függőségmegjelenítési funkciót az Azure Migrate használata szükséges a Szolgáltatástérkép, és elő kell társítani egy Log Analytics-munkaterületet, vagy új vagy meglévő, az Azure Migrate-projektben. A függőségek képi megjelenítésének funkcióival az Azure Migrate az Azure Migrate az első 180 nap díjmentes.

1. A Log Analytics-munkaterületen a Service Map kívül bármely megoldások használatát díjat [standard Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) díjak.
2. További költségek nélkül áttelepítési forgatókönyvek támogatása érdekében a Service Map megoldás nem számítunk fel díjakat a nap, a Log Analytics-munkaterület társítása az Azure Migrate-projekt első 180 napig. 180 nap elteltével a Log Analytics standard díjszabás vonatkozik.

Ha regisztrálja az ügynököket a munkaterülethez, használja az Azonosítót és a kulcsot adja meg a projekt telepítése az ügynök lépések lapján.

Az Azure Migrate-projekt törlése esetén a munkaterület nem törlődik, együtt. Közzététel a projekt törlése, a Service Map használata nem ingyenes lesz, és minden egyes csomópont díját a fizetős szint a Log Analytics-munkaterület.

> [!NOTE]
> A függőségmegjelenítési funkciót használja a Service Map Log Analytics-munkaterület-n keresztül. 2018. február 28 óta az Azure Migrate általános rendelkezésre állást, a közlemény a funkció már elérhető külön díjfizetés nélkül. Hozzon létre egy új projektet, győződjön meg arról, hogy kell használni az ingyenes használat munkaterület. Általános rendelkezésre állás előtti meglévő munkaterületek felszámítható továbbra is, ezért azt javasoljuk, hogy helyezze át egy új projektet.

További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

## <a name="how-do-i-manage-the-workspace"></a>Hogyan kezelhetem a munkaterületet?

A Log Analytics-munkaterületet az Azure Migrate kívül is használhatja. Ha törli a migrálási projektben, ahol létrehozták nem törlődik. Ha már nincs szüksége a munkaterületen [törölné](../azure-monitor/platform/manage-access.md) manuálisan.

Ne törölje az Azure Migrate, által létrehozott munkaterület, hacsak nem törli a migrálási projektet. Ha így tesz, a függőségek képi megjelenítésének funkcióival nem működnek megfelelően.

## <a name="next-steps"></a>További lépések
- [A gépek függőségeivel csoportosítása](how-to-create-group-machine-dependencies.md)
- [További](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) kapcsolatban a gyakori kérdések a függőségek képi megjelenítéséről.
