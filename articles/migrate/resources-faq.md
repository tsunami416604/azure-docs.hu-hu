---
title: Gyakori kérdések a Azure Migrate
description: Választ kaphat a Azure Migrate szolgáltatással kapcsolatos gyakori kérdésekre.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: baf01c0a0d5c6154305f7137c24deb0365b5e812
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062098"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: gyakori kérdések

Ez a cikk a Azure Migrateával kapcsolatos gyakori kérdésekre ad választ. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye fel őket a [Azure Migrate fórumba](https://aka.ms/AzureMigrateForum). Ha további kérdései vannak, tekintse át a következő cikkeket:

- Az Azure Migrate berendezéssel kapcsolatos [kérdések](common-questions-appliance.md) .
- A felderítéssel, az értékeléssel és a függőségi vizualizációval kapcsolatos [kérdések](common-questions-discovery-assessment.md) .


## <a name="what-is-azure-migrate"></a>Mi az az Azure Migrate?

Azure Migrate egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz. [További információk](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>Mire használhatom a Azure Migrate?

A Azure Migrate használatával felderítheti, elemezheti és áttelepítheti a helyszíni infrastruktúráját, alkalmazásait és az Azure-ba irányuló alkalmazásokat. Azure Migrate támogatja a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a más virtualizált virtuális gépek, az adatbázisok, a webalkalmazások és a virtuális asztalok értékelését és áttelepítését. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Mi a különbség a Azure Migrate és az Site Recovery között?

A Azure Migrate központosított hubot biztosít az Azure-ba történő értékeléshez és áttelepítéshez. [Azure site Recovery](../site-recovery/site-recovery-overview.md) vész-helyreállítási megoldás. A Azure Migrate: a kiszolgáló áttelepítési eszköze néhány háttér-Site Recovery funkciót használ a helyszíni gépek áttelepítéséhez.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség a Azure Migrate Server Assessment és a MAP Toolkit között?

A kiszolgáló értékelése értékelést nyújt az áttelepítésre való felkészüléshez és az Azure-ba való áttelepítéshez szükséges munkaterhelések kiértékeléséhez. A [Microsoft felmérési és tervezési (MAP) eszközkészlete](https://www.microsoft.com/download/details.aspx?id=7826) más feladatokkal is segít, beleértve a Windows ügyfél/kiszolgáló operációs rendszerek újabb verzióinak áttelepítésének megtervezését és a szoftverek használatának nyomon követését. Ezekben a forgatókönyvekben folytassa a MAP Toolkit használatát.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Mi a különbség a kiszolgálók értékelése és a Site Recovery Deployment Planner között?

A kiszolgáló értékelése egy áttelepítési tervező eszköz. A Site Recovery Deployment Planner vész-helyreállítási tervezési eszköz.

- Helyszíni **áttelepítés megtervezése az Azure-** ba: Ha a helyszíni kiszolgálókat az Azure-ba szeretné áttelepíteni, használja az áttelepítés megtervezésére szolgáló kiszolgáló-értékelést. Felméri a helyszíni számítási feladatokat, és útmutatást és eszközöket biztosít a Migrálás megkönnyítéséhez. Az áttelepítési terv betartása után az eszközöket, például Azure Migrate kiszolgáló áttelepítését is használhatja a gépek Azure-ba való áttelepítéséhez.
- Vész- **helyreállítási terv az Azure**-ba: Ha a helyszíni rendszerről az Azure-ba történő vész-helyreállítást tervezi a site Recovery használatával, használja a site Recovery Deployment Planner. A Deployment Planner a helyi környezet alapos, Site Recovery-specifikus értékelését biztosítja a vész-helyreállítási célra. Javaslatokat nyújt a vész-helyreállítási megoldásokról, például a replikálásról és a feladatátvételről.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hogyan működik a kiszolgáló áttelepítése a Site Recovery?

- Ha Azure Migrate-t használ: a kiszolgáló áttelepítése a helyszíni VMware virtuális gépek ügynök nélküli áttelepítésére, az áttelepítés a Azure Migrate, és a Site Recovery nincs használatban.
- Ha Azure Migrate-t használ: a kiszolgáló áttelepítése a VMware virtuális gépek ügynök-alapú áttelepítésének elvégzésére, illetve a Hyper-V virtuális gépek vagy fizikai kiszolgálók áttelepítésére, Azure Migrate a kiszolgáló áttelepítése a Azure Site Recovery replikációs motort használja.


## <a name="which-geographies-are-supported"></a>Mely földrajzi területek támogatottak?

Tekintse át a [VMWare virtuális gép](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) és a [Hyper-V virtuális gépek](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)Azure Migrate támogatott földrajzi területeit.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

Azonosíthatja a szükséges eszközt, és hozzáadhatja egy Azure Migrate projekthez. Ha ISV-eszközt vagy Mozgatót ad hozzá:
- Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz házirendjének megfelelően. Az eszközök licencelése az ISV vagy az eszköz licencelési modelljének megfelelően történik.
- Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate való összekapcsolásához kövesse az eszköz útmutatásait és dokumentációját.
Központilag nyomon követheti az áttelepítési utat a Azure Migrate projekten belül, az Azure-ban és más eszközökön.

## <a name="how-do-i-delete-a-project"></a>Hogyan törölni a projektet?

[További információ](how-to-delete-project.md) a projektek törléséről. 




## <a name="next-steps"></a>Következő lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).
