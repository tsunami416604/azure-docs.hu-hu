---
title: SSIS-csomagok áttelepítése SQL által felügyelt példányra
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepítheti át az SQL Server Integration Services (SSIS) csomagokat és projekteket egy Azure SQL Database-felügyelt példányba az Azure Database Migration Service vagy az Adatáttelepítési segéd használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297179"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>SQL Server Integration Services-csomagok migrálása felügyelt Azure SQL Database-példányra
Ha SQL Server Integration Services (SSIS) szolgáltatást használ, és át szeretné telepíteni az SSIS-projekteket/csomagokat az SQL Server által üzemeltetett ForrásSISDB-ből az Azure SQL Database felügyelt példány által üzemeltetett cél SSISDB-be, használhatja az Azure Database Migration Service szolgáltatást.

Ha az SSIS által használt verzió korábbi, mint 2012,-ban, vagy nem SSISDB csomagtároló-típusokat használ, az SSIS-projektek/csomagok áttelepítése előtt konvertálnia kell azokat az Integrációs szolgáltatások projektkonverzióvarázslójával, amely az SSMS-ből is indítható. További információt a [Projektek konvertálása a projekt telepítési modelljéhez](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)című témakörben talál.

> [!NOTE]
> Az Azure Database Migration Service (DMS) jelenleg nem támogatja az Azure SQL Database céláttelepítési célként. Az SSIS-projektek/-csomagok Azure SQL Database-be való újratelepítéséről az [SQL Server Integration Services-csomagok újratelepítése az Azure SQL Database-be](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * Forrás SSIS-projektek/csomagok felmérése.
> * Telepítse át az SSIS-projekteket/csomagokat az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

A lépések végrehajtásához a következőkre van szükség:

* Microsoft Azure virtuális hálózat létrehozása az Azure Database Migration Service számára az Azure Resource Manager telepítési modelljével, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálókhoz [az ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [a VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával. További információt az [Azure SQL Database által felügyelt példányok áttelepítéséhez az Azure Database-áttelepítések hálózati topológiái című témakörben talál.]( https://aka.ms/dmsnetworkformi) A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.
* Annak biztosítása érdekében, hogy a virtuális hálózati hálózati biztonsági csoport szabályai ne tiltsák le a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)
* A [Windows tűzfal konfigurálása a forrásadatbázis-motor eléréséhez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* A Windows tűzfal megnyitásához, hogy az Azure Database Migration Service hozzáférjen a forrás SQL Server, amely alapértelmezés szerint a TCP-port 1433.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
* Egy Azure SQL Database felügyelt példány az SSISDB üzemeltetéséhez. Ha létre kell hoznia egyet, kövesse az [Azure SQL-adatbázis felügyelt példányának létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)című cikk részleteit.
* Annak biztosítása, hogy a forrás SQL Server és a cél felügyelt példány csatlakoztatásához használt bejelentkezések a sysadmin server szerepkör tagjai legyenek.
* Annak ellenőrzése, hogy az SSIS az Azure-SSIS-integrációs futásidejű (IR) szolgáltatást tartalmazó Azure Data Factoryban (ADF) van-e kiépítve az Azure-SSIS-integrációs futásidejű (IR) tárolóval az Azure SQL Database által felügyelt példány által üzemeltetett cél SSISDB-vel [(az Azure-SSIS-integrációs futásidő létrehozása az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)című cikkben leírtak szerint).

## <a name="assess-source-ssis-projectspackages"></a>Forrás SSIS-projektek/csomagok értékelése

Bár a forrás SSISDB értékelése még nincs integrálva az adatbázis-áttelepítési segédbe (DMA), az SSIS-projektek/-csomagok értékelése/érvényesítése az Azure SQL Database felügyelt példányon tárolt cél SSISDB-re való újratelepítése során történik.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása**lehetőséget, keresse meg az Azure Database **Migration Service**elemet, majd válassza az Azure Database **Migration Service** lehetőséget a legördülő listából.

     ![Azure Piactér](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egyet.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás SQL Server és a cél Az Azure SQL Database felügyelt példány.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

    További részleteket az [Azure SQL DB által felügyelt példányok áttelepítése](https://aka.ms/dmsnetworkformi)az Azure Database Migration Service használatával című cikkben olvashat.

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

4. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza az **SQL Server**lehetőséget a **Célkiszolgáló típusa** szövegmezőben, válassza az Azure SQL Database Felügyelt **példánya lehetőséget,** majd a **Tevékenység típusának kiválasztása**területen válassza az **SSIS-csomag áttelepítése**lehetőséget.

   ![DMS-projekt létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem nyújtanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Ne hagyatkozzon a TLS-re, amely önaláírt tanúsítványokat használ éles környezetben vagy az internethez kapcsolódó kiszolgálókon.

   ![Forrás részletei](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **Áttelepítési cél részletei** képernyőn adja meg a cél kapcsolatának részleteit.

     ![Cél részletei](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Kattintson a **Mentés** gombra.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Az **SSIS-projekt(ek) és a környezet(ek) felülírása beállítás**esetén adja meg, hogy felülírja vagy figyelmen kívül hagyja-e a meglévő SSIS-projekteket és környezeteket.

    ![Migrálási projekt áttekintése](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

## <a name="next-steps"></a>További lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [adatbázis-áttelepítési útmutatójában.](https://datamigration.microsoft.com/)
