---
title: 'Gyors útmutató: Azure Database Migration Service hibrid üzemmódú példány létrehozása a Azure Portal használatával | Microsoft Docs'
description: Azure Database Migration Service hibrid módban való létrehozásához használja a Azure Portal.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/06/2019
ms.openlocfilehash: 47c0ecb5674f821d3034fb5f165df08f176c2e93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646938"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>Gyors útmutató: Azure Database Migration Service-példány létrehozása hibrid módban a Azure Portal használatával (előzetes verzió)

Azure Database Migration Service a hibrid üzemmód az adatbázisok áttelepítését a helyszínen üzemeltetett áttelepítési feldolgozóval, a felhőben futó Azure Database Migration Service egy példányával kezeli. A hibrid mód különösen olyan esetekben hasznos, amikor a helyszíni hálózat és az Azure közötti helyek közötti kapcsolat hiánya, illetve ha a helyek közötti kapcsolat sávszélessége korlátozott.

Ebben a rövid útmutatóban a Azure Portal használatával hozza létre a Azure Database Migration Service egy példányát hibrid módban. Ezt követően töltse le, telepítse és állítsa be a hibrid feldolgozót a helyszíni hálózatán. Az előzetes verzió használata során Azure Database Migration Service hibrid mód használatával áttelepítheti az adatok áttelepítését a SQL Server helyszíni példányáról Azure SQL Databasere.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A Azure Database Migration Service első példányának létrehozása előtt regisztrálja a Microsoft. DataMigration erőforrás-szolgáltatót.

