---
title: Azure Table Storage | Azure piactér
description: Az érdeklődők felügyeletének konfigurálása az Azure Table Storage-ban.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a53ed93813215655c4a165faa0bce36d9249e8e6
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227905"
---
# <a name="lead-management-instructions-for-table-storage"></a>A Table Storage szolgáltatással kapcsolatos érdeklődői kezelési utasítások

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Table Storage-t az értékesítési érdeklődők kezelésére. A Table Storage segítségével tárolhatja és módosíthatja az ügyfelek adatait.

## <a name="configure-table-storage"></a>A Table Storage konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/pricing/free-trial/).
1. Ha a fiókja aktív, jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. A Azure Portal hajtsa végre az alábbi lépéseket:  
    1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali ablaktáblán. Ekkor megnyílik az **új** panel.
    1. Az **új** panelen válassza a **Storage**lehetőséget. A jobb oldalon megnyílik egy **Kiemelt** lista.
    1. Válassza a **Storage-fiók**lehetőséget. Ezután kövesse a [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)című témakör utasításait.

    ![Azure-tárfiók létrehozása](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    További információ a Storage-fiókokról: gyors [útmutató oktatóanyagok](https://docs.microsoft.com/azure/storage/). A díjszabással kapcsolatos információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

1. Várjon, amíg megtörténik a Storage-fiók üzembe helyezése, ami általában néhány percet vesz igénybe. Ezután nyissa meg a fiókot a Azure Portal kezdőlapján: Válassza az **összes erőforrás** vagy az **összes erőforrás** megjelenítése lehetőséget a navigációs ablaktáblán.

    ![Hozzáférés az Azure Storage-fiókhoz](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. A Storage-fiók ablaktáblán másolja a kulcshoz tartozó Storage-fiókhoz tartozó kapcsolatok karakterláncát. Illessze be a Cloud Partner Portal Storage-fiókjának **kapcsolatok karakterlánc** mezőjébe.

    Példa a kapcsolatok karakterláncára:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure Storage-kulcs](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

A Table Storage-ban lévő információk megtekintéséhez [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) vagy hasonló eszköz használható. Az adatok onnan is exportálhatók.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Microsoft Flow használata a Table Storage szolgáltatással (nem*kötelező*)

A [Microsoft flow](https://docs.microsoft.com/flow/) használatával automatikusan küldhet értesítéseket, amikor egy érdeklődőt adnak hozzá a Table Storage-hoz. Ha nincs Microsoft Flow fiókja, [regisztráljon egy ingyenes fiókra](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Példa vezető értesítésre

Ebből a példából megtudhatja, hogyan hozhat létre egy alapszintű folyamatot. A folyamat óránként automatikusan elküld egy értesítő e-mailt, amikor új érdeklődőket adnak hozzá a Table Storage-hoz.

1. Jelentkezzen be Microsoft Flow-fiókjába.
1. A bal oldali navigációs ablaktáblán válassza a **saját folyamatok**lehetőséget.
1. A felső navigációs sávon válassza az **+ új**lehetőséget.  
1. A legördülő listában válassza a **+ Létrehozás üresből**lehetőséget.
1. A **folyamat létrehozása üresből**területen válassza a **Létrehozás üresből**lehetőséget.

   ![Új folyamat létrehozása üresből](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Az összekötők és eseményindítók keresése lapon válassza az eseményindítók lehetőséget.
1. Az **Eseményindítók**területen válassza az **Ismétlődés**lehetőséget.
1. Az **ismétlődési** ablakban tartsa meg az alapértelmezett **1** értéket az **intervallum**értéknél. A **frekvencia** legördülő listából válassza az **óra**lehetőséget.

   >[!NOTE] 
   >Ez a példa egy egyórás időközt használ. Az üzleti igényeknek leginkább megfelelő intervallumot és gyakoriságot is kiválaszthatja.

   ![1 órás gyakoriság beállítása ismétlődéshez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Válassza az **+ új lépés**lehetőséget.
1. Keresse meg a beolvasási **időt**, majd kattintson **a művelet kiválasztása lehetőségre**a **múltbeli időpont** beolvasása elemre.

    ![A "múltbeli idő lekérése" művelet megkeresése és kiválasztása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. A beolvasás **ideje** ablakban állítsa az **intervallumot** **1-re**.  Az **időegység** legördülő listából válassza az **óra**lehetőséget.
    >[!IMPORTANT] 
    >Győződjön meg arról, hogy az **intervallum** és az időegység megegyezik az ismétlődéshez konfigurált intervallummal és gyakorisággal (8. lépés).

    ![A múltbeli idő lekérése időintervallum beállítása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Bármikor ellenőrizheti, hogy az egyes lépések helyesen vannak-e konfigurálva: Válassza a folyamat- **ellenőrzési** lehetőséget a folyamat menüsorán.

A következő lépésben csatlakozik a Storage-táblához, és beállítja a feldolgozási logikát az új érdeklődők kezelésére.

1. A beolvasás **időpontja** lépés után válassza az **+ új lépés**lehetőséget, majd keressen az entitások beolvasása lehetőségre.
1. A **műveletek**területen válassza az entitások beolvasása, majd a **Speciális beállítások megjelenítése**lehetőséget.
1. Az **entitások** beolvasása ablakban töltse ki a következő mezőket:

   - **Tábla**: a tábla tárterületének neve. Az alábbi képen a "MarketPlaceLeads" látható:

     ![Egyéni érték választása az Azure-tábla neveként](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Lekérdezés szűrése**: Ha ezt a mezőt választja, a **beolvasás időpontja** ikon megjelenik egy előugró ablakban. Ha a lekérdezés szűréséhez időbélyegként szeretné használni ezt az értéket, válassza az **elmúlt idő** lehetőséget. A következő függvényt is beillesztheti a mezőbe:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![A szűrő lekérdezési funkciójának beállítása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Válassza az **új lépés** lehetőséget, ha hozzá szeretne adni egy feltételt az új érdeklődők számára a tábla tárterületének vizsgálatához.

   ![A tábla tárterületének vizsgálatához használja az "új lépés" kifejezést.](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. A **művelet kiválasztása** ablakban válassza a **műveletek**, majd a **feltétel vezérlőelem**elemet.

     ![Feltétel vezérlőelem hozzáadása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. A **feltétel** ablakban válassza az **érték kiválasztása**lehetőséget, majd az előugró ablakban válassza a **kifejezés** lehetőséget.
1. Illessze `length(body('Get_entities')?['value'])` be az ***FX*** mezőt. A függvény hozzáadásához kattintson **az OK gombra** . 



     ![Függvény hozzáadása a feltételhez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Állítsa be a feltétel eredményén alapuló műveletet.

    1. A Select **érték nagyobb, mint** a legördülő listából.
   1. Adja meg a **0** értéket.

     ![Művelet beállítása a feltétel eredményei alapján](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Ha a feltétel a "Ha nem" értékre van feloldva, akkor semmit sem kell tennie.

    Ha a feltételt a "Ha igen" értékre oldja fel, egy olyan műveletet indít el, amely összekapcsolja az Office 365-fiókját e-mailek küldéséhez:
   1. Válassza **a művelet hozzáadása**lehetőséget.
   1. Válassza **az E-mail küldése**lehetőséget.
   1. Az **E-mail küldése** ablakban adja meg az adatokat a következő mezőkben:

      - **To**: e-mail-cím mindenkinek, aki megkapja az értesítést.
      - **Tárgy**: az e-mailek tárgya. Példa: *Új érdeklődők!*
      - **Törzs**: az egyes e-mailekben szerepeltetni kívánt szöveg (nem kötelező). Az `body('Get_entities')?['value']` érdeklődői adatok beszúrásához is beillesztheti a függvényt.

        >[!NOTE] 
        >Az e-mail törzsébe további statikus vagy dinamikus adatpontokat is beszúrhat.

      ![E-mailek beállítása a vezető értesítéshez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Válassza a **Mentés** lehetőséget a folyamat mentéséhez. A Microsoft Flow automatikusan ellenőrzi a hibákat. Ha nincsenek hibák, a folyamat a mentés után elindul.

    Az alábbi képen látható egy példa arra, hogy a végső folyamatnak hogyan kell kinéznie.

    [![Végső flow-sorozatot](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Válassza ki a*képre a nagyításhoz.)

### <a name="manage-your-flow"></a>A folyamat kezelése

A folyamat futtatása után egyszerűen kezelheti a folyamatot. A folyamat teljes mértékben szabályozható. Leállíthatja például, szerkesztheti, megtekintheti a futtatási előzményeket, és lekérheti az elemzést. Az alábbi képen a folyamat-felügyeleti lehetőségek láthatók.

 ![Flow-felügyeleti lehetőségek](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

A folyamat addig fut, amíg ki nem választja a **flow kikapcsolása**lehetőséget.

Ha nem kap érdeklődői e-mail-értesítéseket, a Table Storage-hoz nem kerültek új érdeklődők.
A következő példához hasonló e-mail-üzenet jelenik meg, ha a folyamat meghiúsul:

 ![Sikertelen folyamat – e-mail-értesítés](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>További lépések

[Ügyfélérdeklődések konfigurálása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
