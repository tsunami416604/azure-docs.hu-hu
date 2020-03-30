---
title: Azure Table tároló | Azure Piactér
description: Konfigurálja az érdeklődőkezelést az Azure Table storage-ban.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280354"
---
# <a name="lead-management-instructions-for-table-storage"></a>A tábla tárolására vonatkozó ólomkezelési utasítások

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Table storage értékesítési érdeklődők kezelésére. A táblatárolás segít a vevői adatok tárolásában és módosításában.

## <a name="configure-table-storage"></a>Táblatároló konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [hozzon létre egy ingyenes próbafiókot.](https://azure.microsoft.com/pricing/free-trial/)
1. Miután a fiókja aktív, jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Az Azure Portalon kövesse az alábbi lépéseket:  
    1. Válassza **a +Erőforrás létrehozása lehetőséget** a bal oldali ablaktáblában. Megnyílik **az Új** ablaktábla.
    1. Az **Új** ablaktáblán válassza a **Tárolás**lehetőséget. **A Kiemelt** lista a jobb oldalon nyílik meg.
    1. Válassza **a Tárfiók lehetőséget.** Ezután kövesse a [Tárfiók létrehozása című útmutatóutasításait.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    ![Azure Storage-fiók létrehozása](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    A tárfiókokról további információkért olvassa [el a Rövid útmutatók at.](https://docs.microsoft.com/azure/storage/) A díjszabási információkért lásd: [Azure storage-díjszabás.](https://azure.microsoft.com/pricing/details/storage/)

1. Várjon, amíg a tárfiók ki van építve, ami általában néhány percet vesz igénybe. Ezután a fiók az Azure Portal kezdőlapjáról érhető el: válassza **az összes erőforrás vagy** az összes **erőforrás** megtekintése lehetőséget a navigációs ablakban.

    ![Az Azure storage-fiók elérése](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. A tárfiók ablaktábláján másolja a tárfiók kapcsolati karakterláncát a kulcshoz. Illessze be a **Kapcsolati karakterlánc** mezőbe a felhőpartner-portálon lévő tárfiókhoz.

    Példa kapcsolati karakterláncra:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure tárolási kulcs](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Használhatja [az Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) vagy egy hasonló eszköz a táblatárolóban lévő adatok megtekintéséhez. Az adatokat is exportálhatja belőle.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>A Microsoft Flow használata táblázattárolóval (*nem kötelező*)

A [Microsoft Flow](https://docs.microsoft.com/flow/) segítségével automatikusan küldhet értesítéseket, ha egy érdeklődőt ad hozzá a táblatárolóhoz. Ha nem rendelkezik Microsoft Flow-fiókkal, [regisztráljon egy ingyenes fiókot.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Példa érdeklődőkeresési értesítésre

Ez a példa bemutatja, hogyan hozhat létre egy alapfolyamatot. A folyamat automatikusan küld egy e-mail értesítést óránként, amikor új érdeklődőket ad nak hozzá a táblatárolóhoz.

1. Jelentkezzen be A Microsoft Flow-fiókba.
1. A bal oldali navigációs ablakban válassza a **Saját folyamatok lehetőséget.**
1. A felső navigációs sávon válassza a **+Új**lehetőséget.  
1. A legördülő listában válassza a **+Létrehozás üresből**lehetőséget.
1. A **Folyamat létrehozása üres ből**csoportban válassza a **Létrehozás üres ből**lehetőséget.

   ![Új folyamat létrehozása üres](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Az összekötők és eseményindítók keresési lapján válassza az **Eseményindítók**lehetőséget.
1. Az **Eseményindítók csoportban**válassza **az Ismétlődés**lehetőséget.
1. Az **Ismétlődés** ablakban tartsa meg az **1** alapértelmezett beállítást az **Intervallum**mezőben. A **Gyakoriság** legördülő listában válassza az **Óra**lehetőséget.

   >[!NOTE] 
   >Ez a példa egyórás intervallumot használ. De kiválaszthatja az üzleti igényeinek leginkább megfelelő intervallumot és gyakoriságot.

   ![1 órás gyakoriság beállítása az ismétlődéshez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Válassza **a +Új lépés lehetőséget.**
1. Keressen a **Korábbi idő betöltése**mezőbe, és válassza a Művelet kiválasztása csoportban az **Elmúlt idő bekerülése** lehetőséget. **Choose an action**

    ![A "get past time" művelet megkeresése és kiválasztása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. A **Get past time** ablakban állítsa az Intervallum ot **1-re.** **Interval**  Az **Időegység** legördülő listában válassza az **Óra**lehetőséget.
    >[!IMPORTANT] 
    >Győződjön meg arról, hogy az **Időköz** és **az Időegység** megegyezik az ismétlődéshez beállított időközzel és gyakorisággal (8. lépés).

    ![A bekerülési időintervallum beállítása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >A folyamatot bármikor ellenőrizheti, hogy az egyes lépéseket megfelelően konfigurálta-e: Válassza a **Folyamat ellenőrzőelemet** a Folyamat menüsorból.

A következő lépésekben csatlakozhat a tárolótáblához, és beállíthatja a feldolgozási logikát az új érdeklődők kezeléséhez.

1. Az **Elmúlt idő bekerülése** lépés után válassza az **+Új lépés**lehetőséget, majd keresse meg az **Entitások betöltése**lehetőséget.
1. A **Műveletek**csoportban jelölje be az **Entitások bekerülése**lehetőséget, majd válassza **a Speciális beállítások megjelenítése**lehetőséget.
1. Az **Entitások bekerülése** ablakban töltse ki a következő mezőket:

   - **Táblázat:** a táblatároló neve. Az alábbi képen a "MarketPlaceLeads" szerepel:

     ![Egyéni érték választása az Azure-tábla nevéhez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Lekérdezés szűrése:** Ha ezt a mezőt választja, a **Korábbi idők bekerülése** ikon egy előugró ablakban jelenik meg. Válassza **az Elmúlt idő** lehetőséget, ha ezt az értéket időbélyegként szeretné használni a lekérdezés szűréséhez. Vagy beillesztheti a következő függvényt a mezőbe:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![A szűrőlekérdezési függvény beállítása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Válassza **az Új lépés** lehetőséget, ha feltételt szeretne hozzáadni az új érdeklődők táblatárolójának vizsgálatához.

   ![Az "Új lépés" használatával feltétel hozzáadása az asztaltároló beszkafétéihoz](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. A **Művelet kiválasztása** ablakban válassza a **Műveletek**lehetőséget, majd a **Feltételvezérlés**lehetőséget.

     ![Feltételvezérlő hozzáadása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. A **Feltétel** ablakban válassza az **Érték kiválasztása**lehetőséget, majd az előugró ablakban válassza a **Kifejezés** lehetőséget.
1. Illessze `length(body('Get_entities')?['value'])` be az ***fx*** mezőbe. A függvény hozzáadásához válassza az **OK gombot.** 



     ![Függvény hozzáadása a feltételhez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Állítsa be a végrehajtandó műveletet a feltétel eredménye alapján.

    1. A kijelölés **nagyobb, mint** a legördülő listából.
   1. Adja meg a **0** értéket értékként.

     ![Művelet beállítása feltételeredmények alapján](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Ha a feltétel úgy dönt, hogy "Ha nem", ne tegyen semmit.

    Ha a feltétel "Ha igen" lesz, az Office 365-fiókot e-mail küldéséhez kapcsolódó művelet et indít:
   1. Válassza a **Művelet hozzáadása** lehetőséget.
   1. Válassza az **E-mail küldése** lehetőséget.
   1. Az **E-mail küldése** ablakban adja meg az adatokat a következő mezőkben:

      - **Címzett**: egy e-mail cím mindenkinek, aki megkapja az értesítést.
      - **Tárgy**: az e-mail tárgya. Például: *Új érdeklődők!*
      - **Törzs:** az egyes e-mailekben szerepelni kívánt szöveg (nem kötelező). Az érdeklődőadatok `body('Get_entities')?['value']` beszúrásához beillesztendő funkcióként is.

        >[!NOTE] 
        >További statikus vagy dinamikus adatpontokat szúrhat be az e-mail törzsében.

      ![E-mail beállítása érdeklődőértesítéshez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. A folyamat mentéséhez válassza a **Mentés** lehetőséget. A Microsoft Flow automatikusan teszteli a hibákat. Ha nincsenek hibák, a folyamat a mentés után elindul.

    Az alábbi képen egy példa látható arra, hogyan kell kinéznie a végső folyamatnak.

    [![Végső áramlási sorrend](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*A nagyításhoz jelölje ki a képet.)*

### <a name="manage-your-flow"></a>A folyamat kezelése

A működés után könnyen kezelheti az áramlást. Ön teljes mértékben ellenőrzése alatt a flow. Például leállíthatja, szerkesztheti, megtekintheti a futtatási előzményeket, és elemzéseket kaphat. Az alábbi képen látható a folyamatkezelési beállítások.

 ![Folyamatkezelési lehetőségek](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

A folyamat addig fut, amíg a Forgalom kikapcsolása lehetőséget nem **választja.**

Ha nem kap érdeklődői e-mail értesítéseket, nem adott hozzá új érdeklődőket a táblázattárolóhoz.
Folyamathiba esetén a következő példához hasonló e-mailt fog kapni:

 ![Folyamatsikertelen e-mail értesítés](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>További lépések

[Ügyfélérdeklődések konfigurálása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
