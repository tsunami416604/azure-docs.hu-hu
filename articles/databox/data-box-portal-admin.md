---
title: Azure Data Box, Azure Data Box Heavy kezelése az Azure portalon keresztül | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure Portalt az Azure Data Box és az Azure Data Box Heavy felügyeletére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/07/2019
ms.author: alkohli
ms.openlocfilehash: 581f95bd813445d2cc9bd83d91917ea83f0bf04f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259993"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Az Azure Data Box és az Azure Data Box Heavy felügyeletéhez használja az Azure Portalon

Ez a cikk az Azure Data Box ra és az Azure Data Box Heavy-re egyaránt vonatkozik. Ez a cikk ismerteti az Azure Data Box-eszközön elvégezhető összetett munkafolyamatokat és felügyeleti feladatokat. A Data Box-eszközt az Azure Portalon vagy a helyi webes felhasználói felületen keresztül kezelheti.

A cikk az Azure Portalon végrehajtható feladatokra összpontosít. Az Azure Portal használatával kezelheti a rendeléseket, kezelheti a Data Box-eszközt, és nyomon követheti a rendelés állapotát a befejezéshez.


## <a name="cancel-an-order"></a>Rendelés visszavonása

A rendelések azok feladása utáni visszavonása mellett számos különféle okból döntet. A rendelést csak annak feldolgozását megelőzően vonhatja vissza. A megrendelés feldolgozása és a Data Box eszköz előkészítése után nem lehet visszavonni a megrendelést.

A rendelés visszavonásához kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Megszakítás** menüpontra.

    ![Rendelés visszavonása 1](media/data-box-portal-admin/cancel-order1.png)

2.  Adja meg a rendelés visszavonásának okát.  

    ![Rendelés visszavonása 2](media/data-box-portal-admin/cancel-order2.png)

3.  Miután visszavonta a rendelést, a portálon a rendelés állapota frissül a **Megszakítva** állapotra.

## <a name="clone-an-order"></a>Rendelés klónozása

A klónozás bizonyos helyzetekben hasznosnak bizonyulhat. Például a felhasználó egyes adatokat már átvitt a Data Box használatával. Ahogy egyre több adat jön létre, szükség van egy másik Data Box-eszközre, amely továbbítja ezeket az adatokat az Azure-ba. Ebben az esetben egyszerűen klónozhatja az előző rendelést.

A rendelések klónozásához kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Klónozás** menüpontra. 

    ![Rendelés klónozása 1](media/data-box-portal-admin/clone-order1.png)

2.  A rendelés részletei változatlanok maradnak. A rendelés neve az eredeti rendelés neve lesz a *-Klón* utótaggal kiegészítve. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy átolvasta az adatvédelmi információkat. Kattintson **a Létrehozás gombra.**

A klón létrehozása néhány percet vesz igénybe, és a portálon megjelenik az új rendelés.


## <a name="delete-order"></a>Rendelés törlése

A rendeléseket azok teljesítését követően esetleg törölni szeretné. A rendelés tartalmazza az Ön személyes adatait, úgymint a nevét, a címét és a kapcsolattartási adatait. A rendelés törlésekor a személyes adatok törölve lesznek.

Csak a teljesített vagy visszavont rendeléseket törölheti. A rendelések törléséhez kövesse az alábbi lépéseket.

1. Lépjen a **Minden erőforrás** menüpontra. Keresse meg a rendelést.

2. Kattintson a törölni kívánt rendelésre, és lépjen az **Áttekintés** felületre. A parancssoron kattintson a **Törlés** elemre.

    ![Data Box-rendelés törlése 1](media/data-box-portal-admin/delete-order1.png)

3. Ha a rendszer kéri, írja be a rendelés nevét a rendelés törlésének megerősítéséhez. Kattintson a **Törlés** gombra.

## <a name="download-shipping-label"></a>Fuvarlevélcímke letöltése

Ha a Data Box E-ink kijelzője nem működik és nem jeleníti meg a visszaküldési fuvarlevélcímkét, akkor azt le kell töltenie a portálról. A Data Box Heavy nem jelenik meg E-ink, ezért ez a munkafolyamat nem vonatkozik a Data Box Heavy-ra.

A fuvarlevélcímkék letöltéséhez kövesse az alábbi lépéseket.

