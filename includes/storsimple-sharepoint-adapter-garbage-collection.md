---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73182227"
---
Ebben az eljárásban a következőkre nyílik szüksége:

1. [Felkészülés a Karbantartó végrehajtható fájl futtatására.](#to-prepare-to-run-the-maintainer)
2. [Készítse elő a tartalom-adatbázist és a Lomtárt az árva BLOB-k azonnali törlésére.](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)
3. [Futtassa az Maintainer.exe futtassa.](#to-run-the-maintainer)
4. [A tartalom-adatbázis és a Lomtár beállításainak visszaállítása.](#to-revert-the-content-database-and-recycle-bin-settings)

#### <a name="to-prepare-to-run-the-maintainer"></a>Felkészülés a Karbantartó futtatására
1. A webes előtér-kiszolgálón nyissa meg rendszergazdaként a SharePoint 2013 felügyeleti rendszerhéjat.
2. Keresse meg a mappa *rendszerindító meghajtóját*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Nevezze át a **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config nevet** **a web.config**fájlba.
4. A `aspnet_regiis -pdf connectionStrings` web.config fájl visszafejtésére használható.
5. A visszafejtett web.config fájlban `connectionStrings` a csomópont alatt adja hozzá az SQL-kiszolgálópéldány kapcsolati karakterláncát és a tartalom-adatbázis nevét. Tekintse meg a következő példát.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. A `aspnet_regiis –pef connectionStrings` web.config fájl újratitkosítására használható. 
7. Nevezze át a web.config nevet a Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config névre. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>A tartalom-adatbázis és a Lomtár előkészítése az árva BLOB-k azonnali törléséhez
1. Az SQL Server sql management studiójában futtassa a következő frissítési lekérdezéseket a céltartalom-adatbázishoz: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. A webes előtér-kiszolgálóközponti **felügyelet**csoportban a **webalkalmazás általános beállításainak** szerkesztésével ideiglenesen letilthatja a Lomtárat. Ez a művelet kiüríti a Lomtárat a kapcsolódó webhelycsoportok esetében is. Ehhez kattintson a **Központi felügyelet** -> **alkalmazáskezelő** -> **webalkalmazások (Webalkalmazások kezelése)** -> **SharePoint – 80** -> Általános**alkalmazásbeállítások parancsra.** Állítsa a **Lomtár állapotát** **OFF**állásra.
   
    ![Webalkalmazás általános beállításai](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>A Karbantartó futtatása
* A webes előtér-kiszolgálón, a SharePoint 2013 felügyeleti rendszerhéjban futtassa a Karbantartót az alábbiak szerint:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Jelenleg `GarbageCollection` csak a StorSimple támogatja a műveletet. Azt is vegye figyelembe, hogy a Microsoft.Data.SqlRemoteBlobs.Maintainer.exe számára kiadott paraméterek ben a kis- és nagybetűk et érzékenyen ki. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>A tartalom-adatbázis és a Lomtár beállításainak visszaállítása
1. Az SQL Server sql management studiójában futtassa a következő frissítési lekérdezéseket a céltartalom-adatbázishoz:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. A webes előtér-kiszolgálóközponti **felügyelet**területén a Lomtár újbóli engedélyezéséhez szerkesztheti a **webalkalmazás általános beállításait** a kívánt tartalom-adatbázishoz. Ehhez kattintson a **Központi felügyelet** -> **alkalmazáskezelő** -> **webalkalmazások (Webalkalmazások kezelése)** -> **SharePoint – 80** -> Általános**alkalmazásbeállítások parancsra.** Állítsa a Lomtár állapotát **BE állásra.**

