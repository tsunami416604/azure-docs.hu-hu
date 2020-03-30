---
title: 'Oktatóanyag: Az SQL Server áttelepítése online egy SQL által felügyelt példányba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést a helyszíni SQL Server-ből egy Azure SQL Database által felügyelt példányba az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298909"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Oktatóanyag: Az SQL Server áttelepítése egy Azure SQL Database felügyelt példányába online a DMS használatával

Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy helyszíni SQL Server-példányból egy [Azure SQL Database által felügyelt példányba](../sql-database/sql-database-managed-instance.md) minimális állásidővel. További módszerek, amelyek némi manuális erőfeszítést igényel, olvassa el a cikk [SQL Server példány áttelepítésaz Azure SQL Database felügyelt példány.](../sql-database/sql-database-managed-instance-migrate.md)

Ebben az oktatóanyagban az **Adventureworks2012** adatbázisát az SQL Server egy helyszíni példányából egy SQL Database felügyelt példányba telepíti át minimális állásidővel az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet, és indítsa el az online áttelepítést az Azure Database Migration Service használatával.
> * A migrálás monitorozása.
> * Ha készen áll, végezze el az áttelepítési átállást.

> [!IMPORTANT]
> Az SQL Serverről az SQL Database Migration Service használatával felügyelt példányba történő online áttelepítések esetén meg kell adnia a teljes adatbázis-biztonsági mentést és az azt követő naplóbiztonsági mentéseket az SMB hálózati megosztáson, amelyet a szolgáltatás az adatbázisok áttelepítéséhez használhat. Az Azure Database Migration Service nem kezdeményez biztonsági mentéseket, és ehelyett a meglévő biztonsági mentéseket, amelyek már rendelkeznek a vész-helyreállítási terv részeként, az áttelepítéshez.
> Győződjön meg arról, hogy biztonsági másolatokat készít [a CHECKSUM kapcsolóval.](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017) Ügyeljen arra is, hogy ne fűzjön több biztonsági mentést (azaz teljes és t-log) egyetlen biztonsági másolatba; minden biztonsági mentést külön biztonsági másolattal. Végül a tömörített biztonsági mentések használatával csökkentheti a nagy biztonsági mentések áttelepítésével kapcsolatos lehetséges problémák előfordulásának valószínűségét.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy példányt az Azure Database Migration Service ugyanabban az Azure-régióban, mint a cél-adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

