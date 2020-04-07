---
title: Az Azure-számla megtekintése és letöltése
description: A cikk az Azure-számlák megtekintését és letöltését mutatja be.
keywords: számlázás,számla,számla letöltése,azure-számla,azure-használat
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479011"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla megtekintése és letöltése

A számláját letöltheti az [Azure Portalról](https://portal.azure.com/), de e-mailben is megkaphatja. Amennyiben Ön Nagyvállalati Szerződéssel rendelkező Azure-ügyfél (EA-ügyfél), a szervezet számláit nem tudja letölteni. A rendszer annak a személynek küldi el a számlákat, akihez a regisztrációkor a számlák fogadása hozzá lett rendelve.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>A számlakiállítás ideje

A rendszer a számlázási fiók típusa alapján állítja ki a számlát. Ez lehet a Microsoft Online Services Program (MOSP), Microsoft-ügyfélszerződés (MCA) vagy Microsoft-partnerszerződés (MPA) számlázási fiókja. A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek számára is létrejönnek számlák. A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek számlái azonban nem jelennek meg az Azure Portalon.

A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókoknak az Azure Portalon történő megtekintését](../manage/view-all-accounts.md) ismertető cikket.

## <a name="invoices-for-mosp-billing-accounts"></a>Az MOSP számlázási fiókokhoz tartozó számlák

