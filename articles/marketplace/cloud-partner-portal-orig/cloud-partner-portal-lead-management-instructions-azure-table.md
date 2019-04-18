---
title: Az Azure Table |} A Microsoft Docs
description: Az Azure Table érdeklődő felügyeletének konfigurálásához.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a8ae3ab71b258d92d9761cc813b168717e44d82
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878009"
---
# <a name="lead-management-instructions-for-azure-table"></a>Az Azure Table utasítások felügyeleti vezethet

Ez a cikk ismerteti a konfigurálása az Azure Table potenciális vásárlók tárolásához. Az Azure Table lehetővé teszi, hogy tárolhatja, és testre szabhatja a vásárlói adatokat.

## <a name="to-configure-azure-table"></a>Az Azure Table konfigurálása

1.  Ha nem rendelkezik Azure-fiókkal, akkor [hozzon létre egy ingyenes próbafiókot](https://azure.microsoft.com/pricing/free-trial/).

2.  Miután az Azure-fiókjával aktív, jelentkezzen be a [az Azure portal](https://portal.azure.com).
3.  Az Azure Portalon hozzon létre egy tárfiókot. A következő képernyőfelvétel bemutatja, hogyan hozzon létre egy tárfiókot. További információ a storage szolgáltatás díjszabása: [storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

    ![Az Azure storage-fiók létrehozásának lépései](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  A kulcs a tárfiók kapcsolati sztringje másolja és illessze be azt a **Tárfiók kapcsolati Sztringje** mezőjében a Cloud Partner portálra. A kapcsolati karakterlánc például `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`
    
    ![Azure storage-kulcs](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Használhat [az Azure storage explorer](https://azurestorageexplorer.codeplex.com/) vagy más eszköz a storage-táblába az adatok megtekintéséhez. Az Azure-táblában lévő adatokat exportálhatja is.
adatok.

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>**(Nem kötelező)**  Használja a Microsoft Flow az Azure-tábla

Használhat [Microsoft Flow](https://docs.microsoft.com/flow/) automatizálhatja az értesítéseket, amikor érdeklődőt kerül az Azure-tábla. Ha nem olyan fiókkal, használhatja [regisztrálhat egy ingyenes fiókot](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Értesítési példában vezethet

Ebben a példában használja útmutatóként egy egyszerű folyamatot, amely automatikusan egy e-mailben értesítést küld, amikor egy új érdeklődővel bővül az Azure-tábla létrehozásához. Ebben a példában úgy állít be, egy ismétlődési érdeklődők adatait óránként küld, ha a table storage frissül.

1. Jelentkezzen be a Microsoft Flow-fiók.
2. A bal oldali navigációs sávján válassza **saját folyamatok**.
3. A felső navigációs sávban válassza **+ új**.  
4. A legördülő listából válassza ki a **+ üres folyamat létrehozása**
5. Hozzon létre egy folyamatot az alapoktól, válassza a **üres folyamat létrehozása**.

   ![Hozzon létre egy új üres](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Az összekötők és eseményindítók keresése oldal, válassza ki a **eseményindítók**.
7. A **eseményindítók**válassza **ismétlődési**.
8. Az a **ismétlődési** ablakban megtartani az alapértelmezett beállítás 1- **időköz**. Az a **gyakorisága** legördülő listában válassza **óra**.

   >[!NOTE] 
   >Bár ebben a példában egy 1 órás időszakban, kiválaszthatja a időközét és gyakoriságát, hogy a legjobb, az üzleti igényeknek megfelelően.

   ![Állítsa be 1 órás ismétlődési gyakorisága](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Válassza ki **+ új lépés**.
10. Keresse meg a "Get múltbeli idő", és válassza **múltbeli időpont beolvasása** műveletek alatt. 

    ![Keresse meg és válassza ki a get múltbeli idő művelet](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. Az a **múltbeli időpont beolvasása** ablakban állítsa be a **időköz** 1-re.  Az a **időegység** legördülő listában válassza **óra**.
    >[!IMPORTANT] 
    >Győződjön meg arról, hogy az időköz és az idő egysége időközét és gyakoriságát ismétlődési konfigurált megegyezik-e.

    ![Lejárt az idő alatt set get](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Ellenőrizheti a folyamat, és ellenőrizze, hogy az egyes lépések megfelelően van konfigurálva. A folyamat ellenőrzéséhez válassza **folyamat ellenőrző** a Flow menüsoron.

A következő készletét a lépéseket is kapcsolódni az Azure-tábla, és állítsa be a feldolgozó logika kezelni az új érdeklődők.

1. Múltbeli idő lépés Get után válassza ki a **+ új lépés**, és keressen a "Get-entitások".
2. Alatt **műveletek**válassza **kéri le az entitást**, majd válassza ki **speciális beállítások megjelenítése**.
3. Az a **kéri le az entitást** ablakban adjon meg információt a következő mezőket:

   - **Tábla** – adja meg az Azure Table Storage nevét. "MarketPlaceLeads" is meg kell adni ehhez a példához a következő képernyőfelvétel-készítés mutatja a rendszer kéri. 

     ![Válasszon egy egyéni értéket az Azure-tábla neve](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Szűrőlekérdezés** – ebben a mezőben kattintson, és a egy felugró ablakban jelenik meg a Get múltbeli idő ikonra. Válassza ki **múltbeli időpont** időbélyegzőként Ez a lekérdezés szűrése használatára. Ez a függvény azt is megteheti, beillesztheti az mezőbe: `gt datetime'@{body('Get_past_time')}'`

     ![Szűrő lekérdezés függvény beállítása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Válassza ki **új lépés** vizsgálata az Azure-tábla új érdeklődők feltétel hozzáadása.

   ![Új lépés használja az Azure table vizsgálata feltétel hozzáadása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. Az a **válasszon ki egy műveletet** ablakban válassza **műveletek**, majd válassza ki a **feltétel** vezérlő.

     ![A feltétel vezérlőelem felvétele](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. Az a **feltétel** ablakban válassza ki a **válasszon egy értéket** mezőben, majd válassza ki **kifejezés** az előugró ablakban.
7. Beillesztés `length(body('Get_entities')?['value'])` be a ***fx*** mező. Válassza ki **OK** hozzáadni ezt a funkciót. A feltétel beállításának befejezéséhez:

   - Jelölje ki a "nagyobb, mint" lehetőséget a legördülő listából.
   - Adjon meg 0 értéket 

     ![Függvény hozzáadása a feltételhez](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Állítsa be a művelet a feltétel eredménye alapján érvénybe.

     ![A feltétel eredménye alapján a művelet beállítása](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Ha a feltétel **Ha nincsenek**, nincs teendője. 
10. Ha a feltétel **Ha Igen**, egy műveletet, amely kapcsolódik az e-mail küldése az Office 365-fiókjába. Válassza ki **művelet hozzáadása**.
11. Válassza ki **e-mail küldése**. 
12. Az a **e-mail küldése** ablakban adjon meg információt a következő mezőket:

    - **A** – adja meg egy e-mail-címet, amely ezt az értesítést fog kapni minden tagja számára.
    - **Tulajdonos** – adja meg az e-mail tárgyát. Példa: Új érdeklődők!
    - **Törzs**:   Adja hozzá a szöveget, amely tartalmazza minden e-mailben (opcionális), és illessze be a szervezet `('Get_entities')?['value']` függvényében, érdeklődő információit.

      >[!NOTE] 
      >Ez az e-mail szövegtörzséhez további statikus vagy dinamikus adatok pontok beszúrásához.

       ![Állítsa be a érdeklődő értesítési e-mailben](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Válassza ki **mentése** a folyamat mentéséhez. Microsoft Flow automatikusan teszteli a folyamatot a hibákat. Ha nincsenek hibák, a folyamat elindul, Miután elmentette futtatása.

A következő képernyőfelvétel-készítés azt szemlélteti, hogyan kell kinéznie a végső folyamatot.

 ![A folyamat utolsó feladatütemezési](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

### <a name="managing-your-flow"></a>A folyamat kezelése

A folyamat kezelése futás után sem ördöngösség.  Teljes körű, a folyamat rendelkezik. Például akkor is állítsa le, szerkeszthetik, tekintse meg a futtatási előzmények és elemzések lekérése. A következő képernyő-rögzítési folyamat kezeléséhez rendelkezésre álló lehetőségeket mutatja. 

 ![Folyamatok kezelése](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

A folyamat továbbra is működik, amíg használatával állítsa le a **folyamat kikapcsolása** lehetőséget.

Nem érdeklődő e-mail értesítéseket kap, ha az azt jelenti, hogy az új érdeklődők még nem sikerült hozzáadni az Azure-tábla. Ha a folyamat hibákat, a példához hasonló e-mailt kap a következő képernyőfelvételen.

 ![A folyamat sikertelen e-mail-értesítés](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>További lépések

[Ügyfélérdeklődések konfigurálása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)