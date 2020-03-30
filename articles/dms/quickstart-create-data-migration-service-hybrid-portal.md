---
title: 'Rövid útmutató: Hibrid módú példány létrehozása az Azure Portalon'
titleSuffix: Azure Database Migration Service
description: Az Azure Portal használatával hibrid módban hozd létre az Azure Database Migration Service példányát.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: dd3e77610749eb5d146b0c0b7cf9d307fba0dd83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370236"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Rövid útmutató: Hibrid módú példány létrehozása az Azure Portal & az Azure Database Migration Service szolgáltatással

Az Azure Database Migration Service hibrid mód kezeli az adatbázis-áttelepítések segítségével egy áttelepítési dolgozó, amely a helyszínen üzemeltetett, valamint egy példányát az Azure Database Migration Service fut a felhőben. A hibrid mód különösen hasznos olyan esetekben, amikor a helyszíni hálózat és az Azure között nem áll rendelkezésre hely kapcsolat, vagy ha korlátozott a helyek közötti kapcsolat sávszélessége.

>[!NOTE]
>Jelenleg a hibrid módban futó Azure Database Migration Service támogatja az SQL Server áttelepítését:
>
>- Az Azure SQL Database felügyelt példánya közel nulla állásidővel (online).
>- Azure SQL Database egyetlen adatbázis némi állásidő (offline).
>- MongoDb az Azure CosmosDB közel nulla állásidő (online).
>- MongoDb az Azure CosmosDB némi állásidő (offline).

Ebben a gyorsútmutatóban az Azure Portal használatával hozza létre az Azure Database Migration Service hibrid módban példányait. Ezt követően töltse le, telepítse és állítsa be a hibrid feldolgozó a helyszíni hálózaton. Az előzetes verzió során az Azure Database Migration Service hibrid mód használatával áttelepítheti az sql server helyszíni példányából az Azure SQL Database-be az adatokat.

> [!NOTE]
> Az Azure Database Migration Service hibrid telepítője Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 és Windows 10 rendszeren fut.

