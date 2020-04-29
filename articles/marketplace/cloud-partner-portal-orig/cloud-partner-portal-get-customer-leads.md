---
title: Ügyfél-érdeklődők konfigurálása | Azure piactér
description: Az ügyfél-érdeklődők konfigurálása a Cloud Partner Portalban.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280320"
---
<a name="get-customer-leads"></a>Ügyfélérdeklődések lekérése
==================

Ez a cikk azt ismerteti, hogyan hozhatók létre az ügyfelek a Cloud Partner Portal használatával. Ezeket az érdeklődőket összekapcsolhatja a CRM-rendszerrel, és integrálhatja őket az értékesítési folyamatba.

## <a name="leads"></a>Érdeklődők

Az érdeklődők olyan ügyfelek, akik érdeklik, vagy üzembe helyezik termékeiket az [Azure piactéren](https://azuremarketplace.microsoft.com/) vagy a [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Piactér

1.  Az ügyfél a saját ajánlatának "tesztelési meghajtóját" veszi igénybe. A test Drives egy gyorsított lehetőség, amellyel azonnal megoszthatja vállalatát a potenciális ügyfelekkel anélkül, hogy a belépést akadályozni kellene. Az összes tesztvezetés egy olyan ügyfelet hoz, aki szeretne többet megtudni a termék kipróbálásáról. További információ a tesztelési meghajtókról az [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)-on.

    ![Példák a Marketplace test drivere](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Az ügyfél beleegyezik, hogy megosztják az adataikat a "Letöltés most" lehetőség kiválasztása után. Ez az érdeklődő egy **kezdeti érdeklődési** vezető, ahol megosztjuk a termék beszerzésével kapcsolatos érdeklődéssel bíró ügyfelet. Az érdeklődő a beszerzési tölcsér felső része.

   ![Letöltés lehetőség](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Az ügyfél a "vásárlás" lehetőséget választja az [Azure Portalon](https://portal.azure.com/) a termék beszerzéséhez. Ez az érdeklődő egy **aktív** érdeklődő, ahol megosztjuk a termék üzembe helyezését megkezdő ügyféllel kapcsolatos információkat.

   ![Vásárlási lehetőség](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Az ügyfél az ajánlathoz tartozó "tesztelési meghajtót" vett igénybe. A test Drives egy gyorsított lehetőség, amellyel azonnal megoszthatja vállalatát a potenciális ügyfelekkel anélkül, hogy a belépést akadályozni kellene. Az összes tesztvezetés olyan ügyfelet fog eredményezni, aki szeretne többet megtudni a termék kipróbálásáról. További információ a [AppSource-tesztelési meghajtón](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)található tesztelési meghajtókról.

    ![Példa tesztelési meghajtóra](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Az ügyfél beleegyezik, hogy megosztják az adataikat a "Letöltés most" lehetőség kiválasztása után. Ez az érdeklődő egy **kezdeti érdeklődést** eredményez, ahol megosztjuk az ügyfélre vonatkozó információkat, akik érdeklődést mutatnak a termék beszerzéséhez. Az érdeklődő a beszerzési tölcsér felső része.

      ![Letöltés lehetőség](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Az ügyfél a "Kapcsolatfelvétel" lehetőséget választja az ajánlaton. Ez az érdeklődő egy **aktív** érdeklődő, ahol megosztjuk az ügyféllel kapcsolatos információkat, akik azt kérik, hogy kövessék a terméket.

    ![Kapcsolatfelvételi lehetőség](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Érdeklődői adatkészletek
---------

Az ügyfél beszerzési folyamata során kapott összes érdeklődő meghatározott mezőkben lévő adattal rendelkezik. Mivel több lépésből is nyer, az érdeklődők kezelésének legjobb módja az, hogy a rendszer duplikálja és testreszabja a követést. Így minden ügyfél megfelelő üzenetet kap, és egyedi kapcsolatot hoz létre.

### <a name="lead-source"></a>Ólom forrása

A vezető forrás formátuma a **forrás**-**műveleti** |  **ajánlat**

**Források**: "AzureMarketplace", "AzurePortal", "testdrive" és "APPSOURCE (SPZA)"

**Műveletek**:
- "INS" – telepítés. Ez a művelet az Azure Marketplace-en vagy a AppSource, amikor az ügyfél megvásárolja a terméket.
- "PLT" – a partner által vezetett próbaverziót jelenti. Ez a művelet a AppSource, ha az ügyfél a kapcsolatfelvételi lehetőséget használja.
- "DNC –" – ne lépjen kapcsolatba. Ez a művelet a AppSource, ha az alkalmazás oldalán megjelenő partnert kérik a kapcsolatfelvételre. Megosztjuk a vezetőket, hogy az ügyfél az alkalmazáson keresztül szerepel, de nem kell kapcsolatba lépnie.
- "Létrehozás" – Ez a művelet csak az Azure Portalon belül történik, és akkor jön létre, amikor az ügyfél megvásárolja az ajánlatát a fiókjához.
- "StartTestDrive" – Ez a művelet csak tesztelési meghajtók esetében használható, és akkor jön létre, amikor egy ügyfél elindítja a tesztelési meghajtót.

**Ajánlatok**

Az alábbi példák a közzétevőhöz és egy adott ajánlathoz rendelt egyedi azonosítókat mutatják be: ellenőrzőpont. ellenőrzés-pont-r77-10sg-byol, bitnami. openedxcypress és docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Vásárlói adatok

A következő példában szereplő mezőkben láthatók a érdeklődők által megadott ügyfél-információk.
- FirstName: John
- LastName: Smith
- E-mail: jsmith\@Microsoft.com
- Telefon: 1234567890
- Ország: Egyesült Államok
- Vállalat: Microsoft
- Cím: műszaki igazgató

>[!Note]
>Az előző példában szereplő összes érték mindig elérhető az egyes érdeklődők számára.

Aktívan dolgozunk az érdeklődők fejlesztésén, tehát ha van olyan adatmező, amelyet itt nem lát, de szeretné, [küldje el nekünk visszajelzését](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>A CRM rendszer összekötése a Cloud Partner Portal
------------------------------------------------------------

Az érdeklődők beszerzésének megkezdéséhez építettük a Cloud Partner Portal vezető felügyeleti összekötőjét, így könnyedén csatlakoztathatja a CRM-adatokat, és elvégezheti a kapcsolódást. Mostantól könnyedén kihasználhatja a piactér által generált érdeklődőket, és nem kell egy külső rendszerrel integrálni a jelentős mérnöki erőfeszítéseket.

![Vezető felügyeleti összekötő](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Számos CRM-rendszerbe, vagy közvetlenül egy Azure Storage-táblába is írhatunk, ahol Ön is kezelheti az érdeklődőket. A következő hivatkozások mindegyike útmutatást nyújt a lehetséges érdeklődői célokhoz való csatlakozáshoz:

-   A [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) segítségével megtudhatja, hogyan konfigurálhatja a Dynamics CRM Online-t az érdeklődők beszerzéséhez.
-   A Marketo- [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) beállításához szükséges utasítások beszerzése az érdeklődők beolvasásához.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) a Salesforce-példány beállításához az érdeklődők beszerzéséhez.
-    Az Azure- [táblázat](./cloud-partner-portal-lead-management-instructions-azure-table.md) segítségével megtudhatja, hogyan állíthatja be Azure Storage-fiókját az érdeklődők Azure-táblázatba való beszerzéséhez.
-   [Https-végpont](./cloud-partner-portal-lead-management-instructions-https.md) , amely útmutatást nyújt a https-végpont beállításához az érdeklődők beszerzéséhez.

Miután konfigurálta az érdeklődő célját, és közzéteszi az ajánlatát, érvényesítjük a kapcsolatokat, és elküldjük Önnek a tesztelési érdeklődőt. Ha az ajánlat megtekintése előtt megtekinti az ajánlatot, akkor tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja meg saját maga is beszerezni az ajánlatot. Fontos, hogy az érdeklődői beállítások naprakészek maradjanak, hogy ne veszítse el az érdeklődőket, ezért ügyeljen arra, hogy frissítse ezeket a kapcsolatokat, ha valami módosult a végén.

<a name="what-next"></a>Mi a következő lépés?
----------

Ha a technikai beállítás be van állítva, ezeket az érdeklődőket be kell építenie a jelenlegi értékesítési & marketing stratégiába és működési folyamataiba. Nagyon fontos, hogy jobban megértsük a teljes értékesítési folyamatokat, és szeretne szorosan együttműködni Önnel, hogy kiváló minőségű érdeklődőket és elegendő adatmennyiséget biztosítson a siker érdekében. Örömmel vesszük visszajelzését arról, hogy miként optimalizálható és növelhető az érdeklődők számára elérhető további információk, amelyek segítenek az ügyfelek sikerességében. Kérjük, tudassa velünk, ha szeretne [visszajelzést](mailto:AzureMarketOnboard@microsoft.com) küldeni, és javaslatokat tesz arra, hogy az értékesítési csapata még eredményesebb legyen a piactéren.