1.  Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra. Ez a lehetőség csak az eszköz kézbesítését követően érhető el. 

    ![Fuvarlevélcímke letöltése](media/data-box-portal-admin/download-shipping-label.png)

2.  Ezzel letölti a következő fuvarlevélcímkét a visszaküldéshez. Mentse a címkét, és nyomtassa ki. Hajtsa be és helyezze be a címkét a készülék tiszta hüvelyébe. Győződjön meg arról, hogy a címke jól látható. Távolítsa el a matricákat, amelyek az előző szállítás során kerültek az eszközre.

    ![Példa a fuvarlevélcímkére](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Szállítási cím szerkesztése

Előfordulhat, hogy a rendelés feladását követően módosítania kell a szállítási címet. Ezt csak az eszköz feladásáig teheti meg. Az eszköz feladását követően ez a lehetőség már nem érhető el.

A rendelés szerkesztéséhez kövesse az alábbi lépéseket.

1. Lépjen a **Rendelés részletei > Szállítási cím szerkesztése** menüpontra.

    ![Szállítási cím szerkesztése 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Szerkesztheti és érvényesítheti a szállítási címet, majd mentheti a változásokat.

    ![Szállítási cím szerkesztése 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Értesítési adatok szerkesztése

Lehet, hogy módosítani szeretné, hogy mely felhasználók kapjanak értesítő e-maileket a rendelés állapotáról. Például előfordulhat, hogy valamelyik felhasználót értesíteni kell, ha az eszközt kiszállítják vagy már átvették. Előfordulhat, hogy egy másik felhasználót tájékoztatni kell, ha az adatmásolat befejeződött, így ellenőrizhetik, hogy az adatok az Azure storage-fiókban vannak-e, mielőtt a forrásból kikellene őket választani. Ilyen esetekben szerkesztheti az értesítési adatokat.

Az értesítési adatok szerkesztéséhez kövesse az alábbi lépéseket.

1. Lépjen a **Rendelés részletei > Értesítési adatok szerkesztése** menüpontra.

    ![Értesítési adatok szerkesztése 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Itt szerkesztheti az értesítési adatokat, majd mentheti a változásokat.
 
    ![Értesítési adatok szerkesztése 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Rendelés előzményeinek letöltése

A Data Box-rendelés befejezése után az eszközlemezeken található adatok törölve lesznek. Ha az eszközök adatainak törlése befejeződött, a rendelési előzmények letölthetők az Azure Portalon.

A rendelési előzmények letöltéséhez kövesse az alábbi lépéseket.

1. A Data Box-rendelésben lépjen az **Áttekintés** lapra. Ellenőrizze, hogy a rendelés befejeződött-e. Ha a rendelés végrehajtása és az eszközök adatainak törlése befejeződött, lépjen a **Rendelés részletei** területre. Elérhető a **Rendelés előzményeinek letöltése** lehetőség.

    ![Rendelés előzményeinek letöltése](media/data-box-portal-admin/download-order-history-1.png)

2. Kattintson a **Rendelés előzményeinek letöltése** elemre. A letöltött előzmények között a futárcégek nyomkövetési naplóinak rekordjai találhatóak. A Data Box Heavy két csomópontjának megfelelő két naplókészlet lesz. A napló aljára görgetve a következő hivatkozásokat láthatja:
    
   - A **másolási naplók** a Data Boxból az Azure-tárfiókba történő adatmásolás során hibásként jelzett fájlok listáját tartalmazzák.
   - **Naplónaplók** – információkat tartalmaznak arról, hogyan lehet be-és hozzáférés megosztások az adatmezőn, ha az Azure datacenter en kívül található.
   - A **darabjegyzékfájlok****a szállítás előkészítése** során letölthető fájlok listáját (más néven a fájljegyzéket) tartalmazzák a fájlok neveivel, méretével és a fájlok ellenőrzőösszegeivel.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
       Name                                               : DataBoxTestOrder                              
       StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
       DeviceType                                         : DataBox                                           
       -------------------
       Data Box Activities
       -------------------
       Time(UTC)                 | Activity                       | Status          | Description  
       
       10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
       11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
       11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
       11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
       11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
       11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
       11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
       11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
       11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
       11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
       11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
       11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
       11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
       11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
       11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
       11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
       1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
       1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
       1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
       1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
       1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
       1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
       1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
       1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
       1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
       1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
       1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
       1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
       1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
       1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
       1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
       1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
       1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
       1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
       2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
       2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
       2/4/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

       ------------------
       Data Box Log Links
       ------------------

       Account Name         : Gus                                                       
       Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
       Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
       BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
       ```
     Ezután a tárfiókba lépve megtekintheti a másolási naplókat.

![Naplók a tárfiókokban](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Emellett a felügyeleti naplók láncát, azaz az auditnaplókat és a darabjegyzékfájlokat is megtekintheti.

![Naplók a tárfiókokban](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Rendelés állapotának megtekintése

Az eszköz állapotának a portálon való változásáról a rendszer értesítést küld e-mailben.

|Rendelés állapota |Leírás |
|---------|---------|
|Megrendelve     | A rendelés sikeresen fel lett adva. <br>Ha az eszköz elérhető, a Microsoft kiválaszt egy eszközt a szállításhoz, és előkészíti. <br> Ha az eszköz nem érhető el azonnal, a rendelés akkor lesz feldolgozva, amikor az eszköz elérhetővé válik. A rendelés feldolgozása néhány napot, de akár hónapokat is igénybe vehet. Ha a rendelés nem hajtható végre 90 napon belül, akkor a rendszer visszavonja azt, és e-mailben értesíti Önt.         |
|Feldolgozva     | A rendelés feldolgozása befejeződött. Az eszköz szállításra való előkészítése a rendelésnek megfelelően megtörténik az adatközpontban.         |
|Feladva     | A rendelés fel lett adva. A portálon a rendelésnél feltüntetett követési azonosítóval követheti nyomon a szállítást.        |
|Kézbesítve     | A rendelés ki lett kézbesítve a rendelésben megadott címre.        |
|Szállítás alatt     |A visszaküldött csomagot a szállítmányozó átvette és ellenőrizte.         |
|Megérkezett     | Az eszközt átvették és ellenőrizték az Azure-adatközpontban. <br> Miután a szállítmányt megvizsgálták, elkezdődik az eszköz feltöltése.      |
|Adatok másolása     | Az adatok másolása folyamatban van. Kövesse nyomon a rendelés másolásának folyamatát az Azure Portalon. <br> Várjon, amíg az adatok másolása befejeződik. |
|Befejezve       |A rendelés sikeresen teljesítve lett.<br> Ellenőrizze, hogy az adatok elérhetők-e az Azure-ban, mielőtt törölné a helyszíni adatokat a kiszolgálókról.         |
|Befejeződött, hibákkal| Az adatok másolása befejeződött, azonban hibák jelentkeztek a másolás során. <br> Tekintse át a másolási naplókat az Azure Portalon megadott útvonalon. Tekintsemeg [a másolási naplók példáit, ha a feltöltés hibákkal fejeződött be.](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-errors)   |
|Figyelmeztetésekkel kiegészítve| Az adatok másolása befejeződött, de az adatok módosultak. Az adatok nem kritikus blob- vagy fájlnév-hibákat tartalmaznak, amelyeket a fájl- vagy blobnevek módosításával javítottak ki. <br> Tekintse át a másolási naplókat az Azure Portalon megadott útvonalon. Jegyezze fel az adatok módosításait. Tekintsemeg [a másolási naplók példáit, ha a feltöltés figyelmeztetésekkel fejeződik be.](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-warnings)   |
|Megszakítva            |A rendelés vissza lett vonva. <br> Vagy visszavonta a rendelést, vagy hiba történt, és a szolgáltatás vonta vissza. Ha a rendelés nem hajtható végre 90 napon belül, akkor a rendszer visszavonja azt, és e-mailben értesíti Önt.     |
|A fölöslegessé vált elemek eltávolítása | Az eszközlemezeken található összes adat törölve lesz. Az eszköz adatainak törlése akkor fejeződik be, amikor a rendelés előzményei letölthetővé válnak az Azure Portalon.|



## <a name="next-steps"></a>További lépések

- További információ az [Adatdoboz- és adatdoboz-hibák kal kapcsolatos problémák elhárításáról.](data-box-troubleshoot.md)
