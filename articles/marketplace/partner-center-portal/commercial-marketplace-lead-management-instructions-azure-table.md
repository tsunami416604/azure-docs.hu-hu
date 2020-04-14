---
title: Azure-táblázat | Azure Piactér
description: Konfigurálja az Azure Table érdeklődőkezelés.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a36c411b9ababc42adb51d82a316df4252c01e24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252016"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Érdeklődőkezelés konfigurálása Azure-táblázat használatával

Ha az ügyfélkapcsolat-kezelési (CRM) rendszer nem kifejezetten támogatja a Partner Center az Azure Marketplace és AppSource érdeklődők fogadására, használhatja az Azure Table kezelni ezeket az érdeklődőket. Ezután exportálhatja és importálhatja az adatokat a CRM rendszerbe. Ebben a cikkben található utasításokat végigvezeti az Azure Storage-fiók létrehozásának folyamatában, és egy Azure-táblázat az adott fiók alatt. Emellett a Microsoft Flow segítségével új folyamatot is létrehozhat, amelyei e-mailértesítést küldhet, ha az ajánlat érdeklődőt kap.

## <a name="configure-azure-table"></a>Az Azure-tábla konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes próbafiókot.](https://azure.microsoft.com/pricing/free-trial/)
2. Miután az Azure-fiók aktív, jelentkezzen be az [Azure Portalon.](https://portal.azure.com)
3. Az Azure Portalon hozzon létre egy tárfiókot az alábbi eljárással.  
    1. Válassza **a +Erőforrás létrehozása** lehetőséget a bal oldali menüsorban.  Az **Új** ablaktábla (panel) jobbra jelenik meg.
    2. Válassza a **Tárolás** lehetőséget az **Új** ablaktáblában.  A **kiemelt** lista jobbra jelenik meg.
    3. Válassza **a Tárfiók** lehetőséget a fiók létrehozásának megkezdéséhez.  Kövesse a tárfiók létrehozása című [cikkutasításait.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

        ![Az Azure-tárfiók létrehozásának lépései](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        A tárfiókokról további információt a [Rövid útmutató lehetőséget](https://docs.microsoft.com/azure/storage/)válassza.  A tárolási díjszabásról a [tárolási díjszabás című](https://azure.microsoft.com/pricing/details/storage/)témakörben talál további információt.

4. Várjon, amíg a tárfiók ki van építve, egy folyamat, amely általában néhány percet vesz igénybe.  Ezután az Azure Portal **kezdőlapjáról** érheti el a tárfiókot az **Összes erőforrás megtekintése** vagy az Azure Portal bal oldali navigációs menüsorában az Összes **erőforrás** kiválasztásával.

    ![Az Azure storage-fiók elérése](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. A tárfiók ablaktábláján válassza az **Access-kulcsok** lehetőséget, és másolja a *kulcs kapcsolati* karakterlánc-értékét. Mentse ezt az értéket, mivel ez a *storage-fiók kapcsolati karakterlánc* értéke, amelyet meg kell adnia a közzétételi portálon, hogy érdeklődőket kapjon a piactéri ajánlathoz. 

    Egy példa a kapcsolat csípése a következő:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure tárolási kulcs](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. A tárfiók ablaktábláján válassza a **Táblák** lehetőséget, és a **+Tábla** lehetőséget a tábla létrehozásához. Adja meg a tábla nevét, és válassza az **OK gombot.** Mentse ezt az értéket, ahogy szüksége lesz rá, ha be szeretné állítani az MS Flow-t, hogy e-mailértesítést kapjon az érdeklődők fogadásáról.

    ![Azure-táblázatok](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Használhatja [az Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) vagy bármely más eszköz a tárolótáblában lévő adatok megtekintéséhez. Az Azure-táblázatban is exportálhatja az adatokat. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Nem kötelező) A Microsoft Flow használata Azure-táblával  

A [Microsoft Flow](https://docs.microsoft.com/flow/) segítségével automatizálhatja az értesítéseket minden alkalommal, amikor egy érdeklődőt hozzáad az Azure-táblához. Ha nem rendelkezik fiókkal, [regisztrálhat egy ingyenes fiókot.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Példa érdeklődőkeresési értesítésre

Ebben a példában útmutatóként hozzon létre egy egyszerű folyamatot, amely automatikusan küld egy e-mail értesítést, ha egy új érdeklődőt adnak hozzá egy Azure-táblához. Ez a példa beállít egy ismétlődést, hogy óránként küldjön érdeklődőadatokat, ha a táblatároló frissül.

1. Jelentkezzen be A Microsoft Flow-fiókba.
2. A bal oldali navigációs sávon válassza a **Saját folyamatok lehetőséget.**
3. A felső navigációs sávon válassza a **+ Új**lehetőséget.  
4. A legördülő listában válassza a **+ Ütemezett - lehetőséget az üres**

   ![Saját folyamatok **+ ütemezett - üres **](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.    A *Build a scheduled flow* ablak alatt *Ismételje meg minden* válassza ki az "1" intervallum és az "óra" a gyakoriság. Is, hogy az áramlás egy nevet, ha úgy tetszik. Kattintson a **Létrehozás** gombra.

>[!Note]
>Bár ez a példa egy 1 órás intervallumot használ, kiválaszthatja az üzleti igényeinek leginkább megfelelő intervallumot és gyakoriságot.

![Ütemezett folyamat létrehozása.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Válassza az **+ Új lépés** gombot.
7. A *Műveletablak kiválasztása mezőben* keressen rá a "Túllépések ideje" kifejezésre, és válassza a **Műveletek korábbi idő bekerülése** lehetőséget.

   ![Válasszon egy műveletet.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. A **Get past time** ablakban állítsa az **Intervallum ot 1-re.** Az **Időegység** legördülő listában válassza az **Óra**lehetőséget.

    >[!Important]
    >Győződjön meg arról, hogy ez az időköz és időegység megegyezik az 5.

    ![A bekerülési időintervallum beállítása](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>A folyamatot bármikor ellenőrizheti, hogy az egyes lépéseket megfelelően konfigurálhatja-e. A folyamat ellenőrzéséhez válassza a **Folyamat menüsor Folyamat-ellenőrző** elemét.

A következő lépésekben csatlakozik az Azure-táblához, és beállítja a feldolgozási logikát az új érdeklődők kezeléséhez.

9. Az Elmúlt idő bekerülése lépés után válassza a **+ Új lépést**, majd a *Művelet* kiválasztása ablakban keressen rá az "Entitások betöltése" kifejezésre.
10. A **Műveletek csoportban**válassza **az Entitások begete (Azure Table Storage)** lehetőséget.
11.    Az **Azure Table Storage** ablakban adja meg a következő mezők adatait, és válassza a **Létrehozás lehetőséget:**
* *Kapcsolat neve* – adjon meg egy értelmes nevet a folyamat és az Azure-tábla között létrehozott kapcsolathoz.
* *Tárfiók neve* – adja meg az Azure-tábla tárfiókjának nevét. Ezt a tárfiók **hozzáférési kulcsok** lapján találja.
* *Megosztott tárolási kulcs* – adja meg az Azure-tábla áruházi fiókjának kulcsértékét. Ezt a tárfiók **hozzáférési kulcsok** lapján találja.
    ![Azure Table-tároló.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

A Létrehozás gombra kattintás után megjelenik egy *Entitások bekeresése* ablaka. Itt válassza **a Speciális beállítások megjelenítése** lehetőséget, és adjon meg információkat a következő mezőkhöz:
* *Táblázat* – Válassza ki az Azure Table Storage nevét (az Azure-tábla konfigurálására vonatkozó utasítások 6. lépéséből). A következő képernyőfelvételen megjelenik a kérdés, ha a "marketplaceleads" tábla van kiválasztva ehhez a példához.
    ![Az Azure Table entitásokat kap.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

* *Lekérdezés szűrése* – Jelölje ki ezt a `Timestamp gt datetime'@{body('Get_past_time')}'` ![mezőt, és illessze be ezt a függvényt a mezőbe: Az Azure Table entitásokat kap – Lekérdezés szűrése.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Most, hogy befejezte a kapcsolatot az Azure-táblával, válassza az **Új lépés** lehetőséget az Azure-tábla új érdeklődők iránti vizsgálatához. 

13. A **Művelet kiválasztása** ablakban válassza a **Műveletek**lehetőséget, majd a **Feltétel vezérlőt**.

    ![Azure Table – Válasszon egy műveletet.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. A **Feltétel** ablakban jelölje ki az **Érték kiválasztása** mezőt, majd az előugró ablakban válassza a **Kifejezés** lehetőséget.

15. Illessze `length(body('Get_entities')?['value'])` be az ***fx*** mezőbe. A függvény hozzáadásához válassza az **OK gombot.** 

16. A feltétel beállításának befejezése:
    1. Válassza a "nagyobb, mint" lehetőséget a legördülő listából.
    2. A 0 érték megadása

        ![Azure Table – feltétel.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

A következő néhány lépésben a feltétel eredménye alapján beállítja a végrehajtandó műveletet.

* Ha a feltétel megszűnik, ha **nem,** ne tegyen semmit.
* Ha a feltétel az **If yes (Igen)** állapotra vall, indítson el egy műveletet, amely összeköti az Office 365-fiókját e-mail küldésével. 

17. Válassza **a Művelet hozzáadása** csoportBan válassza a Művelet hozzáadása **lehetőséget.**

    ![Azure Table - Feltétel, **Ha igen**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Válassza **az E-mail küldése (Office 365 Outlook)** lehetőséget.

    ![Azure Table - Feltétel, **Ha igen**, küldjön e-mailt.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Ha egy másik e-mail szolgáltatót szeretne használni, keressen rá, és helyette az E-mail értesítés küldése (E-mail) lehetőséget válassza. Az utasítások megmutatják, hogyan konfigurálható az Office 365 Outlook használatával, de az utasítások hasonlóak egy másik e-mail szolgáltatóhoz.

19. Az **Office 365 Outlook** ablakban adja meg a következő mezők adatait:

    1. **To** - Adjon meg egy e-mail címet mindenkinek, aki megkapja ezt az értesítést.
    1. **Tárgy** - Adja meg az e-mail tárgyát. Például: Új érdeklődők!
    1. **Törzs** – Adja hozzá az egyes e-mailekben szerepelni kívánt `body('Get_entities')?['value']`szöveget (nem kötelező), majd illessze be a törzsbe .

    >[!Note]
    >További statikus vagy dinamikus adatpontokat szúrhat be az e-mail törzséhez.

    ![Azure Table – Feltétel, **Ha igen**, Az Office 365 Outlook ablak.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. A folyamat mentéséhez válassza a **Mentés** lehetőséget. A Microsoft Flow automatikusan teszteli a folyamat hibákat. Ha nincsenek hibák, a folyamat a mentés után elindul.

A következő képernyőfelvétel egy példát mutat be arra, hogyan kell kinéznie a végső folyamatnak.

![Egy példa a végső folyamatra.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>A folyamat kezelése

A folyamat kezelése futás után egyszerű. Ön teljes mértékben ellenőrzése alatt a flow. Például leállíthatja, szerkesztheti, megtekintheti a futtatási előzményeket, és elemzéseket kaphat. A következő képernyőfelvétel en láthatók a folyamat kezeléséhez rendelkezésre álló lehetőségek. 

 ![Folyamat kezelése](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

A folyamat addig fut, amíg le nem állítja a **Folyamat kikapcsolása** beállítással.

Ha nem kap érdeklődői e-mail értesítéseket, az azt jelenti, hogy az új érdeklődők nem lettek hozzáadva az Azure-táblához. Ha vannak folyamathibák, a következő képernyőfelvételen egy olyan e-mailt kap, mint a példa.

 ![Folyamatsikertelen e-mail értesítés](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Az ajánlat konfigurálása érdeklődők küldésére az Azure-táblázatba

Ha készen áll az ajánlat érdeklődőkezelési adatainak konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Nyissa meg az **ajánlat beállítási** oldalát.
2. Válassza a **Csatlakozás** lehetőséget az Érdeklődőkezelés szakaszban.
3. A Kapcsolat részletei előugró ablakban válassza az **Azure Table** az **érdeklődő cél,** és illessze be a kapcsolati karakterlánc az Azure storage-fiók létrehozott a következő korábbi lépéseket a **Storage-fiók kapcsolati karakterlánc** a mezőbe.
4. **Kapcsolatfelvételi e-mail** – E-maileket adhat meg a vállalatnál lévő személyeknek, akiknek e-mailes értesítéseket kell kapniuk, ha új érdeklődő érkezik. Több e-mailt is megadhat, ha pontosvesszővel választja el őket.
5. Válassza az **Ok gombot.**

Ha meg szeretne győződni arról, hogy sikeresen csatlakozott egy érdeklődő célállomásához, kattintson az érvényesítés gombra. Ha sikeres, akkor lesz egy teszt vezető az érdeklődő cél.

>[!Note]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie, mielőtt érdeklődőket kapna az ajánlathoz.

Érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket az Azure-táblába. Ha beállított egy folyamatot, a rendszer e-mailt is küld a megadott e-mail címre.

![Érdeklődők kezelése](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Érdeklődőkezelés - kapcsolat részletei](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Érdeklődőkezelés - kapcsolat részletei tárfiók](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

