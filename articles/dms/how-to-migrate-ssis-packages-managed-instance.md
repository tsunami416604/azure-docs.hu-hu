---
title: Az SQL Server Integration Services-csomagok áttelepítése egy Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Ismerje meg, hogy az SQL Server Integration Services-csomagok áttelepítése egy Azure SQL Database felügyelt példányában.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083183"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Az SQL Server Integration Services-csomagok áttelepítése egy Azure SQL Database felügyelt példány
Ha az SQL Server Integration Services (SSIS) használ, és az SSIS-projektek/csomagok áttelepítése a forráskiszolgálóról a cél Azure SQL Database felügyelt példány által üzemeltetett SSISDB SQL-kiszolgáló által üzemeltetett SSISDB szeretne, Azure Database Migration Service használhatja.

Ha az SSIS használata verziója 2012 rendszernél korábbi vagy ha nem SSISDB csomag tárolók típusait, mielőtt áttelepítése az SSIS-projektek/csomagok, kell átalakítani azokat az integrációs szolgáltatások projekt átalakítás varázslót, amely is elindítható az SSMS használatával. További információkért tekintse meg a cikket [projektek konvertálása a project-alapú üzemi modellbe](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) jelenleg nem támogatja az Azure SQL Database cél áttelepítési céljaként. SSIS-projektek/csomagok az Azure SQL Database-beli ismételt üzembe, tekintse meg a cikket [ismételt üzembe helyezése az SQL Server Integration Services-csomagok az Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Forrás SSIS-projektek/csomagok felmérése
> * SSIS-projektek/csomagok áttelepítése az Azure-bA.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez szüksége:

* A helyek közötti kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával az Azure Resource Manager üzemi modell használatával hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Database Migration Service [ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). További információkért tekintse meg a cikket [hálózati topológiák az Azure SQL Database felügyelt példányainak migrálásához, Azure Database Migration Service segítségével]( https://aka.ms/dmsnetworkformi). A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: a [Virtual Network-dokumentáció](https://docs.microsoft.com/azure/virtual-network/), és különösen a témakör részletesen a rövid útmutató cikkek.
* Annak érdekében, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443, 53, 9354, 445, 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurálhatja a [forrás hozzáféréshez a Windows tűzfal](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* A Windows tűzfalat az Azure Database Migration Service eléréséhez a forrás SQL-kiszolgáló, amely alapértelmezés szerint az 1433-as TCP-port megnyitásához.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
* Az Azure SQL Database felügyelt példány SSISDB-gazdagépre. Ha szeretne létrehozni egyet, kövesse a cikk részletesen [hozzon létre egy Azure SQL Database felügyelt példányába](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Annak érdekében, hogy a forrás kapcsolódhat a bejelentkezések a felügyelt példány az SQL Server és a cél a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Győződjön meg arról, hogy SSIS ki van építve az Azure Data Factory (ADF) tartalmazó Azure-SSIS integrációs modul (IR) a cél Azure SQL Database által üzemeltetett SSISDB által felügyelt példány (a cikkben leírtak szerint [létrehozása az Azure-SSIS az Azure Data Factory saját üzemeltetésű integrációs](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Forrás SSIS-projektek/csomagok felmérése

Értékelési forrás SSISDB még nem integrált, a Database Migration Assistant (DMA), míg az SSIS-projektek/csomagok értékelt/érvényesíti, azokat a cél egy Azure SQL Database felügyelt példányon futó SSISDB kérésének van.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Válassza ki az előfizetést, amelyben az Azure Database Migration Service példányát létre, és válassza ki a kívánt **erőforrás-szolgáltatók**.

    ![Erőforrás-szolgáltatók megtekintése](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keressen ár az **Azure Database Migration Service** kifejezésre, és a legördülő menüben válassza ki az **Azure Database Migration szolgáltatás** elemet.

     ![Azure Piactér](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő Vnetet, vagy hozzon létre egyet.

    A virtuális hálózat az Azure Database Migration Service a forrás SQL-kiszolgáló és a cél Azure SQL Database felügyelt példány hozzáférést biztosít.

    További információ a virtuális hálózat létrehozása az Azure Portalon, tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

    További részletekért tekintse meg a cikket [hálózati topológiák az Azure SQL DB felügyelt példányainak migrálásához az Azure Database Migration Service segítségével](https://aka.ms/dmsnetworkformi).

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **SQL Server**, a a **célkiszolgáló típus** szövegbeviteli mezőben válasszon ki **Azure SQL Database felügyelt példányába**, majd **válassza ki a tevékenység típusát**, jelölje be **SSIS-csomag áttelepítési**.

   ![DMS-projekt létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az a **Migrálási cél részletei** képernyőn, adja meg a cél kapcsolati adatait.

     ![Cél részletei](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Kattintson a **Mentés** gombra.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Az a **SSIS projekt(ek) és környezete(i) overwrite beállítást**, adja meg, hogy felülírja, vagy hagyja figyelmen kívül a meglévő SSIS-projektek és környezeteket.

    ![Migrálási projekt áttekintése](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

## <a name="next-steps"></a>További lépések

* Tekintse át a migrálási útmutató segítséget nyújt a Microsoft [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).
