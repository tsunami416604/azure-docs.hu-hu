---
title: Gyakran ismételt kérdések a partneri központba való áttelepítésről | Azure piactér
description: Ez a cikk az ajánlatok Cloud Partner Portalról a partneri központba való áttelepítésével kapcsolatos gyakori kérdéseket tárgyalja.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274379"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Gyakori kérdések a Cloud Partner Portalról a partneri központba való Migrálás során

Ez a cikk az ajánlatok Cloud Partner Portalról a partneri központba való áttelepítésével kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="what-does-offer-migration-mean"></a>Mit jelent az ajánlatok áttelepítése?

Áthelyezjük az ajánlat adatait a Cloud Partner Portalról a partneri központba az ajánlat-közzétételi és-kezelési élmény változásaival.

| Terület  | Változások  |
|-------|----------|
| **Közzétételi és ajánlat-kezelési élmény** | A partner Centerben intuitív kezelőfelülettel javíthatja a felhasználói élményt. További részletekért lásd: a [partner központ és a Cloud Partner Portal közötti különbségek?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Az ajánlatok elérhetősége a piactéren** | Nincs változás. Ha az ajánlata a piactéren is elérhető, akkor az áttelepítés befejezése után és után is folytatódik. |
| **Új vásárlások és központi telepítések** | Nincs változás. Ügyfelei továbbra is megvásárolhatják és üzembe helyezhetik az ajánlatait megszakítás nélkül. |
| **Kifizetések** | Az áttelepítés során vagy azt követően megjelenő vásárlások és telepítések továbbra is a szokásos módon lesznek kifizetve. |
|**API-integrációk meglévő [Cloud Partner Portal API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) -kkal** | A meglévő Cloud Partner Portal API-k továbbra is támogatottak lesznek az áttelepítés után, és a meglévő integrációk továbbra is működni fognak. További részletek: [a Cloud Partner Portal REST API-k támogatása az áttelepítés után?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Továbbra is hozzáférhetek a Cloud Partner Portal és kezelhetem az ajánlatokat az áttelepítés során?

Az ajánlatokat a partnervállalat április 13-ától kezdődően telepítjük a partner Centerbe. Ebben az időszakban a szokásos módon férhet hozzá a Cloud Partner Portalhoz, kivéve, ha az Ön által megadott idő áttelepítésre van ütemezve.
Az ajánlatok migrálása során a rendszer a következő képernyőképen látható módon "zárolva – áthelyezi a partneri központba" állapotot fogja tartalmazni. Az áttelepítési időszaknak 24 óránál rövidebbnek kell lennie. Ebben az időszakban nem fog tudni frissítéseket készíteni az ajánlatokról. Az ajánlatok áttelepítésének befejezése után e-mail-értesítést fog kapni.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Az áttelepített ajánlatok állapotát mutatja.":::

Az ajánlatok migrálása után a Cloud Partner Portal **egy korlátozott** ideig csak olvasható módban lesznek. Az állapotuk "áthelyezve a partner központba" jelenik meg, és az ajánlatra mutató hivatkozást is tartalmaz a partner Centerben, ahogy az alábbi képernyőképeken is látható. Ebből a pontból az összes ajánlat frissítését kezelheti, vagy kizárólag a partner centeren keresztül hozhat létre új ajánlatokat.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="A partner központba áttelepített ajánlatok számára megadott üzenet bemutatása":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Az áttelepített ajánlat Cloud Partner Portal lapját mutatja be.":::

## <a name="how-will-i-create-new-offers"></a>Hogyan hozhatok létre új ajánlatokat?

A Cloud Partner Portal új ajánlatokat hoz létre a partner Centerben

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Az új ajánlat létrehozásához szükséges menüt mutatja be Cloud Partner Portal":::

Az új ajánlat kiválasztása után egy üzenet jelenik meg, például a következő.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Az új ajánlat létrehozásakor kapott üzenetet mutatja be a CPP-ben":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Létre kell hozni egy új fiókot az ajánlatok kezeléséhez a partner Centerben?

Nem. A rendszer megőrzi a mögöttes fiókját, és azt a partner Centerben kell kezelni. Ez azt jelenti, hogy ha Ön már meglévő partner, használhatja a meglévő Cloud Partner Portal fiókjának hitelesítő adatait, hogy bejelentkezzen a partner Centerbe a Migrálás után. A rendszer csak az ajánlatokat és a kapcsolódó felügyeleti élményt helyezi át a Cloud Partner Portal a partner központba. Azt kérjük, hogy ne hozzon létre új fiókot, mert az ajánlatok nem lesznek társítva az új fiókhoz.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Meglátok egy üzenetet a Cloud Partner Portal a fiókom aktiválásához? mit jelent ez?

További részletekre van szükségünk ahhoz, hogy megfelelően áttelepítse a fiókját a partner központba, és lehetővé tegye, hogy az ajánlat migrálása után kezelhesse az ajánlatokat a partner Centerben. A fiók aktiválásával kapcsolatos további információkért lásd: [fiók áttelepítése Cloud Partner Portalról a partner központjába](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

A fiók aktiválásának befejezéséhez szükséges lépések a fiók szerepköre alapján változnak.

| Fiók szerepköre | Aktiválási lépések |
|--------------|----------------|
|Tulajdonos | Lépjen a Cloud Partner Portal [közzétevői profil](https://cloudpartner.azure.com/#profile) lapjára, majd kattintson a szalagcímben található hivatkozásra az aktiváláshoz. A fiók aktiválásának befejezéséhez a rendszer átirányítja a partner központba. |
| Közreműködő | A fiók csak a tulajdonosi szerepkörrel rendelkező felhasználók számára aktiválható. Forduljon a fiók tulajdonosainak a fiók aktiválásának befejezéséhez. A fiók tulajdonosainak szerepelniük kell a szalagcím üzenetben. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Gondjaim vannak a fiókomba való bejelentkezéssel és támogatási jegy megnyitásával

Ha nem tud bejelentkezni a fiókjába, nyisson meg egy [támogatási jegyet](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Hol találhatok dokumentációt az új partner Center Publishing Experience szolgáltatással kapcsolatban?

Nyissa meg a [kereskedelmi piactér dokumentációját](https://docs.microsoft.com/azure/marketplace/). Ezután bontsa ki a **kereskedelmi piactér portált a partner Centerben**  > , és**hozzon létre egy új ajánlatot** , ahol megtekintheti az egyes típusú ajánlatok létrehozásához szükséges súgótémaköröket.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="A partner centerrel kapcsolatos súgótémakörök bemutatása":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Mi a különbség a partner központ és a Cloud Partner Portal között?

Észreveheti, hogy a Cloud Partner Portal és a partner központ között a következő különbségek vannak.

### <a name="modular-publishing-capabilities"></a>Moduláris közzétételi képességek

A partner Center egy moduláris közzétételi lehetőséget biztosít, amellyel kiválaszthatja a közzétenni kívánt módosításokat, ahelyett, hogy mindig az összes frissítést közzéteszi. A következő képernyőkép például azt mutatja, hogy az egyetlen közzétételre kijelölt módosítások a **Tulajdonságok** és az **ajánlati lista**módosításai.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Az előnézet és a közzétételi oldal bemutatása":::

A nem közzéteendő frissítések piszkozatként lesznek mentve. Továbbra is használhatja az ajánlat előzetesét, hogy ellenőrizze az ajánlatát, mielőtt a nyilvánosságra tenné.

### <a name="rich-text-format"></a>Rich Text formátum

Bővítse ajánlatát, és tervezze meg a leírást a részletes szövegszerkesztő használatával az ajánlati lista és a csomag listázása oldalon.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="A Rich Text Editor bemutatása":::

### <a name="enhanced-preview-options"></a>Bővített előnézet beállításai

A partner Center egy [összehasonlítási funkcióval](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) bővült, amely továbbfejlesztett szűrési lehetőségekkel rendelkezik. Ez lehetővé teszi, hogy összehasonlítsa az ajánlat előzetes és élő verzióit.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Az összehasonlítás funkció bemutatása":::

### <a name="branding-and-navigation-changes"></a>Védjegyezési és navigációs változások

Megfigyelheti néhány védjegyezési változást. A "SKU" például "csomagok" a partner Centerben.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="A terv áttekintését mutatja be.":::

Továbbá a **piactéren** vagy Cloud Partner Portal a**torefront** található információk (tanácsadási szolgáltatás, Power bi alkalmazás) oldalain megadható információkat a rendszer a partner Center **ajánlati lista** lapján gyűjti.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Az ajánlati lista oldalát szemlélteti.":::

Az SKU-nak a Cloud Partner Portal egyetlen lapján való megadásához használt információk mostantól a partner központ több oldalán is összegyűjthetők:

* Terv beállítása lap
* A lista megtervezése lap
* Csomag rendelkezésre állása lap
* Tervezze meg a technikai konfiguráció oldalát, ahogy az a képernyőképen is látható.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="A terv technikai konfigurációs oldalának bemutatása.":::

Az ajánlat azonosítója mostantól az ajánlat bal oldali navigációs sávján jelenik meg.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="A bal oldali navigációs menüt mutatja az ajánlat azonosítójával.":::

### <a name="stop-selling-an-offer"></a>Ajánlat értékesítésének leállítása

Kérheti, hogy közvetlenül a partner Center portálról [állítsa le az ajánlat értékesítését](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) a piactéren. A lehetőség az ajánlat **Áttekintés** lapján érhető el.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Az ajánlat – áttekintés oldalt mutatja az értékesítés leállítása lehetőséggel.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>A partner Centerben milyen lapok tartoznak a Cloud Partner Portal használt lapokhoz?

A következő táblázat a két portál közötti megfelelő hivatkozásokat tartalmazza.

| Oldal | Cloud Partner Portal hivatkozás | Partneri központ hivatkozása |
|------|---------------------------|---------------------|
| **Minden ajánlat oldal** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Minden közzétevő oldal** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Közzétevői profil** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Felhasználók oldal** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Előzmények lap** | https://cloudpartner.azure.com/#history | Az előzmények funkció még nem támogatott a partner Centerben. |
| **Az áttekintések irányítópultja** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Kifizetési jelentés** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Támogatott-e a Cloud Partner Portal REST API-k támogatása az áttelepítés után?

A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át az alábbi táblázatban szereplő módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

| **API** | **Módosítások ismertetése** | **Hatás** |
| ------- | ---------------------- | ---------- |
| Közzététel utáni, GoLive, Mégse | Az áttelepített ajánlatok esetében a válasz fejlécének formátuma eltérő lesz, de továbbra is ugyanúgy működik, mint a művelet állapotának lekéréséhez szükséges relatív elérési út. | Az ajánlathoz tartozó BEJEGYZÉSi kérelmek bármelyikének elküldésekor a hely fejléce az ajánlat áttelepítési állapotának függvényében két formátummal fog rendelkezni:<ul><li>Nem áttelepített ajánlatok<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Áttelepített ajánlatok<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Művelet beolvasása | A válaszban korábban a "Notification-mail" mező által támogatott ajánlatok esetében ez a mező elavult, és az áttelepített ajánlatok esetében már nem lesz visszaküldve. | Az áttelepített ajánlatok esetében a továbbiakban nem küldünk értesítéseket a kérelmekben megadott e-mailek listájára. Ehelyett az API szolgáltatás egyezteti az e-mailek küldését a partner Centerben az értesítő e-mail folyamattal. Az értesítéseket a rendszer a partner Center fiók beállításainak eladó kapcsolattartási adatok szakaszában megadott e-mail-címre küldi el, hogy értesítse a művelet előrehaladásáról.<br><br>Tekintse át az e-mail-címet a fiókpartner [fiók beállításai](https://partner.microsoft.com/dashboard/account/management) között, az eladó kapcsolattartási adatai szakaszban, és győződjön meg arról, hogy a megfelelő e-mail-cím van megadva az értesítésekhez.  |
| | | |
