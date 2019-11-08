---
title: Azure-tábla | Azure piactér
description: Az Azure Table vezető felügyeletének konfigurálása.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 9b24e6eb714c531b49ba08591bf4ed33d0f10101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812339"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Az érdeklődők felügyeletének konfigurálása Azure-táblázat használatával

Ha az Ügyfélkapcsolat-kezelési (CRM) rendszer nincs kifejezetten támogatva a partner Centerben az Azure Marketplace és a AppSource-érdeklődők fogadásához, akkor az Azure-táblázat segítségével kezelheti ezeket az érdeklődőket. Ezután exportálhatja az adatait, és importálhatja azt a CRM-rendszerbe. A cikkben szereplő utasítások végigvezetik az Azure Storage-fiók létrehozásának folyamatán, valamint az adott fiók alatt található Azure-táblázaton. Emellett létrehozhat egy új folyamatot Microsoft Flow használatával, amely e-mailben értesítést küld, ha az ajánlat érdeklődőt kap.

## <a name="configure-azure-table"></a>Az Azure Table konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/pricing/free-trial/).
2. Ha az Azure-fiókja aktív, jelentkezzen be a [Azure Portalba](https://portal.azure.com).
3. A Azure Portal a következő eljárással hozzon létre egy Storage-fiókot.  
    1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali menüsorban.  Ekkor megjelenik az **új** ablaktábla (panel) a jobb oldalon.
    2. Válassza a **tároló** lehetőséget az **új** ablaktáblán.  Egy **Kiemelt** lista jelenik meg a jobb oldalon.
    3. A fiók létrehozásának megkezdéséhez válassza a **Storage-fiók** lehetőséget.  Kövesse a [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)című cikk utasításait.

        ![Azure Storage-fiók létrehozásának lépései](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        A Storage-fiókokkal kapcsolatos további információkért válassza a gyors üzembe helyezési [oktatóanyag](https://docs.microsoft.com/azure/storage/)elemet.  További információ a Storage díjszabásáról: [Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

4. Várjon, amíg megtörténik a Storage-fiók üzembe helyezése, egy folyamat, amely általában néhány percet vesz igénybe.  Ezután nyissa meg a Storage-fiókját a Azure Portal **kezdőlapján** az **összes erőforrás** megjelenítése lehetőség kiválasztásával, vagy válassza ki az **összes erőforrás** elemet a Azure Portal bal oldali navigációs menüjében.

    ![Hozzáférés az Azure Storage-fiókhoz](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. A Storage-fiók ablaktáblán válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kulcshoz tartozó *kapcsolati sztring* értéket. Mentse ezt az értéket, mivel ez a *Storage-fiók kapcsolódási karakterláncának* értéke, amelyet a közzétételi portálon kell megadnia a Piactéri ajánlathoz tartozó érdeklődők fogadásához. 

    A kapcsolatok csípése például a következőket szemlélteti:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure Storage-kulcs](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. A Storage-fiók ablaktáblán válassza a **táblák** lehetőséget, és válassza a **+ táblázat** lehetőséget egy tábla létrehozásához. Adja meg a tábla nevét, majd kattintson **az OK gombra**. Mentse ezt az értéket, mert szüksége lesz rá, ha egy MS flow-t szeretne beállítani az e-mail-értesítések fogadásához, amikor az érdeklődők érkeznek.

    ![Azure-táblák](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Az [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) vagy bármely más eszköz használatával megtekintheti a Storage-tábla adatait. Az Azure-táblában lévő adatexportálást is elvégezheti. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Választható Microsoft Flow használata Azure-táblázattal  

A [Microsoft flow](https://docs.microsoft.com/flow/) segítségével automatizálhatja az értesítéseket minden alkalommal, amikor érdeklődőt adnak hozzá az Azure Table szolgáltatáshoz. Ha nincs fiókja, regisztrálhat [egy ingyenes fiókot](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Példa vezető értesítésre

Ez a példa útmutatóként szolgál egy olyan egyszerű folyamat létrehozásához, amely automatikusan értesítő e-mailt küld, ha új érdeklődőt adnak hozzá egy Azure-táblához. Ez a példa egy ismétlődést állít be az érdeklődői adatok óránkénti elküldéséhez, ha a tábla tárterülete frissül.

1. Jelentkezzen be Microsoft Flow-fiókjába.
2. A bal oldali navigációs sávon válassza a **saját folyamatok**lehetőséget.
3. A felső navigációs sávon válassza az **+ új**lehetőséget.  
4. A legördülő listában válassza a **+ ütemezett – üres** lehetőséget.

   ![Saját folyamatok * * + ütemezett – üres * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  Az *ütemezett folyamat létrehozása* ablak *ismétlése* alatt az összes kijelölése "1" értéket az intervallum és a "Hour" értéknél. Adja meg a folyamat nevét is, ha szeretné. Kattintson a **Létrehozás** gombra.

    >[!Note]
    >Bár ez a példa 1 órás időközt használ, kiválaszthatja az üzleti igényeinek leginkább megfelelő intervallumot és gyakoriságot.

    ![Hozzon létre egy ütemezett folyamatot.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Válassza az **+ új lépés**lehetőséget.
7. A *művelet kiválasztása* ablakban keressen rá a "beolvasás ideje" kifejezésre, majd válassza a műveletek területen a **múltbeli idő beolvasása** lehetőséget.

   ![Válasszon műveletet.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. A **beolvasás ideje** ablakban állítsa az **intervallumot** 1-re. Az **időegység** legördülő listából válassza az **óra**lehetőséget.

    >[!Important]
    >Győződjön meg arról, hogy ez az intervallum és időegység megfelel az 5. lépésben az ismétlődéshez konfigurált intervallumnak és gyakoriságnak.

    ![Az elmúlt időintervallum beállítása](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Bármikor ellenőrizheti, hogy az egyes lépések megfelelően vannak-e konfigurálva. A folyamat ellenőrzését a folyamat menüsorában válassza ki a flow- **ellenőr** lehetőséget.

A következő lépések során csatlakozni fog az Azure-táblához, és beállíthatja a feldolgozási logikát az új érdeklődők kezeléséhez.

9. A korábbi időpont beolvasása lépés után válassza az **+ új lépés**lehetőséget, majd keressen rá az "entitások beolvasása" elemre a *művelet választása* ablakban.
10. A **műveletek**területen válassza az **entitások beolvasása (Azure Table Storage)** lehetőséget.
11. Az **Azure Table Storage** ablakban adja meg a következő mezők adatait, majd válassza a **Létrehozás**lehetőséget:

    * *Kapcsolat neve* – adjon meg egy értelmes nevet a folyamat és az Azure-tábla közötti kapcsolathoz.
    * *Storage-fiók neve* – adja meg az Azure-tábla Storage-fiókjának nevét. Ezt a Storage-fiók **hozzáférési kulcsainak** oldalán találja.
    * *Megosztott tároló kulcsa* – adja meg az Azure-tábla áruházbeli fiókjához tartozó kulcs értékét. Ezt a Storage-fiók **hozzáférési kulcsainak** oldalán találja.

        ![Azure Table Storage.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    A Létrehozás gombra kattintva megjelenik az *entitások beolvasása* ablak. Itt válassza a **Speciális beállítások megjelenítése** lehetőséget, és adja meg a következő mezők adatait:

       * *Tábla* – válassza ki az azure-Table Storage nevét (az Azure-táblák konfigurálására vonatkozó útmutatás 6. lépésében). A következő képernyőfelvételen látható, hogy mikor van kiválasztva a "marketplaceleads" tábla erre a példára.

            ![Azure-tábla – entitások beolvasása.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Lekérdezés szűrése* – jelölje be ezt a mezőt, és illessze be a függvényt a következő mezőbe: `Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Table – entitások beolvasása – querry szűrése.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Most, hogy befejezte a kapcsolódást az Azure-táblához, válassza az **új lépés** lehetőséget, hogy felvegyen egy feltételt az új érdeklődők Azure-táblázatának vizsgálatához. 

13. A **művelet kiválasztása** ablakban válassza a **műveletek**lehetőséget, majd válassza ki a **feltétel vezérlőelemet**.

    ![Azure-tábla – válasszon egy műveletet.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. A **feltétel** ablakban válassza ki a **válasszon egy értéket** mezőt, majd válassza a **kifejezés** lehetőséget az előugró ablakban.

15. Illessze be `length(body('Get_entities')?['value'])` az ***FX*** mezőbe. A függvény hozzáadásához kattintson **az OK gombra** . 

16. A feltétel beállításának befejezéséhez:
    1. Válassza a "nagyobb, mint" lehetőséget a legördülő listából.
    2. Adja meg a 0 értéket

        ![Azure Table-Condition.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

A következő néhány lépésben be kell állítania a feltétel eredményén alapuló műveletet.

* Ha a feltétel a **nem**értékre van feloldva, semmit sem kell tennie.
* Ha a feltétel az **Igen**értékre van feloldva, aktiváljon egy műveletet, amely összekapcsolja az Office 365-fiókját e-mailek küldéséhez. 

17. Válassza a **művelet hozzáadása** lehetőséget, **Ha igen**.

    ![Azure Table-Condition, * * ha igen * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Válassza **az E-mail küldése (Office 365 Outlook)** lehetőséget.

    ![Azure Table-Condition, * * ha igen * *, küldjön e-mailt.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Ha más e-mail-szolgáltatót szeretne használni, válassza az e-mail értesítés küldése (e-mail) lehetőséget a művelet helyett. Az utasítások azt mutatják be, hogyan konfigurálhatja az Office 365 Outlook használatát, de az utasítások hasonlóak egy másik e-mail szolgáltatóhoz.

19. Az **Office 365 Outlook** ablakban adja meg a következő mezők adatait:

    1. Ide: adjon meg egy **e-mail-címet** mindenki számára, aki megkapja ezt az értesítést.
    1. **Tárgy** – adjon meg egy tárgyat az e-mailhez. Például: új érdeklődők!
    1. **Törzs** – adja hozzá az egyes e-mailekben szerepeltetni kívánt szöveget (opcionális), majd illessze be a szövegtörzsbe `body('Get_entities')?['value']`.

    >[!Note]
    >További statikus vagy dinamikus adatpontokat is beszúrhat az e-mailek törzséhez.

    ![Azure Table-Condition, * * ha igen * *, az Office 365 Outlook ablaka.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Válassza a **Mentés** lehetőséget a folyamat mentéséhez. Microsoft Flow automatikusan teszteli a folyamatot a hibákhoz. Ha nincsenek hibák, a folyamat a mentés után elindul.

A következő képernyőfelvételen látható egy példa arra, hogy a végső folyamatnak hogyan kell kinéznie.

![Példa a végső folyamatra.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>A folyamat kezelése

A folyamat a futtatása után egyszerűen kezelhető. A folyamat teljes mértékben szabályozható. Leállíthatja például, szerkesztheti, megtekintheti a futtatási előzményeket, és lekérheti az elemzést. A következő képernyőfelvételen a folyamatok kezeléséhez rendelkezésre álló beállítások láthatók. 

 ![Folyamat kezelése](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

A folyamat addig fut, amíg le nem állítja a **folyamatot a kikapcsolás** kikapcsolása lehetőség használatával.

Ha nem kap vezető e-mailes értesítéseket, az azt jelenti, hogy az új érdeklődők nem lettek hozzáadva az Azure-táblához. Ha bármilyen folyamat meghiúsul, egy e-mailt fog kapni a következő képernyőfelvételen látható példához hasonlóan.

 ![Sikertelen folyamat – e-mail-értesítés](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére

Ha készen áll arra, hogy az ajánlathoz tartozó érdeklődő-felügyeleti információkat a közzétételi portálon konfigurálja, kövesse az alábbi lépéseket:

1. Navigáljon az ajánlat **telepítési** lapjára.
2. Válassza a **kapcsolat** lehetőséget az érdeklődő felügyelete szakaszban.
3. A kapcsolat részletei előugró ablakban válassza ki az **Azure-tábla** lehetőséget az **érdeklődő céljához**, és illessze be a kapcsolati karakterláncot a létrehozott Azure Storage-fiókból, amelyet a korábbi lépések a **Storage-fiók kapcsolati karakterlánca** alapján hozott létre. mező.
4. Kattintson a **Mentés** gombra. 

>[!Note]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.

Az érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket az Azure-táblába. Ha konfigurált egy folyamatot, a rendszer e-mailt küld a konfigurált e-mail-címre is.

![Érdeklődők kezelése](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Érdeklődői felügyelet – kapcsolat részletei](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Vezető felügyelet – kapcsolat részletei Storage-fiók](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