> [!IMPORTANT]
> Csökkentse az online áttelepítési folyamat időtartamát a lehető legnagyobb mértékben, hogy minimálisra csökkentse a példányújrakonfigurálás vagy a tervezett karbantartás által okozott megszakítás kockázatát. Ilyen esemény esetén az áttelepítési folyamat az elejétől kezdődik. Tervezett karbantartás esetén az áttelepítési folyamat újraindítása előtt 36 órás türelmi idő áll fenn.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk az SQL Server kiszolgálóról az SQL Database által felügyelt példányba történő online áttelepítést ismerteti. Kapcsolat nélküli áttelepítés esetén olvassa el Az [SQL Server áttelepítése sql-adatbázisba offline módban a DMS használatával című témakört.](tutorial-sql-server-to-managed-instance.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. [Ismerje meg az Azure SQL Database által felügyelt példányok áttelepítéséhez használt hálózati topológiákat az Azure Database Migration Service használatával.](https://aka.ms/dmsnetworkformi) A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    >
    > * Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > * Tárolási végpont
    > * Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.
    >
    >Ha nem rendelkezik a helyszíni hálózat és az Azure közötti, a helyek közötti kapcsolattal, vagy ha a helyek közötti kapcsolat sávszélessége korlátozott, fontolja meg az Azure Database Migration Service hibrid módban (előzetes verzió) használatát. A hibrid mód egy helyszíni áttelepítési dolgozót és a felhőben futó Azure Database Migration Service egy példányát használja ki. Az Azure Database Migration Service hibrid módban történő példányának létrehozásához olvassa el az [Azure Database Migration Service példányának létrehozása hibrid módban az Azure Portal használatával](https://aka.ms/dms-hybrid-create)című témakört.

    > [!IMPORTANT]
    > Az áttelepítés részeként használt tárfiókot illetően a következőket kell tennie:
    > * Válassza ki, hogy az összes hálózat hozzáférhet a tárfiókhoz.
    > * Kapcsolja be az [alhálózati delegálást](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) a mi-alhálózaton, és frissítse a Storage Account tűzfalszabályait az alhálózat engedélyezéséhez.

* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a forrás SQL Server kiszolgálóhoz, amely alapértelmezés szerint a 1433-as TCP-port.
* Ha több elnevezett SQL Server-példányt futtat dinamikus portokkal, engedélyezze az SQL Browser Service szolgáltatást, és engedélyezze az 1434-es UDP-porthoz való hozzáférést a tűzfalakon keresztül, hogy az Azure Database Migration Service a forrásnévvel ellátott példányhoz kapcsolódjon Szerver.
* Ha a forrásadatbázisok előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
* Hozzon létre egy SQL Database felügyelt példányt az Azure Portalon felügyelt [Azure Database-példány létrehozása](https://aka.ms/sqldbmi)című cikk részleteinek követésével.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Adjon meg egy SMB hálózati megosztást, amely tartalmazza az összes adatbázis teljes adatbázis-biztonsági mentési fájlokat és az azt követő tranzakciós napló biztonsági mentési fájlokat, amelyeket az Azure Database Migration Service használhat az adatbázis-áttelepítéshez.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
* Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy töltse fel a biztonsági mentési fájlokat az Azure Storage-tárolóba visszaállítási művelethez.
* Hozzon létre egy Azure Active Directory-alkalmazásazonosítót, amely létrehozza azt az alkalmazásazonosító kulcsot, amelyet az Azure Database Migration Service az Azure Database felügyelt példányának és az Azure Storage Container célpéldányához való kapcsolódáshoz használhat. További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

  > [!NOTE]
  > Az Azure Database Migration Service megköveteli a közreműködői engedélyt az előfizetés a megadott alkalmazásazonosítóhoz. Azt is megteheti, hogy egyéni szerepköröket, amelyek az Azure Database Migration Service által igényelt engedélyeket adnak. Az egyéni szerepkörök használatával kapcsolatos részletes útmutatást az [SQL Server egyéni szerepkörei az SQL Database által felügyelt példányonline áttelepítése című témakörben talál.](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance)

* Hozzon létre vagy jegyezzen fel egy **standard teljesítményszintű** Azure Storage-fiókot, amelybe a DMS-szolgáltatás feltöltheti az adatbázis biztonsági mentési fájljait, majd felhasználhatja azokat az adatbázisok migrálásakor.  Győződjön meg arról, hogy az Azure Storage-fiók ugyanabban a régióban, mint az Azure Database Migration Service példány jön létre.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása**lehetőséget, keresse meg az Azure Database **Migration Service**elemet, majd válassza az Azure Database **Migration Service** lehetőséget a legördülő listából.

     ![Azure Piactér](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egyet.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás SQL Server és a cél SQL Database felügyelt példány hoz.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

    További részleteket az [Azure DATABASE-ben felügyelt példányok áttelepítéséhez az Azure Database-áttelepítéshez](https://aka.ms/dmsnetworkformi)című cikkben olvashat.

6. Válasszon ki egy termékváltozatot a prémium szintű tarifacsomagból.

    > [!NOTE]
    > Az online áttelepítések csak a prémium szint használata esetén támogatottak.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service összes példányának megkeresése](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza az **SQL Server**lehetőséget a **Célkiszolgáló típusa** szövegmezőben, válassza az Azure SQL Database Felügyelt **példánya lehetőséget,** majd a **Tevékenység típusának kiválasztása**területen válassza az Online **adatáttelepítés**lehetőséget.

   ![Azure-adatbázis-áttelepítési szolgáltatásprojekt létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem nyújtanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Ne hagyatkozzon a TLS-re, amely önaláírt tanúsítványokat használ éles környezetben vagy az internethez kapcsolódó kiszolgálókon.

   ![Forrás részletei](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Kattintson a **Mentés** gombra.

4. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki az **Adventureworks2012** adatbázist a migráláshoz.

   ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Ha SQL Server Integration Services (SSIS) szolgáltatást használ, a DMS jelenleg nem támogatja az SSIS-projektek/-csomagok (SSISDB) katalógusadatbázisának áttelepítését az SQL Server ről az Azure SQL Database felügyelt példányára. Az SSIS-t azonban az Azure Data Factoryban (ADF) is kiépítheti, és újratelepítheti az SSIS-projekteket/-csomagokat az Azure SQL Database által felügyelt példány által üzemeltetett cél SSISDB-re. Az SSIS-csomagok áttelepítéséről az SQL [Server Integration Services-csomagok áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat bővebben.

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn adja meg azt az **alkalmazásazonosítót** és **kulcsot,** amelyet a DMS-példány az Azure SQL Database felügyelt példánya és az Azure Storage-fiók célpéldányához való csatlakozáshoz használhat.

    További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

2. Válassza ki az Azure SQL Database felügyelt példányának célpéldányát tartalmazó **előfizetést,** majd válassza ki a célpéldányt.

    Ha még nem kiépítette az Azure SQL Database felügyelt példányát, válassza ki a [hivatkozást,](https://aka.ms/SQLDBMI) amely segít a példány kiépítésében. Amikor az Azure SQL Database felügyelt példánya készen áll, térjen vissza az adott projektaz áttelepítés végrehajtásához.

3. **SQL-felhasználó** és **jelszó** az Azure SQL Database felügyelt példányához való csatlakozáshoz.

    ![Cél kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Kattintson a **Mentés** gombra.

## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása

1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | | |
    |--------|---------|
    |**SMB hálózatihely-megosztás** | A helyi SMB hálózati megosztás vagy az Azure fájlmegosztás, amely tartalmazza a Teljes adatbázis biztonsági mentési fájlokat és a tranzakciónapló biztonsági mentési fájlokat, amelyeket az Azure Database Migration Service használhat az áttelepítéshez. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak olvasási és írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder. A jobb teljesítmény érdekében ajánlott külön mappát használni minden egyes áttelepítendő adatbázishoz. Az adatbázisszintű fájlmegosztási elérési utat a **Speciális beállítások** beállítással biztosíthatja. |
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy töltse fel a biztonsági mentési fájlokat az Azure Storage-tárolóba visszaállítási művelethez. Ha az Azure File share használatával használja a tárfiók nevét, amelyet előtöltődött az AZURE\ felhasználónévként. |
    |**Jelszó** | A felhasználó jelszava. Ha az Azure fájlmegosztás, használja a tárfiók kulcs a jelszó. |
    |**Az Azure Storage-tárfiók előfizetése** | Válassza ki az Azure Storage-tárfiókot tartalmazó előfizetést. |
    |**Azure storage-fiók** | Válassza ki az Azure Storage-tárfiókot, amelybe a DMS feltöltheti az SMB hálózati megosztásból származó biztonsági mentési fájlokat, majd felhasználhatja azokat a migráláskor.  Az optimális fájlfeltöltési teljesítmény érdekében javasoljuk, hogy a tárfiók ugyanabban a régióban legyen, mint a DMS-szolgáltatás. |

    ![Migrálási beállítások konfigurálása](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Ha az Azure Database Migration Service "System Error 53" vagy "System Error 57" hibaüzenetet jelenít meg, az ok oka az azure-adatbázis-áttelepítési szolgáltatás Azure-fájlmegosztás elérésének képtelensége lehet. Ha a hibák valamelyikével találkozik, az [itt](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)található utasítások segítségével adjon hozzáférést a virtuális hálózat ból származó tárfiókhoz.

    > [!IMPORTANT]
    > Ha a visszacsatolási ellenőrzés funkció engedélyezve van, és a forrás SQL Server és fájlmegosztás ugyanazon a számítógépen található, akkor a forrás nem fogja tudni elérni a fájlokat a nyúlhoz a teljes tartománynevesítő n-nel. A probléma megoldásához tiltsa le a visszacsatolási ellenőrző funkciót az [itt](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)található utasítások használatával.

2. Kattintson a **Mentés** gombra.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

    ![Migrálási projekt áttekintése](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>A migrálás futtatása és monitorozása

1. Válassza a **Migrálás futtatása** lehetőséget.

2. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Átállásos migrálás végrehajtása

Miután az adatbázis teljes biztonsági mentése helyreállt az SQL Database felügyelt példányának célpéldányán, az adatbázis elérhető vétethetéséhez.

1. Ha készen áll az adatbázis online migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Állítsa le a forrásadatbázisok összes bejövő forgalmát.

3. Vegye a [naplóvég biztonsági mentését], tegye elérhetővé a biztonságimásolat-fájlt az SMB hálózati megosztáson, majd várjon, amíg a rendszer helyreállítja ennek a végső tranzakciónaplónak a biztonsági másolatát.

    Ekkor a **Függőben lévő módosítások** száma 0-ra vált.

4. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.

    ![Teljes átállás előkészítése](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Ha az adatbázis-áttelepítési állapot **azt mutatja, befejezett,** csatlakoztassa az alkalmazásokat az Azure SQL Database felügyelt példányúj célpéldányához.

    ![Az átállás befejeződött](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>További lépések

* Az adatbázis T-SQL RESTORE paranccsal történő áttelepítését bemutató oktatóanyagról a [Visszaállítás paranccsal című témakörben található a Biztonsági másolat visszaállítása felügyelt példányra című témakörben.](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)
* A felügyelt példányról a Mi a felügyelt példány című [témakörben](../sql-database/sql-database-managed-instance.md)talál további információt.
* Az alkalmazások felügyelt példányhoz való csatlakoztatásáról az [Alkalmazások csatlakoztatása című](../sql-database/sql-database-managed-instance-connect-app.md)témakörben talál további információt.
