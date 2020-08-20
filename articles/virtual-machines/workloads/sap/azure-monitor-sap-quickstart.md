---
title: SAP-megoldások Azure Monitor üzembe helyezése a Azure Portal
description: SAP-megoldások Azure Monitor üzembe helyezése a Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.date: 08/17/2020
ms.openlocfilehash: 2c3b11ca0c2bb916a5a3fcaf50eb99c7db3aa8e7
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88643005"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>SAP-megoldások Azure Monitor üzembe helyezése Azure Portal

Az SAP-megoldások erőforrásainak Azure Monitor a [Azure Portal](https://azure.microsoft.com/features/azure-portal)használatával hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a Azure Monitor SAP-megoldások üzembe helyezéséhez és a szolgáltatók konfigurálásához.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen

## <a name="create-monitoring-resource"></a>Figyelési erőforrás létrehozása

1. Válassza ki Azure Monitor az **Azure Marketplace**-en elérhető **SAP-megoldásokhoz** .

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="A rendszerkép az Azure Marketplace-en elérhető SAP-megoldások Azure Monitor kiválasztását mutatja be." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Az **alapvető beállítások** lapon adja meg a szükséges értékeket. Ha alkalmazható, használhat meglévő Log Analytics munkaterületet.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="A Azure Portal konfigurációs beállításainak megjelenítése." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. A virtuális hálózat kiválasztásakor győződjön meg arról, hogy a figyelni kívánt rendszerek elérhetők a VNET belülről. 

   > [!IMPORTANT]
   > Ha a **megosztás** lehetőséget választja a Microsofttal való adatmegosztáshoz, a támogatási csapatunk további támogatást biztosít.

## <a name="configure-providers"></a>Szolgáltatók konfigurálása

### <a name="sap-hana-provider"></a>SAP HANA szolgáltató 

1. Válassza a **szolgáltató** lapot a konfigurálni kívánt szolgáltatók hozzáadásához. A figyelési erőforrás telepítése után több szolgáltatót is hozzáadhat egy másik után, vagy hozzáadhatja őket. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Megjeleníti a szolgáltató lapot, és további szolgáltatókat adhat hozzá a Azure Monitor SAP-megoldásokhoz." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Válassza a **szolgáltató hozzáadása** lehetőséget, majd a legördülő listából válassza a **SAP HANA** lehetőséget. 

3. Adja meg a HANA-kiszolgáló magánhálózati IP-címét.

4. Adja meg a használni kívánt adatbázis-bérlő nevét. Bármelyik bérlőt kiválaszthatja, de javasoljuk a **SYSTEMDB** használatát, mivel a figyelési területek szélesebb körét teszi lehetővé. 

5. Adja meg a HANA-adatbázishoz tartozó SQL-portszámot. A portszámnak **[3]**[  +  **példány #]**  +  **[13]** vagy **[3]**[példány  +  **#]**  +  **[15]** formátumúnak kell lennie. Például 30013 vagy 30015. 

6. Adja meg a használni kívánt adatbázis-felhasználónevet. Győződjön meg arról, hogy az adatbázis-felhasználónak van hozzárendelve a **figyelés** és a **katalógus olvasási** szerepköre. 

7. Ha elkészült, válassza a **szolgáltató hozzáadása**elemet. Szükség esetén folytassa a további szolgáltatók hozzáadását, vagy válassza a **felülvizsgálat + létrehozás** lehetőséget a telepítés befejezéséhez.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="A szolgáltatói információk hozzáadásakor a konfigurációs beállítások képe." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Magas rendelkezésre állású fürt (pacemaker) szolgáltatója

1. Válassza ki a **magas rendelkezésre állású fürtöt (pacemaker)** a legördülő listából. 

   > [!IMPORTANT]
   > A magas rendelkezésre állású fürt (pacemaker) szolgáltatójának konfigurálásához győződjön meg arról, hogy a ha_cluster_provider telepítve van az egyes csomópontokon. További információ: [Ha a fürt exportőre](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Adja meg a Prometheus-végpontot a formájában http://IP:9664/metrics . 
 
3. Adja meg a rendszerazonosító (SID), az állomásnév és a fürt nevét.

4. Ha elkészült, válassza a **szolgáltató hozzáadása**elemet. Szükség esetén folytassa a további szolgáltatók hozzáadását, vagy válassza a **felülvizsgálat + létrehozás** lehetőséget a telepítés befejezéséhez.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="A rendszerkép a HA-fürt pacemaker-szolgáltatójához kapcsolódó beállításokat jeleníti meg." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server szolgáltató

1. A Microsoft SQL Server-szolgáltató hozzáadása előtt futtassa a következő parancsfájlt SQL Server Management Studio egy olyan felhasználó létrehozásához, amely a szolgáltató konfigurálásához szükséges megfelelő engedélyekkel rendelkezik.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Válassza a **szolgáltató hozzáadása** lehetőséget, majd a legördülő listából válassza a **Microsoft SQL Server** lehetőséget. 

3. Töltse ki a mezőket a Microsoft SQL Serverhoz tartozó adatok használatával. 

4. Ha elkészült, válassza a **szolgáltató hozzáadása**elemet. Szükség esetén folytassa a további szolgáltatók hozzáadását, vagy válassza a **felülvizsgálat + létrehozás** lehetőséget a telepítés befejezéséhez.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="A rendszerkép a Microsoft SQL Server-szolgáltató hozzáadásával kapcsolatos információkat jeleníti meg." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Következő lépések

További információ az [SAP-megoldások Azure monitor](azure-monitor-overview.md)
