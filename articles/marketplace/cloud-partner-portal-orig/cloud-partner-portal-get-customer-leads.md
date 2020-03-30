---
title: Vevőérdeklődők konfigurálása | Azure Piactér
description: Konfigurálja az ügyfélérdeklődőket a Cloud Partner Portalon.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280320"
---
<a name="get-customer-leads"></a>Ügyfélérdeklődések lekérése
==================

Ez a cikk bemutatja, hogyan hozhat létre ügyfélérdeklődőket a Cloud Partner Portal használatával. Ezeket az érdeklődőket csatlakoztathatja a CRM-rendszerhez, és integrálhatja őket az értékesítési folyamatba.

## <a name="leads"></a>Érdeklődők

Az érdeklődők olyan ügyfelek, akik érdeklődnek az [Azure Piactérről](https://azuremarketplace.microsoft.com/) vagy az [AppSource-ból,](https://appsource.microsoft.com)vagy üzembe helyezik termékeit.

### <a name="azure-marketplace"></a>Azure Piactér

1.  Az ügyfél "Test Drive"-ot hajt az ajánlatából. A tesztmeghajtók felgyorsított lehetőséget kínálnak arra, hogy vállalkozását azonnal megossza a potenciális ügyfelekkel, a belépés akadályai nélkül. Minden tesztmeghajtó olyan érdeklődőt hoz létre, amely szeretne többet megtudni a termék kipróbálásában. További információ az [Azure Marketplace-en](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)vezetett tesztmeghajtókról.

    ![Példák a Marketplace tesztmeghajtóira](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Az Ügyfél beleegyezik abba, hogy megossza az adatait, miután kiválasztotta a "Get it now" lehetőséget. Ez az érdeklődő egy **kezdeti érdeklődési** kör, ahol megosztjuk az információkat azokról az ügyfelekről, akik érdeklődést mutattak a termék megszerzése iránt. Az érdeklődő az akvizíciós tölcsér teteje.

   ![Szerezd meg most lehetőség](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Az ügyfél a "Vásárlás" lehetőséget választja az [Azure Portalon](https://portal.azure.com/) a termék beszerzéséhez. Ez az érdeklődő egy **aktív** érdeklődő, ahol információkat osztunk meg egy olyan ügyfélről, aki megkezdte a termék üzembe helyezését.

   ![Vásárlási lehetőség](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Az ügyfél "Test Drive"-ot vett fel az ajánlatához. A tesztmeghajtók felgyorsított lehetőséget kínálnak arra, hogy vállalkozását azonnal megossza a potenciális ügyfelekkel, a belépés akadályai nélkül. Minden tesztmeghajtó olyan érdeklődőt hoz létre, amely érdeklődik a termék kipróbálása iránt, hogy többet tudjon meg. További információ az [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)tesztmeghajtójának tesztmeghajtóiról.

    ![Példa a tesztvezetésre](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Az Ügyfél beleegyezik abba, hogy megossza az adatait, miután kiválasztotta a "Get it now" lehetőséget. Ez az érdeklődő egy **kezdeti érdeklődési** kör, ahol megosztjuk az ügyfelekkel kapcsolatos információkat, akik érdeklődést mutatnak a termék megszerzése iránt. Az érdeklődő az akvizíciós tölcsér teteje.

      ![Szerezd meg most lehetőség](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Az ügyfél az ajánlatában a "Kapcsolatfelvétel" lehetőséget választja. Ez az érdeklődő egy **aktív** érdeklődő, ahol információkat osztunk meg egy olyan ügyfélről, aki azt kéri, hogy kövessék nyomon a termékével kapcsolatban.

    ![Kapcsolat felvétel opció](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Érdeklődőadatai
---------

Az ügyfélszerzési folyamat során kapott minden érdeklődő adott mezőkben található adatokkal rendelkezik. Mivel több lépésből kapsz érdeklődőket, az érdeklődők kezelésének legjobb módja az, ha leiheted és személyre szabod a nyomon követéseket. Így minden ügyfél megkapja a megfelelő üzenetet, és ön egyedi kapcsolatot hoz létre.

### <a name="lead-source"></a>Érdeklődő forrása

Az érdeklődőforrás formátuma-**forrásművelet-ajánlat** |  **Offer** **Source**

**Források**: "AzureMarketplace", "AzurePortal", "TestDrive" és "AppSource (SPZA)"

**Intézkedések**:
- "INS" - telepítés. Ez a művelet az Azure Marketplace-en vagy az AppSource-on történik, amikor egy ügyfél megvásárolja a terméket.
- "PLT" - A Partner Led trial rövidítése. Ez a művelet az AppSource-on történik, ha az ügyfél a Kapcsolatfelvétel lehetőséget használja.
- "DNC" -- Ne érintkezz. Ez a művelet az AppSource-on történik, amikor egy partner, aki keresztlistára került az alkalmazás oldalán, felkérést kap a kapcsolatfelvételre. Megosztjuk a figyelmeztetést arról, hogy ez az ügyfél keresztlistára került az alkalmazásban, de nem kell velük kapcsolatba lépni.
- "Létrehozás" – Ez a művelet csak az Azure Portalon belül érhető el, és akkor jön létre, amikor egy ügyfél megvásárolja az ajánlatot a fiókjába.
- "StartTestDrive" – Ez a művelet csak tesztmeghajtókra szolgál, és akkor jön létre, amikor egy ügyfél elindítja a tesztvezetést.

**Ajánlatok**

A következő példák egy közzétevőhöz és egy adott ajánlathoz rendelt egyedi azonosítókat mutatnak be: checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress és docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Ügyféladatok

Az alábbi példában szereplő mezők az érdeklődőben található vevői adatokat mutatják.
- Keresztnév: János
- Vezetéknév: Smith
- E-mail:\@jsmith microsoft.com
- Telefon: 1234567890
- Ország: USA
- Vállalat: Microsoft
- Cím: CTO

>[!Note]
>Az előző példában nem minden adat érhető el mindig az egyes érdeklődőkhöz.

Aktívan dolgozunk az érdeklődők javításán, így ha van egy adatmező, amelyet itt nem lát, de szeretne, kérjük, [küldje el nekünk visszajelzését.](mailto:AzureMarketOnboard@microsoft.com)

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>A CRM-rendszer csatlakoztatása a Cloud Partner Portal portállal
------------------------------------------------------------

Az érdeklődők megszerzéséhez a Felhőpartner-portálon építettük fel az Érdeklődőkezelés-összekötőt, hogy könnyedén csatlakoztathassa CRM-adatait, és mi megkötjük önnek a kapcsolatot. Mostantól könnyedén kihasználhatja a piactér által generált érdeklődőket anélkül, hogy jelentős mérnöki erőfeszítéseket tene egy külső rendszerrel való integrációérdekében.

![Érdeklődőkezelési összekötő](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Az érdeklődőket különböző CRM-rendszerekbe vagy közvetlenül egy Azure Storage Table-be írhatjuk, ahol tetszés szerint kezelheti az érdeklődőket. Az alábbi hivatkozások mindegyike útmutatást nyújt a lehetséges érdeklődői helyekhez való csatlakozáshoz:

-   [A Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) az érdeklődők leéséhez való konfigurálásának módjára vonatkozó útmutatásért.
-   [Marketo,](./cloud-partner-portal-lead-management-instructions-marketo.md) hogy az utasításokat a forgalomba Marketo vezető konfiguráció, hogy vezet.
-    [Salesforce,](./cloud-partner-portal-lead-management-instructions-salesforce.md) hogy utasításokat kapjon a Salesforce példány beállításához az érdeklődők lekérni.
-    [Az Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) az Azure storage-fiók beállítására vonatkozó utasításokat az Azure-táblában való érdeklődők leéséhez.
-   [Https Endpoint](./cloud-partner-portal-lead-management-instructions-https.md) a Https-végpont beállítására vonatkozó utasítások lekérni.

Miután konfigurálta az érdeklődő célhelyét és közzétette az ajánlatot, érvényesítjük a kapcsolatot, és elküldjük Önnek a tesztérdeklődőt. Amikor az ajánlat megtekintése előtt éles, akkor is tesztelheti a vezető kapcsolat, ha megpróbálja megszerezni az ajánlatot magad az előnézeti környezetben. Fontos, hogy az érdeklődőbeállítások naprakészek maradjanak, hogy ne veszítsd el az érdeklődőket, ezért mindenképpen frissítsd ezeket a kapcsolatokat, ha valami megváltozott a végén.

<a name="what-next"></a>Mi lesz a következő?
----------

Miután a technikai beállítás megtörtént, ezeket az érdeklődőket be kell építenie a jelenlegi értékesítési & marketingstratégiájába és működési folyamataiba. Nagyon szeretnénk jobban megérteni a teljes értékesítési folyamatot, és szorosan együtt kívánunk működni Önnel a kiváló minőségű érdeklődők és elegendő adat biztosításában ahhoz, hogy sikeres legyen. Örömmel fogadjuk visszajelzését arról, hogyan optimalizálhatjuk és javíthatjuk az általunk küldött érdeklődőket további adatokkal, hogy ezek az ügyfelek sikeresek legyenek. Kérjük, tudasd velünk, ha visszajelzést és javaslatokat szeretnél [adni,](mailto:AzureMarketOnboard@microsoft.com) hogy az értékesítési csapatod sikeresebb legyen a Marketplace-érdeklődőkkel.
