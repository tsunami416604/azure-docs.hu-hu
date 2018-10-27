---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0d2fde4ce80e09db19f65134697ba0ba379e1824
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166212"
---
<!--author=SharS last changed: 9/17/15-->

Ebben az eljárásban fogja végrehajtani:

1. [Készítse elő a karbantartó végrehajtható fájl futtatásához](#to-prepare-to-run-the-maintainer) .
2. [A tartalom-adatbázist és a Lomtár előkészítése a megfelelő árva Blobok azonnali törlésre](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Futtassa a Maintainer.exe](#to-run-the-maintainer).
4. [A tartalom-adatbázist és a Lomtár beállításainak visszaállítása](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>A karbantartó futtatni előkészítése
1. A webes kezelőfelületet futtató kiszolgálón nyissa meg rendszergazdaként a SharePoint 2013 felügyeleti rendszerhéjat.
2. Lépjen abba a mappába *rendszerindítási meghajtó*: az SQL távoli Blob Storage 10.50\Maintainer \Program Files\Microsoft\.
3. Nevezze át **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** való **web.config**.
4. Használat `aspnet_regiis -pdf connectionStrings` visszafejteni a web.config fájlt.
5. A visszafejtett web.config fájl mellett a `connectionStrings` csomópont, az SQL server-példány és a tartalom-adatbázis neve a kapcsolati karakterlánc hozzáadása. Tekintse meg a következő példát.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Használat `aspnet_regiis –pef connectionStrings` újratitkosítása a web.config fájlt. 
7. Nevezze át a web.config Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>A tartalom előkészítése, adatbázis, és azonnal törli a Lomtár árva Blobok
1. Az SQL Server az SQL Management Studióban futtassa a következő frissítés lekérdezéseket a cél tartalom-adatbázist: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. A webes kezelőfelületet futtató kiszolgálón alatt **központi adminisztrációs**, szerkesztheti a **webalkalmazás általános beállítások** a kívánt tartalom-adatbázist ideiglenesen letilthatja a Lomtár. Ez a művelet is üres a Lomtár, az összes webhelycsoportban vonatkozó. Ehhez kattintson **központi adminisztrációs** -> **Alkalmazáskezelés** -> **webalkalmazások (kezelés webes alkalmazások)**  ->  **SharePoint – 80-as** -> **általános Alkalmazásbeállítások**. Állítsa be a **Recycle Bin állapot** való **OFF**.
   
    ![Webes alkalmazás általános beállításai](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>A karbantartó futtatni
* A webes kezelőfelületet futtató kiszolgálón a SharePoint 2013 felügyeleti rendszerhéjban futtassa a karbantartó futtatni a következő:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Csak a `GarbageCollection` művelet StorSimple jelenleg támogatott. Azt is vegye figyelembe, hogy a kiadott Microsoft.Data.SqlRemoteBlobs.Maintainer.exe paraméterei kis-és nagybetűket. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>A tartalom-adatbázist és a Lomtár beállításainak visszaállítása
1. Az SQL Server az SQL Management Studióban futtassa a következő frissítés lekérdezéseket a cél tartalom-adatbázist:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. A webes kezelőfelületet futtató kiszolgálón a **központi adminisztrációs**, szerkesztheti a **webalkalmazás általános beállítások** számára kívánja újból engedélyezni a Lomtár a kívánt tartalom-adatbázist. Ehhez kattintson **központi adminisztrációs** -> **Alkalmazáskezelés** -> **webalkalmazások (kezelés webes alkalmazások)**  ->  **SharePoint – 80-as** -> **általános Alkalmazásbeállítások**. A Lomtár Bin állapotának beállítása **ON**.

