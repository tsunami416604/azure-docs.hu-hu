---
title: Ügyfélérdeklődések konfigurálása |} A Microsoft Docs
description: Ügyfélérdeklődések adja meg a Cloud Partner portálra.
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 997727acd1bbaf17577160bb996aad7e21d49c86
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809798"
---
<a name="get-customer-leads"></a>Ügyfélérdeklődések lekérése
==================

Ez a cikk bemutatja, hogyan hozhat létre ügyfélérdeklődések használatával a Cloud Partner portálra. Érdeklődők válaszaránya csatlakozhat a saját CRM-rendszerében, és azokat az értékesítési folyamatba való integrálásához.

## <a name="leads"></a>Érdeklődők

Érdeklődők ügyfele iránt, vagy üzembe a termékeket a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) vagy [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Piactér

1.  Ügyfél egy "Test Drive" ajánlat vesz igénybe. Teszt meghajtók gyorsított lehetőséget nyújt az üzleti azonnal megoszthatja a potenciális ügyfelekkel minden belépési korlátok nélkül használhatók. Összes Tesztverzió hozzon létre egy érdeklődő, amelyet a termék további próbált érdekli. További információ a Tesztverzió [Azure Marketplace-en Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Marketplace-en test drive példák](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

2.  Ügyfél járul hozzá a "Letöltés most" kiválasztása után az adatok megosztása. Ez az érdeklődő egy **érdeklődés kezdeti** vezető, ahol osztjuk meg az első a termék rendelkezik érdeklődést vásárló kapcsolatos információkat. Az érdeklődő felső részén a beszerzési tölcsér.

    ![Beszerzés most lehetőség](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

3.  Ügyfél kijelölése "Vásárlás" a [az Azure Portal](https://portal.azure.com/) beolvasni a termék. Ez az érdeklődő egy **aktív** vezető, ahol osztjuk meg olyan ügyfelünk, akit a termék telepítése megkezdődött kapcsolatos információkat.

    ![Vásárlási lehetőség](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Ügyfél tartott egy "Test Drive" az ajánlatban. Teszt meghajtók gyorsított lehetőséget nyújt az üzleti azonnal megoszthatja a potenciális ügyfelekkel minden belépési korlátok nélkül használhatók. Összes Tesztverzió generál, amelyet iránt érdeklődik, érdeklődő a termék további során. További információ a Tesztverzió [appsource-on Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Test drive példa](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Ügyfél járul hozzá a "Letöltés most" kiválasztása után az adatok megosztása. Ez az érdeklődő egy **érdeklődés kezdeti** vezető, ahol osztjuk meg fejezi ki a termék első iránt vásárló kapcsolatos információkat. Az érdeklődő felső részén a beszerzési tölcsér.

      ![Beszerzés most lehetőség](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Ügyfél választja ki a "Velem a kapcsolatot" ajánlat a. Ez az érdeklődő egy **aktív** vezető, ahol osztjuk meg olyan ügyfelünk, akit kéri az nyomon kell követni a termékkel kapcsolatos információkat.

    ![Megkeresést kérek lehetőség](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Elégtelen adat
---------

Egyes potenciális ügyfelek az ügyfél megszerzése során kap az adott mezők adatokat tartalmaz. Több lépésből származó érdeklődők fog kapni, mert az a legjobb módszer az érdeklődők kezeléséhez megszüntetéséhez ismétlődő, és a követések személyre. Ezzel a módszerrel minden egyes ügyfélnek egy megfelelő üzenet, és a egy egyedi kapcsolatot hoz létre.

### <a name="lead-source"></a>Referencia

Egy érdeklődő forrás formátuma **forrás**-**művelet** |  **ajánlat**

**Források**: "AzureMarketplace", "Tevékenységjelentési", "TestDrive" és "Appsource-ban (SPZA)"

**Műveletek**:
- "Modulok"--telepítését. Az Azure piactéren vagy az appsource-ban Ez a művelet akkor, amikor egy ügyfél vásárol a termék.
- ".-LBI"--partner összhangban próbaverzió vezetett. Ez a művelet esetén az appsource-on ügyfél használ az ügyfél me lehetőséget.
- "DNC"--ne forduljon. Ez a művelet akkor az appsource-on, ha értesítenének igénylésekor a lekérdezi egy partnerrel, aki közötti az alkalmazás a lapon látható volt. Hogy megosztásakor az, hogy az ügyfél közötti volt-e fel az alkalmazás megjelenik a fejek, de nem szükséges elvégezni.
- "Create" – Ez a művelet csak az Azure Portalon található, és létrejön, amikor a vásárló megvásárolhatja az ajánlat-fiókjához.
- "StartTestDrive" – Ez a művelet csak a meghajtók tesztelése, és létrejön, amikor egy ügyfél elindítja a tesztverziós.

**Ajánlatok**

Az alábbi példák bemutatják a közzétevő és a egy adott ajánlat rendelt egyedi azonosítónak: checkpoint.check – pont-r77-10sg-byol bitnami.openedxcypress és 56-91e6-3ed0b622145a docusign.3701c77e-1cfa - 4c.


### <a name="customer-info"></a>Customer Info

A mezőket az alábbi példában a felhasználó adatait, amely tartalmazza az érdeklődő megjelenítése.
- Vezetéknév: János
- Vezetéknév: Smith
- E-mail cím: jsmith\@microsoft.com
- Telefonszám: 1234567890
- Ország: USA
- Vállalati: Microsoft
- Title: műszaki Igazgatója

>[!Note]
>Az előző példában nem minden adat nem mindig érhető el az egyes potenciális ügyfelek.

Aktívan dolgozunk növelése az érdeklődők, így ha egy adatmező, amely itt nem jelenik meg, de szeretné, hogy, kérjük [küldjön nekünk visszajelzést](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>A Cloud Partner portálra a CRM-rendszerbe csatlakoztatása
------------------------------------------------------------

Megkezdődik az érdeklődők, elkésztettünk az érdeklődő Management-összekötő a Cloud Partner portálra szóló, hogy egyszerűen csatlakoztathatja a CRM-adatait, és a kapcsolat használunk. Most már könnyedén kihasználhatja az érdeklődők nélkül egy jelentős mérnöki annak érdekében, hogy külső rendszerrel integrálja a piactér által generált.

![Elégtelen management-összekötő](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Írunk CRM rendszereket vagy közvetlenül az Azure Storage számos kulcstényezők táblában, ahol kezelheti az érdeklődők azonban szeretné. Az alábbi hivatkozások mindegyike adja meg a lehetséges érdeklődő célhelyre történő összekapcsolására vonatkozó utasításokat:

-   [A Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) útmutatást szeretne kapni az érdeklődők lekérhesse a Dynamics CRM Online konfigurálása.
-   [A Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) az érdeklődők lekérése a Marketo vezethet konfigurációs beállításával kapcsolatos útmutatást.
-    [A Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) beolvasni az érdeklődőket a Salesforce-példány beállításával kapcsolatos útmutatás beolvasásához.
-    [Az Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) beolvasni az érdeklődők lekérhesse az Azure-táblát az Azure storage-fiók beállításával kapcsolatos utasításokat.
-   [HTTPS-végpont](./cloud-partner-portal-lead-management-instructions-https.md) beolvasni az érdeklődők lekérése a Https-végpont beállításával kapcsolatos utasításokat.

Miután az érdeklődő cél konfigurálása és az ajánlat közzététele, azt fog a kapcsolat ellenőrzése, és küldünk egy teszt érdeklődő. Az ajánlat előtt élő tekinti meg, ha a vezető kapcsolat is letesztelheti próbál szerezni az ajánlat magát az előzetes verziójú környezet a. Fontos győződjön meg arról, hogy az érdeklődő beállítások naprakész marad, hogy ne veszítse el azok az érdeklődők, ezért ügyeljen arra, amikor valami megváltozott, a végén frissíti ezeket a kapcsolatokat.

<a name="what-next"></a>Következő lépések
----------

Után állítsa be a műszaki van beállítva, az érdeklődők válaszaránya meg kell építhet be az aktuális értékesítési és marketing stratégia és üzemeltetési folyamatokat. Nagyon érdeklik jobban megértheti a teljes értékesítési feldolgozásához, és szorosan együttműködnek Önnel, hogy kiváló minőségű érdeklődőket és elég adat, melyekkel biztosíthatja sikerét szeretné vagyunk. Szívesen fogadjuk, hogyan lehet optimalizálja és növelje az érdeklődők, további adatokkal annak érdekében, ezeket az ügyfeleket a sikeres küldhetjük a. Tudassa velünk, ha érdekelné [visszajelzés](mailto:AzureMarketOnboard@microsoft.com) és javaslatok az értékesítési csoportnak további sikeres, a piactér-beli érdeklődők engedélyezéséhez.
