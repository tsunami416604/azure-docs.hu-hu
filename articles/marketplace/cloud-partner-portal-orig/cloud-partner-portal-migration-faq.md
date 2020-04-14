---
title: Gyakori kérdések a Partnerközpontba való áttelepítéssel kapcsolatban | Azure Piactér
description: Ez a cikk a Felhőpartner-portálról a Partnerközpontba való áttéréssel kapcsolatos gyakori kérdéseket ismerteti.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274379"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Gyakori kérdések a felhőpartneri portálról a Partnerközpontba való áttelepítéshez

Ez a cikk a felhőpartneri portálról a Partnerközpontba való áttéréssel kapcsolatos gyakori kérdéseket ismerteti.

## <a name="what-does-offer-migration-mean"></a>Mit jelent a migráció ajánlata?

Ajánlatadatait áthelyezzük a Cloud Partner Portalról a Partner centerbe az ajánlatközzétételi és -kezelési élmény változásaival.

| Terület  | Változások  |
|-------|----------|
| **Közzétételi és felügyeleti élmény** | A Partnerközpont intuitív kezelőfelületével továbbfejlesztett felhasználói élményben lesz része. További részletek: [Mi a különbség a Partnerközpont és a Felhőpartneri portál között?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Ajánlatai elérhetősége a piacon** | Nincs változás. Ha az ajánlat él a piacon, hogy továbbra is élő alatt és után az áttelepítés befejeződött. |
| **Új beszerzések és telepítések** | Nincs változás. Az ügyfelek továbbra is megszakítás nélkül vásárolhatják meg és telepíthetik az ajánlatokat. |
| **Kifizetések** | Az áttelepítés alatt vagy után végrehajtott vásárlások és telepítések továbbra is a szokásos módon kerülnek kifizetésre. |
|**API-integrációk a meglévő [Cloud Partner Portal API-kkal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | A meglévő felhőpartneri portál API-jai az áttelepítés után is támogatottak lesznek, és a meglévő integrációk továbbra is működni fognak. További részletekért [lásd: A felhőpartner-portál REST API-k támogatása](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) az áttelepítés után? |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Továbbra is hozzáférhetek a Cloud Partner Portálhoz, és kezelhetim az ajánlataimat az áttelepítés során?

Az ajánlatokat április 13-tól május 13-tól áttelepítik a Partnerközpontba. Ebben az időszakban a szokásos módon érheti el a Felhőpartner-portált, kivéve azt az időpontot, amikor az ön áttelepítésre van ütemezve.
Az ajánlatok áttelepítése közben a következő képernyőképen látható módon a "Zárolva – a Partnerközpontba való áthelyezés" állapotot kapják. Ennek az átállási időszaknak 24 óránál rövidebbnek kell lennie. Ez alatt az idő alatt nem tud frissítéseket készíteni az ajánlatairól. E-mailben értesítést kap, miután befejeztük az ajánlatok áttelepítését.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Az áttelepített ajánlatok állapotát mutatja be.":::

Az ajánlatok áttelepítése után **korlátozott ideig** csak olvasható módban lesznek a Cloud Partner Portalon. Állapotukban megjelenik az "Áthelyezve a Partnerközpontba", és tartalmaz egy hivatkozást az ajánlatra a Partnerközpontban, ahogy az az alábbi képernyőképeken is látható. Ettől a ponttól kezdve kezelheti az összes ajánlatának frissítéseit, vagy új ajánlatokat hozhat létre kizárólag a Partnercenteren keresztül,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="A Partnerközpontba áttelepített ajánlatoküzenetének szemléltetése":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Az áttelepített ajánlat Felhőpartnerportál-oldalát mutatja be.":::

## <a name="how-will-i-create-new-offers"></a>Hogyan hozhatok létre új ajánlatokat?

A Cloud Partner Portal portálon új ajánlatokat fog létrehozni a Partnerközpontban

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Bemutatja a menüt, hogy hozzon létre egy új ajánlatot a Cloud Partner Portal":::

Miután kiválasztott egy új ajánlatot, megjelenik egy üzenet, például az alábbiak.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Új ajánlat cpp-ben történő létrehozásakor kapott üzenet szemléltetése":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Létre kell hoznom egy új fiókot az ajánlatok kezeléséhez a Partnerközpontban?

Nem. Az alapul szolgáló fiók megmarad, és már a Partnerközpontban kell kezelnie. Ez azt jelenti, hogy ha Ön már meglévő partner, a meglévő Cloud Partner Portal-fiók hitelesítő adataival bejelentkezhet a Partnerközpontba az áttelepítés után. Csak az ajánlatok és a kapcsolódó felügyeleti élmény vált át a Felhőpartner-portálról a Partnerközpontra. Kérjük, hogy ne hozzon létre új fiókokat, mivel ajánlatai nem lesznek társítva az új fiókhoz.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Megjelenik egy üzenet a Cloud Partner Portal-on a fiókom aktiválásához, mit jelent ez?

További részletekre van szükségünk, hogy megfelelően áttudjuk telepíteni fiókját a Partnerközpontba, és lehetővé tehesítsük az ajánlatok kezelését a Partnerközpontban az ajánlatáttelepítés befejezése után. A fiókaktiválással kapcsolatos további részleteka [Fiókáttelepítés a Felhőpartner-portálról a Partnerközpontba című témakörben találhatók.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)

A fiók aktiválásának végrehajtásához szükséges lépések a fiókszerepkörtől függően változnak.

| Fiók szerepkör | Aktiválási lépések |
|--------------|----------------|
|Tulajdonos | Nyissa meg a Felhőpartner-portál [Közzétevői profil](https://cloudpartner.azure.com/#profile) lapját, majd az aktiváláshoz kattintson a szalagcímben lévő hivatkozásra. A rendszer átirányítja a Partnerközpontba a fiók aktiválásának befejezéséhez. |
| Közreműködő | Csak a tulajdonosi szerepkörrel rendelkező fiók egyik felhasználója aktiválhatja a fiókot. A fiók aktiválásának befejezéséhez lépjen kapcsolatba a fiók tulajdonosaival. A fióktulajdonosoknak szerepelniük kell a szalagcímüzenetben. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Nem tudok bejelentkezni a fiókomba, és támogatási jegyet nyitni

Ha nem tud bejelentkezni a fiókjába, megnyithat egy [támogatási jegyet.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Hol találhatok dokumentációt a Partnerközpont új közzétételi élményéhez?

Nyissa meg a [kereskedelmi piac dokumentációját.](https://docs.microsoft.com/azure/marketplace/) Ezután bontsa ki a **Kereskedelmi piactérportált a Partnerközpontban:**  > **Hozzon létre egy új ajánlatot,** és tekintse meg az egyes ajánlattípusok létrehozásához kapcsolódó súgótémaköröket.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="A Partnerközpont súgótémaköreit mutatja be":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Mi a különbség a Partnerközpont és a Felhőpartneri portál között?

A következő különbségeket veheti észre a Felhőpartner-portál és a Partnerközpont között.

### <a name="modular-publishing-capabilities"></a>Moduláris közzétételi lehetőségek

A Partnerközpont moduláris közzétételi lehetőséget biztosít, amely lehetővé teszi a közzétenni kívánt módosítások kiválasztását ahelyett, hogy mindig egyszerre tenné közzé az összes frissítést. A következő képernyőkép például azt mutatja, hogy csak a **Tulajdonságok** és az **Ajánlati lista**módosításai jelennek meg közzétéve.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Az Előnézet és közzététel lap szemléltetése":::

A nem közzétett frissítések piszkozatként kerülnek mentésre. Továbbra is használja az ajánlat előnézetét, hogy ellenőrizze az ajánlatot, mielőtt élőben elérhetővé teszi a nyilvánosság számára.

### <a name="rich-text-format"></a>Rich text formátum

Bővítse ajánlatát és tervének leírását az Ajánlatlista és a Csomaglista oldalon található Rich Text szerkesztő vel.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="A Rich Text szerkesztő tanait mutatja be":::

### <a name="enhanced-preview-options"></a>Továbbfejlesztett előnézeti beállítások

A Partnerközpont tartalmaz egy [összehasonlító funkciót](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) továbbfejlesztett szűrési lehetőségekkel. Ez lehetővé teszi az ajánlat előzetes és élő verzióinak összehasonlítását.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Az összehasonlítási funkció szemléltetése":::

### <a name="branding-and-navigation-changes"></a>A márkajelzés és a navigáció változásai

Észre fogod venni, néhány branding változásokat. A Partnerközpontban például a "SK-k" a "Tervek" nevet(k) nevezik.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="A terv áttekintését mutatja be.":::

A **Marketplace-en** vagy az S**torefront Details** (Consulting Service, Power BI app) oldalakon a Felhőpartner-portálon megadott adatokat a Partnerközpont **Ajánlat listaelem-oldalán** gyűjtjük össze.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Az ajánlatlista-oldal szemléltetése.":::

A Felhőpartner-portál egyetlen oldalán a sus-ok hoz adására használt adatok mostantól a Partnerközpont több oldalán is gyűjthetők:

* A tervezés beállítási lapja
* A listaelem megtervezése lap
* A Tervezés elérhetőségi lapja
* Tervezze meg a technikai konfigurációs oldalt, ahogy az a képernyőképen látható.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="A Tervezés technikai konfigurációja lapot mutatja be.":::

Az ajánlatazonosítója most antól megjelenik az ajánlat bal oldali navigációs sávján.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="A bal oldali navigációs menüt mutatja be az ajánlatazonosítóval.":::

### <a name="stop-selling-an-offer"></a>Ajánlat értékesítésének leállítása

Kérheti, hogy [ne értékesítsen egy ajánlatot](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) a piacon közvetlenül a Partner Center portálon. A lehetőség az **Ajánlat áttekintése** oldalon érhető el az ajánlathoz.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Az Ajánlat áttekintése oldalt mutatja be a stop selling opcióval.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>A Partnerközpontban található lapok a Felhőpartneri portálon használt oldalaknak felelnek meg?

Az alábbi táblázat a két portál közötti megfelelő kapcsolatokat mutatja be.

| Oldal | A Cloud Partner Portal hivatkozása | Partnerközpont hivatkozás |
|------|---------------------------|---------------------|
| **Minden ajánlat oldal** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Minden közzétevő oldal** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Közzétevő profilja** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Felhasználók oldal** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Előzmények lap** | https://cloudpartner.azure.com/#history | Az Előzmények funkciót még nem támogatja a Partnerközpont. |
| **Az Elemzések irányítópultja** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Kifizetési jelentés** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>A felhőpartneri portál REST API-jait támogatják az áttelepítés után?

A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át az alábbi táblázat módosításait, és győződjön meg arról, hogy a kód a Partnerközpontba való áttelepítés után is működik.

| **Api** | **Módosítások ismertetése** | **Hatás** |
| ------- | ---------------------- | ---------- |
| KÖZZÉTÉTEL, GoLive, Mégse | Az áttelepített ajánlatok esetében a válaszfejléc más formátumú lesz, de továbbra is ugyanúgy fog működni, és relatív elérési utat jelöli konkretizált a művelet állapotának beolvasásához. | Az ajánlathoz tartozó POST-kérelmek bármelyikének küldésekor a Hely fejléc az ajánlat áttelepítési állapotától függően két formátum valamelyikével fog rendelkezni:<ul><li>Nem áttelepített ajánlatok<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Áttelepített ajánlatok<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET művelet | A válaszban korábban támogatott "értesítési-e-mail" mezőt tartalmazó ajánlatok esetében ez a mező elavult, és az áttelepített ajánlatok hoz már nem kerül vissza. | Az áttelepített ajánlatok esetében a továbbiakban nem küldünk értesítéseket a kérelmekben megadott e-mailek listájára. Ehelyett az API-szolgáltatás igazodik az értesítési e-mail folyamathoz a Partner Központban az e-mailek küldéséhez. Pontosabban, értesítéseket küldünk az Eladó elérhetőségi adatait szakaszban beállított e-mail címre a Partner Center fiókbeállításaiban, hogy értesítsük Önt a művelet előrehaladásáról.<br><br>Tekintse át az e-mail címet, amelyet a Partnerközpont [Fiókbeállításai című rész Eladó](https://partner.microsoft.com/dashboard/account/management) elérhetősége szakaszában állított be, hogy megbizonyosodjon arról, hogy a megfelelő e-mail jelen van az értesítésekhez.  |
| | | |