1. A Azure Portal válassza az **előfizetések**elemet, válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók**elemet.

    ![Erőforrás-szolgáltató keresése](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása

1. Válassza az +**erőforrás létrehozása** lehetőséget Azure Database Migration Service-példány létrehozásához.

2. A piactéren keressen a „migration” kifejezésre, válassza ki az **Azure Database Migration Service** elemet, majd az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** parancsot.

3. A **Migrálási szolgáltatás létrehozása** képernyőn:

    - Válasszon egy emlékezetes és egyedi **szolgáltatásnevet** a Azure Database Migration Service példányának azonosításához.
    - Válassza ki azt az **Azure-előfizetést**, amelyben a példányt létre szeretné hozni.
    - Válasszon ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat.
    - Válassza ki a forráshoz vagy a célkiszolgálóhoz legközelebb eső **Helyet**.

    > [!IMPORTANT]
    > Az előzetes verzióban a hibrid üzemmód csak az USA keleti régiójában támogatott. Mivel a hibrid feldolgozó a helyszíni hálózatra van telepítve, a teljesítményre nincs hatással, még akkor is, ha egy másik régióban lévő célra végez áttelepítést.

    - **Szolgáltatási mód**esetén válassza a **hibrid (előzetes verzió)** lehetőséget.

      ![Áttelepítési szolgáltatás létrehozása – alapismeretek](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

5. A **felülvizsgálat + létrehozás** lapon tekintse át a feltételeket, ellenőrizze a többi megadott információt, majd válassza a **Létrehozás**lehetőséget.

    ![Áttelepítési szolgáltatás létrehozása – felülvizsgálat + létrehozás](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Néhány pillanat múlva létrejön a Azure Database Migration Service hibrid módban, és készen áll a beállításra. A Azure Database Migration Service példány az alábbi képen látható módon jelenik meg:

    ![Azure Database Migration Service hibrid üzemmód példánya](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. A szolgáltatás létrehozása után válassza a **Tulajdonságok**elemet, majd másolja ki az **erőforrás-azonosító** mezőben megjelenő értéket, amelyet az Azure Database Migration Service Hybrid Worker telepítéséhez fog használni.

    ![Azure Database Migration Service hibrid üzemmód tulajdonságai](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Azure-alkalmazás regisztrációs AZONOSÍTÓjának létrehozása

Létre kell hoznia egy Azure-alkalmazás regisztrációs AZONOSÍTÓját, amelyet a helyszíni hibrid feldolgozó használhat a felhőben lévő Azure Database Migration Service való kommunikációhoz.

1. A Azure Portal válassza a **Azure Active Directory**lehetőséget, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **új regisztráció**lehetőséget.
2. Adja meg az alkalmazás nevét, majd a **támogatott fióktípus**területen válassza ki a támogatni kívánt fiókok típusát annak megadásához, hogy ki használhatja ki az alkalmazást.

    ![Azure Database Migration Service Hybrid Mode – alkalmazás regisztrálása](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Használja az **átirányítási URI (nem kötelező)** mezők alapértelmezett értékeit, majd válassza a **regisztráció**lehetőséget.

4. Az alkalmazás-azonosító regisztrációjának befejeződése után jegyezze fel az **alkalmazás (ügyfél) azonosítóját**, amelyet a hibrid feldolgozó telepítésekor fog használni.

5. A Azure Portalban navigáljon a Azure Database Migration Service elemre, válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása** lehetőséget a közreműködői hozzáférés az alkalmazás-azonosítóhoz való hozzárendeléséhez.

    ![Azure Database Migration Service hibrid mód hozzárendelt közreműködői szerepkör](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Válassza ki a **közreműködő** szerepkört, rendeljen hozzá hozzáférést az **Azure ad-felhasználóhoz vagy a szolgáltatáshoz**, majd válassza ki az alkalmazás azonosítójának nevét.

    ![A hibrid mód Azure Database Migration Service közreműködői szerepkör részleteinek megadása](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Válassza a **Mentés** lehetőséget a Azure Database Migration Service erőforrásban található alkalmazás-azonosító szerepkör-hozzárendelésének mentéséhez.

## <a name="download-and-install-the-hybrid-worker"></a>A hibrid feldolgozó letöltése és telepítése

1. A Azure Portal navigáljon a Azure Database Migration Service-példányához.

2. A **Beállítások**területen válassza a **hibrid**lehetőséget, majd a hibrid feldolgozó letöltéséhez válassza a **telepítő letöltése** lehetőséget.

    ![Azure Database Migration Service Hybrid Worker letöltése](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Bontsa ki a ZIP-fájlt azon a kiszolgálón, amely a Azure Database Migration Service Hybrid Worker szolgáltatást fogja üzemeltetni.

4. A telepítési mappában keresse meg és nyissa meg a **dmsSettings. JSON** fájlt, adja meg a **ApplicationId** és a **resourceId**, majd mentse a fájlt.

    ![Hibrid feldolgozói beállítások Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)
 
5. A következő parancs használatával létrehozhat egy olyan tanúsítványt, amelyet a Azure Database Migration Service használhat a hibrid feldolgozóval folytatott kommunikáció hitelesítéséhez.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    A rendszer létrehoz egy tanúsítványt a telepítési mappában.

    ![Hibrid feldolgozói tanúsítvány Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. A Azure Portalban navigáljon az alkalmazás-AZONOSÍTÓhoz a **kezelés**területen válassza a **tanúsítványok & Secrets**elemet, majd a **tanúsítvány feltöltése** lehetőséget a most létrehozott nyilvános tanúsítvány kiválasztásához.

    ![Azure Database Migration Service hibrid feldolgozói tanúsítvány feltöltése](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. A következő parancs futtatásával telepítse a Azure Database Migration Service hibrid feldolgozót a helyszíni kiszolgálóra:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

8. Ha a telepítő hiba nélkül fut, akkor a szolgáltatás a Azure Database Migration Service online állapotot jeleníti meg, és készen áll az adatbázisok áttelepítésére.

    ![Online Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Azure Database Migration Service hibrid üzemmód eltávolítása

Jelenleg a Azure Database Migration Service hibrid üzemmód eltávolítása csak a helyszíni kiszolgálón lévő Azure Database Migration Service Hybrid Worker-telepítőn keresztül támogatott a következő parancs használatával:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [SQL Server migrálása Azure SQL Database felügyelt példányra online](tutorial-sql-server-managed-instance-online.md)
> a [SQL Server migrálása egyetlen adatbázisba vagy készletezett adatbázisba Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
