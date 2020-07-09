---
title: Azure Migrate GYIK
description: Választ kaphat a Azure Migrate szolgáltatással kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81530317"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: gyakori kérdések

Ez a cikk a Azure Migrateával kapcsolatos gyakori kérdésekre ad választ. Ha kérdései vannak a cikk elolvasása után, közzéteheti azokat a [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum). A következő cikkeket is ellenőrizheti:

- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések

## <a name="what-is-azure-migrate"></a>Mi az az Azure Migrate?

Azure Migrate egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követésére az Azure-ban. A hub Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a külső gyártóktól származó ISV-ajánlatokhoz. [További információk](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Mire használhatom a Azure Migrate?

A Azure Migrate használatával felderítheti, elemezheti és áttelepítheti a helyszíni infrastruktúráját, alkalmazásait és az Azure-ba irányuló alkalmazásokat. Azure Migrate támogatja a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a más virtualizált virtuális gépek, az adatbázisok, a webalkalmazások és a virtuális asztalok értékelését és áttelepítését. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Mi a különbség a Azure Migrate és az Azure Site Recovery között?

A [Azure Migrate](migrate-services-overview.md) központosított hubot biztosít az Azure-ba történő értékeléshez és áttelepítéshez. 

[Azure site Recovery](../site-recovery/site-recovery-overview.md) vész-helyreállítási megoldás. 

A Azure Migrate: a kiszolgáló áttelepítési eszköze néhány háttér-Site Recovery funkciót használ néhány helyszíni gép áttelepítésének áttelepítéséhez.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség a Azure Migrate: Server Assessment és a MAP Toolkit között?

A kiszolgáló értékelése értékelést nyújt az áttelepítésre való felkészüléshez és az Azure-ba való áttelepítéshez szükséges munkaterhelések kiértékeléséhez. A [Microsoft Assessment and Planning (MAP) eszközkészlet](https://www.microsoft.com/download/details.aspx?id=7826) más feladatokkal is segít, beleértve a Windows ügyfél és kiszolgáló operációs rendszereinek újabb verzióinak áttelepítésének megtervezését, valamint a szoftverek használatának nyomon követését. Ezekben a forgatókönyvekben folytassa a MAP Toolkit használatát.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Mi a különbség a kiszolgálók értékelése és a Site Recovery Deployment Planner között?

A kiszolgáló értékelése egy áttelepítési tervező eszköz. A Site Recovery Deployment Planner vész-helyreállítási tervezési eszköz.

Válassza ki az eszközt a kívánt műveletek alapján:

- Helyszíni **áttelepítés megtervezése az Azure-** ba: Ha a helyszíni kiszolgálókat az Azure-ba szeretné áttelepíteni, használja az áttelepítés megtervezésére szolgáló kiszolgáló-értékelést. A kiszolgáló értékelése a helyszíni számítási feladatokat értékeli, és útmutatást és eszközöket biztosít a Migrálás megkönnyítéséhez. Az áttelepítési terv érvénybe lépését követően a Azure Migrate: kiszolgáló áttelepítése a gépek Azure-ba való áttelepítéséhez.
- Vész- **helyreállítási terv az Azure**-ba: Ha a helyszíni rendszerről az Azure-ba történő vész-helyreállítást tervezi a site Recovery használatával, használja a site Recovery Deployment Planner. A Deployment Planner a helyi környezet alapos, Site Recovery-specifikus értékelését biztosítja a vész-helyreállítási célra. A vész-helyreállítással kapcsolatos ajánlásokat, például a replikálást és a feladatátvételt ismerteti.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hogyan működik a kiszolgáló áttelepítése a Site Recovery?

- Ha Azure Migrate-t használ: a kiszolgáló áttelepítése a helyszíni VMware virtuális gépek *ügynök* nélküli áttelepítésének elvégzésére, a Migrálás natív, Azure Migrate és site Recovery nincs használatban.
- Ha Azure Migrate-t használ: a kiszolgáló áttelepítése a VMware virtuális gépek *ügynök-alapú* áttelepítésének elvégzésére, illetve Hyper-V virtuális gépek vagy fizikai kiszolgálók áttelepítésére Azure Migrate: a kiszolgáló áttelepítése a Azure site Recovery replikációs motort használja.

## <a name="which-geographies-are-supported"></a>Mely földrajzi területek támogatottak?

Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

Azonosítsa a szükséges eszközt, majd adja hozzá az eszközt egy Azure Migrate projekthez. 

ISV-eszköz vagy-mozgató hozzáadása:

1. Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz házirendjének megfelelően. Az eszközök licencelése az ISV vagy az eszköz licencelési modelljének megfelelően történik.
2. Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate való összekapcsolásához kövesse az eszköz útmutatásait és dokumentációját.

Az áttelepítési utat nyomon követheti a Azure Migrate projekt, az Azure és más eszközök között.

## <a name="how-do-i-delete-a-project"></a>Hogyan törölni a projektet?

További információ a [projektek törléséről](how-to-delete-project.md). 

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).