> [!IMPORTANT]
> Az Azure Database Migration Service hibrid telepítője .NET 4.7.2-es vagy újabb verziót igényel. A .NET legújabb verzióinak megkereséséhez olvassa el a [.NET Framework letöltése](https://dotnet.microsoft.com/download/dotnet-framework) lapot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

Regisztrálja a Microsoft.DataMigration erőforrás-szolgáltatót az Azure Database Migration Service első példányának létrehozása előtt.

1. Az Azure Portalon válassza az **Előfizetések**lehetőséget, válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza az **Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltató keresése](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása

1. Válassza a +**Erőforrás létrehozása az** Azure Database Migration Service egy példányának létrehozásához lehetőséget.

2. Keressen a Piactéren az "áttelepítés" kifejezésre, válassza az **Azure Database Migration Service**lehetőséget, majd az Azure Database Migration **Service** képernyőn válassza a **Létrehozás lehetőséget.**

3. A **Migrálási szolgáltatás létrehozása** képernyőn:

    - Válasszon egy **olyan szolgáltatásnevet,** amely emlékezetes és egyedi az Azure Database Migration Service példányának azonosításához.
    - Válassza ki azt az **Azure-előfizetést**, amelyben a példányt létre szeretné hozni.
    - Válasszon ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat.
    - Válassza ki a forráshoz vagy a célkiszolgálóhoz legközelebb eső **Helyet**.
    - **A Szolgáltatás módban**válassza a **Hibrid (előzetes verzió)** lehetőséget.

         ![Áttelepítési szolgáltatás létrehozása - alapismeretek](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

5. A **Véleményezés + létrehozás** lapon tekintse át a Feltételeket, ellenőrizze a többi megadott információt, és válassza a **Létrehozás gombot.**

    ![Áttelepítési szolgáltatás létrehozása - Véleményezés + létrehozás](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Néhány pillanat múlva az Azure Database Migration Service hibrid módban példánya jön létre, és készen áll a beállításra. Az Azure Database Migration Service példány a következő képen látható módon jelenik meg:

    ![Az Azure Database Migration Service hibrid módú példánya](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. A szolgáltatás létrehozása után válassza a **Tulajdonságok**lehetőséget, majd másolja az **Erőforrás-azonosító** mezőben megjelenő értéket, amelyet az Azure Database Migration Service hibrid feldolgozójának telepítéséhez fog használni.

    ![Az Azure Database Migration Service hibrid módtulajdonságai](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Azure App regisztrációs azonosítójának létrehozása

Létre kell hoznia egy Azure-alkalmazás regisztrációs azonosítót, amely et a helyszíni hibrid feldolgozó a felhőben az Azure Database Migration Service-lel való kommunikációhoz használhat.

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, válassza az **Alkalmazásregisztrációk**lehetőséget, majd az **Új regisztráció**lehetőséget.
2. Adja meg az alkalmazás nevét, majd a **Támogatott fióktípusok**csoportban válassza ki a támogatni kívánt fiókok típusát, és adja meg, hogy ki használhatja az alkalmazást.

    ![Az Azure Database Migration Service hibrid módú regiszteralkalmazása](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Használja az **átirányítási URI (nem kötelező)** mezők alapértelmezett értékeit, majd válassza a **Regisztráció**lehetőséget.

4. Az alkalmazásazonosító-regisztráció befejezése után jegyezze fel az **alkalmazás (ügyfél) azonosítóját,** amelyet a hibrid feldolgozó telepítésekor fog használni.

5. Az Azure Portalon keresse meg az Azure Database Migration Service, válassza **a hozzáférés-vezérlés (IAM)** lehetőséget, majd válassza **a Szerepkör-hozzárendelés hozzáadása** az alkalmazásazonosítóhoz való hozzájáruláshoz.

    ![Az Azure Database Migration Service hibrid módhozzárendelési közreműködői szerepkörhöz rendelése](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Válassza ki a **közreműködő** szerepkörként, rendeljehozzá a hozzáférést az **Azure AD-felhasználóhoz vagy az egyszerű szolgáltatáshoz,** majd válassza ki az alkalmazásazonosító nevét.

    ![Az Azure Database Migration Service hibrid mód közreműködői szerepkör részleteinek hozzárendelése](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Válassza a **Mentés** lehetőséget az Alkalmazásazonosító szerepkör-hozzárendelésének mentéséhez az Azure Database Migration Service erőforráson.

## <a name="download-and-install-the-hybrid-worker"></a>A hibrid feldolgozó letöltése és telepítése

1. Az Azure Portalon keresse meg az Azure Database Migration Service példányát.

2. A **Beállítások csoportban**válassza a **Hibrid**lehetőséget, majd a Telepítse **a letöltést** a hibrid feldolgozó letöltéséhez.

    ![Az Azure Database Migration Service hibrid feldolgozói letöltése](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Bontsa ki a ZIP-fájlt azon a kiszolgálón, amely az Azure Database Migration Service hibrid feldolgozóját fogja üzemeltetni.

    > [!IMPORTANT]
    > Az Azure Database Migration Service hibrid telepítője .NET 4.7.2-es vagy újabb verziót igényel. A .NET legújabb verzióinak megkereséséhez olvassa el a [.NET Framework letöltése](https://dotnet.microsoft.com/download/dotnet-framework) lapot.

4. A telepítési mappában keresse meg és nyissa meg a **dmsSettings.json** fájlt, adja meg az **ApplicationId** és **resourceId azonosítót,** majd mentse a fájlt.

    ![Az Azure Database Migration Service hibrid munkavégző beállításai](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Hozzon létre egy tanúsítványt, amely et az Azure Database Migration Service a következő parancs használatával hitelesítheti a hibrid feldolgozótól érkező kommunikációt.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    A telepítés mappában tanúsítvány jön létre.

    ![Az Azure Database Migration Service hibrid munkavégző tanúsítványa](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Az Azure Portalon keresse meg az **alkalmazásazonosítót,** a Kezelés csoportban válassza **a Tanúsítványokkal & titkos kulcsok**lehetőséget, majd válassza a Tanúsítvány **feltöltése** lehetőséget a létrehozott nyilvános tanúsítvány kiválasztásához.

    ![Az Azure Database Migration Service hibrid munkavégző tanúsítványának feltöltése](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Telepítse az Azure Database Migration Service hibrid feldolgozóját a helyszíni kiszolgálóra a következő parancs futtatásával:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > A telepítési parancs futtatásakor a következő paramétereket is használhatja:
    >
    > - **-TelemetryOptOut** - Leállítja a dolgozó telemetriai adatok küldését, de továbbra is minimálisan naplózza a helyi naplózást.  A telepítő továbbra is telemetriai adatokat küld.
    > - **-p {InstallLocation}**. Lehetővé teszi a telepítési útvonal módosítását, amely alapértelmezés szerint "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Ha a telepítő hiba nélkül fut, majd a szolgáltatás egy online állapot ot jelenít meg az Azure Database Migration Service-ben, és készen áll az adatbázisok áttelepítésére.

    ![Azure adatbázis-áttelepítési szolgáltatás online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Az Azure Database Migration Service hibrid módjának eltávolítása

Jelenleg az Azure Database Migration Service hibrid mód eltávolítása csak az Azure Database Migration Service hibrid feldolgozótelepítőjével támogatott a helyszíni kiszolgálón, a következő paranccsal:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Az eltávolítási parancs futtatásakor használhatja a "-ReuseCert" paramétert is, amely megtartja a generateCert munkafolyamat által létrehozott AdApp tanúsítványt.  Ez lehetővé teszi a korábban létrehozott és feltöltött tanúsítvány használatát.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Az Azure Database Migration Service hibrid feldolgozójának beállítása a PowerShell használatával

Az Azure Database Migration Service hibrid feldolgozóinak az Azure Portalon keresztül történő telepítése mellett egy [PowerShell-parancsfájlt](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) is biztosítunk, amelysegítségével automatizálhatja a dolgozói telepítési lépéseket, miután hibrid módban létrehozta az Azure Database Migration Service új példányát. A forgatókönyv:

1. Új AdApp-ot hoz létre.
2. Letölti a telepítőt.
3. Futtatja a generateCert munkafolyamatot.
4. Feltölti a tanúsítványt.
5. Hozzáadja az AdApp-ot közreműködőként az Azure Database Migration Service-példányhoz.
6. Futtatja a telepítési munkafolyamatot.

Ez a parancsfájl gyors prototípus-készítésre szolgál, ha a felhasználó már rendelkezik a környezetben szükséges engedélyekkel. Vegye figyelembe, hogy az éles környezetben az AdApp és a Cert különböző követelményekkel rendelkezhet, így a parancsfájl sikertelen lehet.

> [!IMPORTANT]
> Ez a parancsfájl feltételezi, hogy az Azure Database Migration Service egy meglévő példánya hibrid módban van, és hogy a használt Azure-fiók rendelkezik engedélyekkel az AdApps létrehozásához a bérlőben, és az rBAC módosítására az előfizetésen.

Töltse ki a paramétereket a parancsfájl tetején, majd futtassa a parancsfájlt egy rendszergazdai PowerShell-példányból.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [SQL Server áttelepítése egy Azure SQL Database felügyelt példányára online](tutorial-sql-server-managed-instance-online.md)
> [SQL Server áttelepítése egyetlen adatbázisba vagy készletezett adatbázisba az Azure SQL Database-ben offline módban](tutorial-sql-server-to-azure-sql.md)
