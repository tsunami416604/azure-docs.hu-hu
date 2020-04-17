---
title: Azure áttelepítése – gyakori kérdések
description: Válaszok az Azure Migrate szolgáltatással kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530317"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: Gyakori kérdések

Ez a cikk az Azure Áttelepítésével kapcsolatos gyakori kérdésekre ad választ. Ha a cikk elolvasása után kérdései vannak, közzéteheti őket az [Azure Migrate fórumon.](https://aka.ms/AzureMigrateForum) Az alábbi cikkeket is áttekintheti:

- Kérdések az [Azure Migrate készülékkel kapcsolatban](common-questions-appliance.md)
- Kérdések [a felderítéssel, értékeléssel és függőségi megjelenítéssel kapcsolatban](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Mi az az Azure Migrate?

Az Azure Migrate központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint a magán- és nyilvános felhőbeli virtuális gépek Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a külső szoftvergyártói ajánlatokhoz. [További információ](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Mit tehetek az Azure Migrate?

Az Azure Migrate használatával felderítheti, felmérheti és áttelepítheti a helyszíni infrastruktúrát, alkalmazásokat és adatokat az Azure-ba. Az Azure Migrate támogatja a helyszíni VMware virtuális gépek, a Hyper-V vm-ek, a fizikai kiszolgálók, más virtualizált virtuális gépek, adatbázisok, webalkalmazások és virtuális asztalok értékelését és áttelepítését. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Mi a különbség az Azure Migrate és az Azure Site Recovery között?

[Az Azure Migrate](migrate-services-overview.md) egy központi központot biztosít az azure-ba való kiértékeléshez és áttelepítéshez. 

[Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) egy vész-helyreállítási megoldás. 

Az Azure Áttelepítés: Kiszolgálóáttelepítés eszköz néhány háttér-site recovery funkciót használ egyes helyszíni gépek lift-és-shift áttelepítése.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség az Azure Áttelepítés: Kiszolgálófelmérés és a MAP-eszközkészlet között?

A Server Assessment értékelést nyújt az áttelepítésre való felkészültség és az Azure-ba való áttelepítéshez kapcsolódó munkaterhelések kiértékeléséhez. A [Microsoft Assessment and Planning (MAP) eszközkészlet](https://www.microsoft.com/download/details.aspx?id=7826) e feladatok elvégzését is segíti, beleértve a Windows ügyfél- és kiszolgálóoperációs rendszerek újabb verzióinak áttelepítési tervezését, valamint a szoftverhasználat nyomon követését. Ezekben a forgatókönyvekben továbbra is használja a MAP-eszközkészletet.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Mi a különbség a kiszolgálófelmérés és a hely-helyreállítási telepítéstervező között?

A Kiszolgálóértékelés áttelepítési tervezési eszköz. A Hely-helyreállítási telepítés tervezője egy vész-helyreállítási tervezési eszköz.

Válassza ki az eszközt a kívánt teendők alapján:

- **Helyszíni áttelepítés tervezése az Azure-ba:** Ha azt tervezi, hogy áttelepíti a helyszíni kiszolgálókat az Azure-ba, használja a Kiszolgálóértékelése az áttelepítés tervezéséhez. A Kiszolgálófelmérés felméri a helyszíni számítási feladatokat, és útmutatást és eszközöket biztosít az áttelepítéshez. Miután az áttelepítési terv a helyén van, használhatja az olyan eszközöket, mint az Azure Áttelepítés: Kiszolgálóáttelepítés a gépek azure-ba való áttelepítéséhez.
- **Tervezze meg a vészhelyreállítást az Azure-ba:** Ha a helyszíni és az Azure-beli vészhelyreállítást a Site Recovery segítségével kívánja beállítani, használja a Site Recovery Deployment Planner t. A Deployment Planner egy mély, site recovery-specifikus értékelést biztosít a helyszíni környezetvész-helyreállítás céljából. A vész-helyreállítási javaslatokkal kapcsolatos, például a replikáció és a feladatátvétel.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hogyan működik a kiszolgálóáttelepítés a hely-helyreállítással?

- Ha az Azure Áttelepítés: Server Migration a helyszíni vmware virtuális gépek *ügynök nélküli* áttelepítéséhez használja, az áttelepítés az Azure Áttelepítése és a Site Recovery nem használható.
- Ha az Azure Migrate: Server Migration használatával hajthatja végre a VMware virtuális gépek *ügynökalapú* áttelepítését, vagy ha hyper-v virtuális gépeket vagy fizikai kiszolgálókat telepít át, az Azure Migrate: Server Migration az Azure Site Recovery replikációs motorját használja.

## <a name="which-geographies-are-supported"></a>Mely földrajzi területek támogatottak?

Tekintse át a támogatott földrajzi köz- [és](migrate-support-matrix.md#supported-geographies-public-cloud) [kormányzati felhőket.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

Azonosítsa a szükséges eszközt, majd adja hozzá az eszközt egy Azure Migrate projekthez. 

Független eszköz vagy Movere hozzáadása:

1. Első lépések: licencet szerez, vagy az eszközszabályzatnak megfelelően regisztrál egy ingyenes próbaverzióra. Az eszközök engedélyezése összhangban van az ISV vagy a szerszámlicencelési modellel.
2. Az egyes eszközökben van egy lehetőség az Azure Migrate való csatlakozáshoz. Kövesse az eszköz utasításait és dokumentációját az eszköz és az Azure Migrate csatlakoztatásához.

Nyomon követheti az áttelepítési útvonalat az Azure Migrate projektből, az Azure-ban és más eszközökben.

## <a name="how-do-i-delete-a-project"></a>Hogyan törölhetek egy projektet?

További információ a [projekt törléséről.](how-to-delete-project.md) 

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Migrate áttekintését.](migrate-services-overview.md)
