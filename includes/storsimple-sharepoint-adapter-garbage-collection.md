---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73182227"
---
Ebben az eljárásban a következőket kell tennie:

1. [Felkészülés a karbantartó végrehajtható fájl futtatására](#to-prepare-to-run-the-maintainer) .
2. [Készítse elő a tartalom-adatbázist és a Lomtárat az árva Blobok azonnali törléséhez](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Futtassa a karbantartó. exe fájlt](#to-run-the-maintainer).
4. [A tartalom-adatbázis és a Lomtár beállításainak visszaállítása](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Felkészülés a karbantartó futtatására
1. A webes előtér-kiszolgálón nyissa meg a SharePoint 2013 felügyeleti rendszerhéjt rendszergazdaként.
2. Navigáljon a mappa *rendszerindítási meghajtó*: \PROGRAM Files\Microsoft SQL távoli Blob Storage 10.50 \ karbantartó\.
3. Nevezze át a Microsoft. Rea. **SqlRemoteBlobs. karbantartó. exe. config fájlt** a **web. config**fájlra.
4. A `aspnet_regiis -pdf connectionStrings` paranccsal fejtheti vissza a web. config fájlt.
5. A visszafejtett web. config fájlban a `connectionStrings` csomópont alatt adja hozzá az SQL Server-példányhoz tartozó kapcsolati karakterláncot és a tartalom-adatbázis nevét. Tekintse meg a következő példát.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. A `aspnet_regiis –pef connectionStrings` web. config fájl ismételt titkosításához használja. 
7. Nevezze át a web. config fájlt a Microsoft. SqlRemoteBlobs. karbantartó. exe. config fájlra. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>A tartalom-adatbázis és a Lomtár előkészítése az árva Blobok azonnali törléséhez
1. Az SQL Management Studio SQL Server futtassa a következő frissítési lekérdezéseket a célként megadott tartalom-adatbázishoz: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. A webes előtér-kiszolgálón a **központi felügyelet**területen szerkessze a **webalkalmazás általános beállításait** a kívánt tartalom-adatbázishoz a Lomtár ideiglenes letiltásához. Ez a művelet a kapcsolódó webhelycsoportok esetében is üresen fogja hagyni a Lomtár használatát. Ehhez kattintson a **központi adminisztrációs** -> **alkalmazás-kezelés** -> **webalkalmazások (webalkalmazások kezelése)** -> **SharePoint-80** -> **általános Alkalmazásbeállítások**elemre. Állítsa ki a **Lomtár állapotát** **ki**értékre.
   
    ![Webalkalmazás általános beállításai](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>A karbantartó futtatása
* A webes előtér-kiszolgálón a SharePoint 2013 felügyeleti rendszerhéjban futtassa a karbantartót a következő módon:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Jelenleg csak `GarbageCollection` a művelet támogatott a StorSimple. Azt is vegye figyelembe, hogy a Microsoft. adat. SqlRemoteBlobs. karbantartó. exe esetében kiadott paraméterek kis-és nagybetűket érintenek. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>A tartalom-adatbázis és a Lomtár beállításainak visszaállítása
1. Az SQL Management Studio SQL Server futtassa a következő frissítési lekérdezéseket a célként megadott tartalom-adatbázishoz:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. A webes előtér-kiszolgálón a **központi felügyelet**lapon szerkessze a **webalkalmazás általános beállításait** a kívánt tartalom-adatbázishoz a Lomtár újbóli engedélyezéséhez. Ehhez kattintson a **központi adminisztrációs** -> **alkalmazás-kezelés** -> **webalkalmazások (webalkalmazások kezelése)** -> **SharePoint-80** -> **általános Alkalmazásbeállítások**elemre. A Lomtár állapotának beállítása **a**következőre:.