Az MOSP számlázási fiók létrehozása az Azure webhelyén, az Azure-regisztráció alkalmával történik. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Egyes régiókban, ha a felhasználó az Azure webhelyén regisztrál egy [használatalapú fizetést használó fiókot](https://azure.microsoft.com/offers/ms-azr-0003p/) vagy egy [ingyenes Azure-fiókot](https://azure.microsoft.com/offers/ms-azr-0044p/), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez.

Ha nem biztos a számlázási fiókja típusában, [Ellenőrizze a számlázási fiókja típusát](../manage/view-all-accounts.md#check-the-type-of-your-account), mielőtt követné az ebben a cikkben megadott utasításokat. 

Az MOSP számlázási fiók az alábbi számlákkal rendelkezhet:

**Azure-szolgáltatási díjak** – Létrejön egy számla minden Azure-előfizetéshez, amely tartalmazza az előfizetés által használt Azure-erőforrásokat. A számla egy számlázási időszak díjait tartalmazza. A számlázási időszakot a hónap azon napja határozza meg, amelyen az előfizetés létrejött.

Például John *01-es számú Azure-előfizetését* március 5-én, *02-es számú Azure-előfizetését* pedig március 10-én hozza létre. A *01-es számú Azure-előfizetés* számláján a hónap ötödik napjától a következő hónap negyedik napjáig szerepelnek díjak. A *02-es számú Azure-előfizetés* számláján a hónap tizedik napjától a következő hónap kilencedik napjáig szerepelnek a díjak. Az Azure-előfizetésekhez tartozó számlákat általában a fiók létrehozásának napján állítják ki, de erre akár két nappal később is sor kerülhet. Ebben a példában, ha John február 2-án hozta létre a fiókját, a *01-es számú Azure-előfizetéshez* és a *02-es számú Azure-előfizetéshez* tartozó számlák általában minden hónap második napján, de legkésőbb két nappal később készülnek el.

**Azure Marketplace, Reservations és Spot virtuális gépek** – A rendszer számlát állít ki az előfizetéssel megvásárolt foglalásokról, Marketplace-termékekről és Spot virtuális gépekről. A számlán az előző hónap vonatkozó díjai szerepelnek. Például John március 1-jén vásárolt egy foglalást, majd március 30-án vásárolt egy újabb foglalást. Áprilisban a két foglalásról egyetlen számla jön létre. Az Azure Marketplace, a Reservations és a Spot virtuális gépek számláját mindig a hónap kilencedik napja környékén állítják ki.

Ha hitelkártyával fizeti az Azure költségeit, és foglalást vásárol, az Azure azonnali számlát állít ki. Ha azonban számlán keresztül fizet, akkor a foglalást a következő havi számla fogja tartalmazni.

**Azure támogatási csomag** – A támogatási csomag előfizetéséhez a rendszer havonta állít ki számlát. A számla a vásárlás napján vagy legkésőbb két nappal később készül el. A további támogatási csomagokra vonatkozó számlák általában a fiók létrehozásának napján, vagy legkésőbb két napon belül jönnek létre minden hónapban.

## <a name="download-your-mosp-azure-subscription-invoice"></a>MOSP Azure-előfizetés számlájának letöltése

A rendszer csak olyan előfizetéshez állít ki számlát, amely egy MOSP számlázási fiókjához tartozik. [MOSP-fiókhoz való hozzáférés ellenőrzése](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Egy számla letöltéséhez fiókadminisztrátori szerepkörrel kell rendelkeznie a megfelelő előfizetésben. A tulajdonos, közreműködő vagy olvasó szerepkörű felhasználók letölthetik a számlát, ha a fiókadminisztrátor engedélyt adott számukra. További információ: [A számlák letöltésének engedélyezése a felhasználóknak](../manage/manage-billing-access.md#opt-in).

1. Válassza ki az előfizetését az Azure Portal [Előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. A számlázási szakaszban válassza a **Számlák** elemet.  
    ![Az előfizetés számlázási lehetőségének kiválasztását megjelenítő képernyőkép](./media/download-azure-invoice/select-subscription-invoice.png)
1. A PDF formátumú számla letöltéséhez válassza a **Letöltés** gombot, majd válassza a **Letöltés** lehetőséget a számlázási szakaszban.  
    [![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját ábrázoló képernyőkép](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. Letöltheti a felhasznált mennyiségek és a díjak napi bontását is, ha a **Letöltés** lehetőséget választja a használati adatok szakaszban. A CSV-fájl létrehozása eltarthat néhány percig.  
    ![Képernyőkép a Számlák és használati adatok letöltése oldalról](./media/download-azure-invoice/usage-and-invoice-subscription.png)

A számlájára vonatkozó további információért lásd [a Microsoft Azure-számla ismertetését](../understand/review-individual-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](../manage/getting-started.md) ismertető szakaszt.

## <a name="download-your-mosp-support-plan-invoice"></a>MOSP támogatási csomag számlájának letöltése

A rendszer csak olyan támogatási csomag előfizetéséhez állít ki számlát, amely egy MOSP számlázási fiókjához tartozik. [MOSP-fiókhoz való hozzáférés ellenőrzése](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Egy számla letöltéséhez fiókadminisztrátori szerepkörrel kell rendelkeznie a megfelelő támogatásicsomag-előfizetésben.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1.  Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/download-azure-invoice/search-cmb.png)
1.  A bal oldalon válassza a **Számlák** elemet.
1.  Jelölje ki a támogatási csomaghoz tartozó előfizetést, majd válassza a **Letöltés** lehetőséget.  
    [![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  A PDF formátumú számla letöltéséhez válassza a **Letöltés** gombot.  
    ![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját ábrázoló képernyőkép](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>MOSP-előfizetéshez tartozó számlák letöltésének engedélyezése mások számára

A számla PDF változata személyes információkat tartalmaz a fiókadminisztrátorról, így más felhasználók csak az előfizetés fiókadminisztrátorának engedélyével tölthetik le. Az engedély megadása után a következő, megfelelő szerepkörű személyek és felhasználók tölthetik le az előfizetés számláit:

- Felhasználók
- Csoportok
- Tulajdonossal rendelkező szolgáltatásnevek
- Közreműködő
- Olvasó
- Felhasználói hozzáférés rendszergazdája
- Számlázási olvasó
- Társadminisztrátor
- Szolgáltatás-rendszergazda

A számla letöltése:

1.  Jelentkezzen be az előfizetés fiókadminisztrátoraként az [Azure Portalra](https://portal.azure.com).
1.  Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
1.  A bal oldalon válassza a **Számlák** elemet.
1.  Válassza ki az Azure-előfizetését, majd válassza a **Számlához való hozzáférés** lehetőséget.  
    [![A számlához való hozzáférés megadását megjelenítő képernyőkép](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Válassza a **Be** lehetőséget, majd kattintson az oldal tetején lévő **Mentés** elemre.  
    ![A számlához való hozzáférés megadásának engedélyezését megjelenítő képernyőkép](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Az MOSP-előfizetés számláinak fogadása e-mailben

Az előfizetésnél fiókadminisztrátori szerepkörrel vagy támogatási csomaggal kell rendelkeznie, hogy engedélyezze a számla e-mailben való fogadását. Az e-mailes számlák csak előfizetések és támogatási csomagok esetén érhetők el, foglalásokhoz vagy az Azure Marketplace-beli vásárlásokhoz nem. Az engedélyezés után további címzetteket is felvehet, akik szintén megkapják a számlát e-mailben.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1.  Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
1.  A bal oldalon válassza a **Számlák** elemet.
1.  Jelölje ki az Azure-előfizetést vagy a támogatási csomaghoz tartozó előfizetést, majd válassza a **Számla elküldése e-mailben** lehetőséget.  
    [![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. Válassza a **Jóváhagyás** lehetőséget, és fogadja el a feltételeket.  
    ![A jóváhagyási folyamat második lépését ábrázoló képernyőkép](./media/download-azure-invoice/invoice-article-step02.png)
1. A számlát a rendszer a kívánt e-mail-címre küldi el. Az e-mail-címet a [számlázási fiókhoz megadott elérhetőségeknél](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties) frissítheti.  
    ![A jóváhagyási folyamat harmadik lépését ábrázoló képernyőkép](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>MOSP-számlák megosztása másokkal e-mailben

Érdemes lehet havonta megosztani az előfizetési és támogatási csomag számláit a számviteli csoporttal, vagy elküldeni őket egy másik saját e-mail-címére.

1. Kövesse [Az előfizetés és a támogatási csomag számláinak fogadása e-mailben](#get-mosp-subscription-invoice-in-email) című szakaszban leírt lépéseket, és válassza a **Címzettek konfigurálása** lehetőséget.  
    ![A címzettek konfigurálásának kiválasztását megjelenítő képernyőkép](./media/download-azure-invoice/invoice-article-step03.png)
1. Adjon meg egy e-mail-címet, majd válassza a **Címzett hozzáadása** elemet. Több e-mail-címet is hozzáadhat.  
    ![A további címzettek hozzáadását megjelenítő képernyőkép](./media/download-azure-invoice/invoice-article-step04.png)
1. Az összes e-mail-cím hozzáadása után kattintson a képernyő alján található **Kész** gombra.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Az MCA és MPA számlázási fiókokhoz tartozó számlák

Az MCA számlázási fiók akkor jön létre, amikor a szervezet a Microsoft képviselőjével együttműködve Microsoft-ügyfélszerződést köt. Egyes régiókban, ha a felhasználó az Azure-webhelyen regisztrál egy [használatalapú fizetést használó fiókot](https://azure.microsoft.com/offers/ms-azr-0003p/) vagy egy [ingyenes Azure-fiókot](https://azure.microsoft.com/offers/ms-azr-0044p/), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez is. További információ: [Az MCA számlázási fiók használatának első lépései](../understand/mca-overview.md).

A Microsoft-partnerszerződés (MPA) számlázási fiókjai a felhőszolgáltatói partnerek (CSP-k) számára készülnek az új kereskedelmi felületen történő ügyfélkezeléshez. A partnereknek legalább egy [Azure-csomaggal](https://docs.microsoft.com/partner-center/purchase-azure-plan) rendelkező ügyfélre van szükségük ahhoz, hogy számlázási fiókjukat az Azure Portalon kezelhessék. További információ: [Az MPA számlázási fiók használatának első lépései](../understand/mpa-overview.md).

A hónap elején a fiókhoz tartozó összes számlázási profilhoz létrejön egy havi számla. A számla az Azure-előfizetések és egyéb vásárlások előző havi díjait tartalmazza. Például John *01-es számú Azure-előfizetését* március 5-én, *02-es számú Azure-előfizetését* pedig március 10-én hozta létre. A *01-es számú Azure támogatási* előfizetést március 28-án vásárolta meg a *01-es számú számlázási profillal*. John április elején egyetlen, az Azure-előfizetések és a támogatási csomag díjait összesítő számlát fog kapni.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Az MCA vagy az MPA számlázási profiljához tartozó számla letöltése

Az Azure Portalon található számlák letöltéséhez tulajdonosi, közreműködői, olvasói vagy számlakezelői szerepkörrel kell rendelkeznie az adott számlázási profilban. Egy számlázási fiók tulajdonosi, közreműködői vagy olvasói szerepkörével rendelkező felhasználó a fiók összes számlázási profiljához tartozó számla letöltésére jogosult.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1.  Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
1. A bal oldalon válassza a **Számlák** elemet.
    [![Az MCA számlázási fiók számlázási oldalát megjelenítő képernyőkép](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. A számlákat tartalmazó táblázatból válassza ki a letölteni kívánt számlát.
1. Válassza a **PDF-formátumú számla letöltése** elemet az oldal tetején.  
    ![A pdf-formátumú számla letöltését megjelenítő képernyőkép](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. Az **Azure használati adatainak letöltése** elem kiválasztásával letöltheti a felhasznált mennyiségek és a becsült költségek napi részletezését tartalmazó CSV-fájlt. A CSV-fájl előkészítése eltarthat néhány percig.

## <a name="get-your-billing-profiles-invoice-in-email"></a>A számlázási profil számláinak e-mailes kézbesítése

A számlázási profil vagy a számlázási fiók e-mailes számlaküldési beállításainak frissítéséhez tulajdonosi vagy közreműködői szerepkörrel kell rendelkeznie. A jóváhagyás után a számlázási profil minden tulajdonosi, közreműködői, olvasói vagy számlakezelői szerepkörrel rendelkező felhasználója megkapja a számlát e-mailben. 

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1.  Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
1.  A bal oldalon válassza a **Számlák** lehetőséget, majd a lap tetején található **Számla küldése e-mailben** elemet.  
    [![Az MCA számlázási fiók számlázási oldalát megjelenítő képernyőkép](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Ha több számlázási profilja van, válasszon ki egy számlázási profilt, majd kattintson a **Jóváhagyás** elemre.  
    ![Az MCA számlázási fiók számlázási oldalát megjelenítő képernyőkép](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Válassza a **Frissítés** lehetőséget.

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>Az MCA vagy az MPA számlákhoz való hozzáférés biztosítása másoknak

Engedélyezheti másoknak a számlák megtekintését, letöltését és befizetését, ha számlakezelő szerepkört rendel hozzájuk az MCA vagy az MPA számlázási profilra vonatkozóan. Ha a számlák e-mailben való fogadását választotta, akkor a felhasználók is megkapják a számlákat e-mailben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
1. Válassza a **Számlázási profilok** lehetőséget a bal oldalon. A számlázási profilok listájában válassza ki azt a számlázási profilt, amelyhez számlakezelő szerepkört szeretne rendelni.  
   ![Képernyőkép a számlázási profilok listájáról](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. A bal oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd a lap tetején található **Hozzáadás** elemet.  
    ![Képernyőkép a hozzáférés-vezérlési lapról](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. A Szerepkör legördülő listában válassza a **Számlakezelő** elemet. Adja meg annak a felhasználónak az e-mail-címét, aki számára hozzáférést kíván biztosítani. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.  
   ![Képernyőkép egy felhasználó számlakezelőként való hozzáadásáról](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> Lehetséges okok, amiért nem láthatja a számlát:

Számos oka lehet annak, hogy nem jelenik meg számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta. 
- Az Azure néhány nappal a számlázási időszak után küld Önnek számlát. Lehetséges, hogy még nem készült számla.
- Nem rendelkezik engedéllyel a számlák megtekintéséhez.
    Az MCA vagy MPA számlázási fiók számláinak megtekintéséhez a számlázási profilban tulajdonosi, közreműködői, olvasói vagy számlakezelői szerepkörrel, a számlázási fiókban pedig tulajdonosi, közreműködői vagy olvasói szerepkörrel kell rendelkeznie. Más előfizetések esetén előfordulhat, hogy nem látja a számlákat, ha nem Ön a fiókadminisztrátor. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](../manage/manage-billing-access.md).
- Ha a MOSP számlázási fiókkal rendelkezik, illetve ha ingyenes Azure-fiókra vagy havi keretösszeggel rendelkező előfizetésre regisztrált, csak akkor fog számlát kapni, ha túllépi a havi kreditösszeget. Ha MCA vagy MPA számlázási fiókkal rendelkezik, minden esetben kap számlát.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a díjakkal kapcsolatos további információkért lásd:

- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](download-azure-daily-usage.md)
- [Microsoft Azure-elszámolások értelmezése](review-individual-bill.md)
- [Az Azure-számla feltételeinek értelmezése](understand-invoice.md)

Ha MCA-fiókkal rendelkezik, tekintse meg az alábbi témaköröket:

- [A számlázási profilhoz tartozó számla díjainak ismertetése](review-customer-agreement-bill.md)
- [A számlázási profilhoz tartozó számla feltételeinek ismertetése](mca-understand-your-invoice.md)
- [A számlázási profiljához tartozó, az Azure használati adatait és díjait tartalmazó fájl ismertetése](mca-understand-your-usage.md)


