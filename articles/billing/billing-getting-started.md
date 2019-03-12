---
title: Váratlan költségek megelőzése, a számlázás az Azure-ban kezelheti |} A Microsoft Docs
description: Útmutató az Azure-elszámolások a váratlan költségek elkerülése érdekében. Költségek nyomon követése és felügyeleti funkciók használata a Microsoft Azure-előfizetés.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: 56d392a7df2a659cf457631ec5693766fd674f91
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727195"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése

Feliratkozás az Azure-hoz, több dolgot is van, a kiadások megvilágításához. A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) költségek becslést ad egy Azure-erőforrás létrehozása előtt. A [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) biztosítja az aktuális költségek részletezése és előrejelzés az előfizetéshez. Ha a csoportot, és költségeit a különböző projektek vagy fejlesztőcsapatok számára szeretne, keresse meg [erőforrás-címkézési](../azure-resource-manager/resource-group-using-tags.md). Ha a szervezete jelentési rendszer, amely szeretné használni, tekintse meg a [számlázási API-jai](billing-usage-rate-card-overview.md).

- Ha az előfizetés egy nagyvállalati szerződés (EA), a nyilvános előzetes jelennek meg a költségek az Azure Portalon érhető el. Ha az előfizetés Cloud Solution Provider (CSP) vagy az Azure sponsorship ajánlattal keresztül, majd a következő funkciók némelyike nem alkalmazható Önnek. Lásd: [további erőforrások az EA, CSP és szponzorálási](#other-offers) további információ.

- Ha az előfizetés egy ingyenes próbaverzióra [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (aio-ra), vagy a BizSpark, az előfizetés automatikusan letiltja, ha az a kreditek felhasználása. Ismerje meg [költségkorlátozási](#spending-limit) váratlanul le van tiltva az előfizetés ne kelljen.

- Ha már regisztrált a [ingyenes Azure-fiók](https://azure.microsoft.com/free/), [néhány, a legnépszerűbb Azure-szolgáltatások 12 hónapig ingyenesen használható](billing-create-free-services-included-free-account.md). Lásd az alábbi javaslatokat, valamint [elkerülése az ingyenes fiók számolják](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Becsült költségek lekérése az Azure-szolgáltatás hozzáadása előtt

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>A díjkalkulátor használatával online költség becslése

Tekintse meg a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) beolvasni az Önt érdeklő szolgáltatás havi becsült költség. Bármely belső Azure-erőforrás beolvasni egy becsült költség is hozzáadhat.

![A díjszabási Számológép menü képernyőképe](./media/billing-getting-started/pricing-calc.png)

Például egy a1-es Windows virtuális gép (VM) becsült 66.96 USD/hónap cost számítási óra, ha meghagyja a futó teljes idő:

![A díjkalkulátor, hogy az a1-es Windows virtuális gép cost 66.96 USD havi becsült megjelenítő képernyőkép](./media/billing-getting-started/pricing-calcVM.png)

Díjszabási információkért tekintse meg ezt [– gyakori kérdések](https://azure.microsoft.com/pricing/faq/). Vagy ha azt szeretné, egy Azure értékesítő kommunikáljon, lépjen kapcsolatba az 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Tekintse át az Azure Portalon a becsült költség

Általában amikor hozzáad egy szolgáltatás az Azure Portalon, nincs olyan nézet, amely bemutatja, egy hasonló havi becsült költség. Például ha úgy dönt, hogy a Windows virtuális gép méretét, láthatja a becsült havi költség a számítási órák:

![Példa: egy a1-es Windows virtuális gép becsült cost 66.96 USD / hó](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="spending-limit"></a> Ellenőrizze, hogy a költségkeret maximumát

Ha egy előfizetés által használt kreditek, majd a költségkeret van kapcsolva, alapértelmezés szerint. Ezzel a módszerrel, amelyet használhat fel a kreditek, ha a megadott bankkártyára nem kell fizetnie. Tekintse meg a [teljes listája megtalálható az Azure és a költségkeret elérhetősége](https://azure.microsoft.com/support/legal/offer-details/).

Azonban ha eléri a költségkeretet, a szolgáltatások letiltásának. Ez azt jelenti, hogy a virtuális gépek felszabadított állapotban vannak. Szolgáltatás állásidő elkerülése érdekében a költségkeret ki kell kapcsolnia. Bármely kerettúllépést – a lekérdezi a hitelkártyára terheljük az alakzatot kell fizetnie. 

Ha Ön már rendelkezik költségkeretet a megtekintéséhez nyissa meg a [előfizetések megtekintéséhez az Account Center webhelyen](https://account.windowsazure.com/Subscriptions). Egy szalagcím jelenik meg, ha a költségkeretet:

![Képernyőkép a kiadásokat, korlátot a folyamatban, az Account Center webhelyen kapcsolatos figyelmeztetés](./media/billing-getting-started/spending-limit-banner.PNG)

Kattintson a szalagcímre, és útmutatást követve el kell távolítania a költségkeretet. Ha a hitelkártyaadatokat való regisztráció során adta meg, meg kell adnia, hogy el kell távolítania a költségkeretet. További információkért lásd: [az Azure-költségkeret működési elve és engedélyezése, vagy távolítsa el](https://azure.microsoft.com/pricing/spending-limits/).

Használhatja a [Cloudyn](https://www.cloudyn.com/) szolgáltatás létre riasztásokat, amelyek automatikusan értesíti a résztvevőket kiadások rendellenességeket és a túlköltekezés kockázatokat. A jelentésekkel, hogy támogatási riasztásokat a költségvetés alapján, és a költségek küszöbértékei riasztásokat is létrehozhat. A Cloudyn használatával kapcsolatos további információkért lásd: [oktatóanyag: Tekintse át a használat és költségek](../cost-management/tutorial-review-usage.md).

Ez a példa a **tényleges időalapú költségeket tartalmazó** jelentés értesítés küldése, amikor egy Azure-beli virtuális gépen a kiadások érvényessége a teljes költségvetést. Ebben a forgatókönyvben 20 000 teljes költségvetése rendelkezik, és értesítést kaphat, ha a költségek hamarosan eléri a költségvetést, $9000 és további riasztás fele amikor a költségek elérik a 10 000 dollárt szeretné.

1. A Cloudyn portál tetején lévő menüben válassza ki a **költségek** > **költségelemzés** > **tényleges időalapú költségeket tartalmazó**. 
2. A **Groups** (Csoportok) alatt állítsa be a **Service** (Szolgáltatás), a **Filter on the service** (Szűrés a következő szolgáltatásra) alatt pedig az **Azure/VM** (Azure/virtuális gép) lehetőséget. 
3. Felső válassza ki a jelentést, jobb **műveletek** majd **jelentés ütemezése**.
4. Használatával küldjön saját magának egy e-mailt a jelentés ütemezett időközönként, válassza ki a **ütemezés** lapján a **menteni vagy ütemezheti** jelentés párbeszédpanel. Ügyeljen arra, hogy a **Send via email** (Küldés e-mailben) beállítás legyen kiválasztva. Minden olyan címkék, csoportosítás és a szűrés, az e-mailben elküldött jelentésben szereplő használja. 
5. Válassza ki a **küszöbérték** lapot, majd **Actual Cost vs. Threshold** (Tényleges költségek a küszöbértékhez képest) lehetőséget. 
   1. Az a **vörös színű riasztásra** küszöbérték mezőbe írja be a 10000. 
   2. Az a **sárga riasztást** küszöbérték mezőbe írja be a 9000. 
   3. Az a **egymást követő riasztások számát** fogadásához egymást követő riasztások számát adja meg. Ha a riasztások száma eléri a megadott számot, a rendszer nem küld további riasztásokat. 
6. Kattintson a **Mentés** gombra.

    ![A példában a költségkeret-beállítási küszöbértékek alapján piros, sárga és riasztások megjelenítése](./media/billing-getting-started/schedule-alert01.png)

Azt is beállíthatja a **Cost Percentage vs. Költségvetés** költségszázalék a riasztásokat. Ez lehetővé teszi, hogy a küszöbérték megadása helyett a költségvetés százalékos.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>A költségek monitorozása az Azure-szolgáltatások használatakor módjai

### <a name="tags"></a> Címkék hozzáadása az erőforrások az elszámolási adatok

A támogatott szolgáltatások címkék a számlázási adatokhoz csoport is használhatja. Például ha futtatja a különböző csapatok számára a több virtuális gépet, majd segítségével címkék költséghely (HR, marketing, pénzügyi) vagy a környezet (éles üzem előtti, test) költségek kategorizálása. 

![Képernyőkép a címkék beállítása a portálon](./media/billing-getting-started/tags.PNG)

A címkék jelennek meg egész nézetek reporting különböző költség. Például, azok megjelennek a [költségek elemzése nézet](#costs) most és a részletes használati .csv az első számlázási időszak után.

További információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Rendszeresen ellenőrizze a portálon a költségek részletezése és írási sebesség

Miután beszerezte a futó szolgáltatások, rendszeresen ellenőrzi azok, amelyek IP-címek fenntartási ára van. Tekintse meg az aktuális költségek, és írási sebesség az Azure Portalon.

1. Látogasson el a [előfizetések az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , és válasszon ki egy előfizetést.

2. Ha azt az előfizetéséhez, tekintse meg a költségek lebontását, és írási sebesség.

    ![Képernyőkép a írási sebesség és az Azure Portalon lebontása](./media/billing-getting-started/burn-rate.PNG)

3. Kattintson a **költségelemzés** a bal oldalon látható a költségek lebontását erőforrás szerint a listában. Várjon 24 órát, miután hozzáadta a szolgáltatás az adatok feltöltéséhez.

    ![A cost analysis nézet az Azure portal képernyőképe](./media/billing-getting-started/cost-analysis.PNG)

4. Különböző tulajdonságok, például szűrhet [címkék](#tags), erőforrás típusa, az erőforráscsoport és az időtartam. Kattintson a **alkalmaz** megerősítéséhez a szűrőket és **letöltése** Ha azt szeretné, exportálhatja a nézet egy Comma-Separated értékeket tartalmazó (.csv) fájlt.

5. Ezenkívül, kattintson egy erőforrás előzmények és az IP-címek fenntartási költségei az az erőforrás a minden nap a napi megtekintéséhez.

    ![A költségek előzmények megtekintése az Azure portal képernyőképe](./media/billing-getting-started/costhistory.PNG)

Javasoljuk, hogy ellenőrizze a becsléseket látott a szolgáltatások kiválasztásakor megjelenik a költségeket. Ha a kampánya eltér a becslések, a díjszabással az erőforrások kiválasztott ellenőrizze díjakat.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Érdemes lehet engedélyezni az általános költségek szolgáltatások, mint az automatikus leállítás a virtuális gépek

A forgatókönyvtől függően konfigurálhatja úgy az automatikus leállítás a virtuális gépek az Azure Portalon. További információkért lásd: [az Azure Resource Manager-beli virtuális gépek automatikus leállítási](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Képernyőkép az automatikus Leállítás lehetőséget a portálon](./media/billing-getting-started/auto-shutdown.PNG)

Automatikus leállítás nem ugyanaz, mint amikor leállítja a virtuális gépen az Energiagazdálkodási lehetőségek. Automatikus leállítás leállítja és felszabadítja a virtuális gépek leállítása további használati díjak. További információkért tekintse meg a díjszabással kapcsolatos gyakori kérdések a [Linux rendszerű virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) kapcsolatos virtuális gépek állapotáról.

További költség általános funkciókat a fejlesztési és tesztelési környezetek esetében, tekintse meg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Kapcsolja be, és tekintse meg az Azure Advisor-javaslatok

[Az Azure Advisor](../advisor/advisor-overview.md) olyan szolgáltatás, amely segít a költségek csökkentése az alacsony kihasználtságú erőforrásokat azonosításával. Az Advisor felkeresése az Azure Portalon:

![Képernyőkép az Azure Advisor gomb az Azure Portalon](./media/billing-getting-started/advisor-button.PNG)

Ezt követően kezdheti gyakorlati ajánlásokat készítünk a **költség** az Advisor irányítópult lapján:

![Képernyőkép az Advisor költség javaslat példa](./media/billing-getting-started/advisor-action.PNG)

További információkért lásd: [Advisor díjakkal kapcsolatos ajánlások](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Az Ön számlázási ciklusa végén költségek áttekintése

A számlázási ciklus végén a számla elérhetővé válik. Emellett [letöltheti a korábbi számlák és használati fájlokat részletességi](billing-download-azure-invoice-daily-usage-date.md) , győződjön meg arról, hogy helyesen lett díj felszámítva. A napi használat a számlán az összehasonlításával kapcsolatban további információkért lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md).

### <a name="billing-api"></a>Számlázási API

A számlázási API-val programozott módon a használati adatok lekéréséhez. A RateCard API-t és a használati API-val együtt történő beolvasásához használja a számlázott használat. További információkért lásd: [betekintést nyerhet a Microsoft Azure erőforrás-használat](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> További erőforrások és a speciális esetekben

### <a name="ea-csp-and-sponsorship-customers"></a>EA, CSP és szponzorálási ügyfelek
Kérdezze meg a ügyfélfelelőshöz vagy a Azure-partnerrel, a kezdéshez.

| Ajánlat | További források |
|-------------------------------|-----------------------------------------------------------------------------------|
| Nagyvállalati Szerződés (EA) | [A nagyvállalati szerződések portáljának](https://ea.azure.com/), [docs súgó](https://ea.azure.com/helpdocs), és [Power BI-jelentés](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (Felhőszolgáltató) | A szolgáltató beszélgetés |
| Azure-szponzorálás | [Szponzorálás portálon](https://www.microsoftazuresponsorships.com/) |

Ha a felügyeli informatikai egy nagy szervezet, javasoljuk, hogy olvassa [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) és a [vállalati informatikai tanulmány](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf letöltés, csak angol nyelven).

#### <a name="EA"></a> Előzetes verzió nagyvállalati szerződés Azure Portalon nézetek költség 

Vállalati költségek nézet jelenleg nyilvános előzetes verzióban érhető el. Figyelembe veendő elemek:

- Előfizetés költségek használati alapulnak, és nem tartalmazza az előre fizetett összegek, túlhasználattal, benne foglalt tranzakciómennyiségek, módosítását és adók. Tényleges költségek a regisztráció szintjén vannak kiszámítva.
- Mi az a vállalati portál az Azure Portalon látható összegek eltérhet. A vállalati portál frissítései is igénybe vehet néhány percet, mielőtt a módosítások megjelennek az Azure Portalon.
- Ha nem találja a költségek, a következő okok valamelyike lehet:
    - Nincs engedélye az előfizetés szintjén. Vállalati költségek nézet jelenik meg, hogy egy számlázási olvasó, olvasó, közreműködő vagy tulajdonos az előfizetés szintjén kell lennie.
    - Ön a fiók tulajdonosa, és a beléptetés adminisztrátora letiltotta a "AO költségek megtekintése" beállítást.  Hozzáférhet a költségeket a regisztráció rendszergazdájához forduljon. 
    - A részleg rendszergazdája, és a beléptetés adminisztrátora letiltotta a "DA költségek megtekintése" beállítást.  Lépjen kapcsolatba a regisztrációs rendszergazdától kérhet hozzáférést.
    - Azure csatorna-partneren keresztül vásárolta, és a partner nem kiadás díjszabási információkat.  
- Hozzáférés a vállalati portál költség kapcsolatos beállítások frissítéséhez van-e késleltetés néhány percig, amíg a módosítások megjelennek az Azure Portalon.
- A költségkeret, és a számla útmutatást EA-előfizetések nem vonatkoznak.

### <a name="check-your-subscription-and-access"></a>Az előfizetés és a hozzáférés ellenőrzése

Költségek megtekintéséhez rendelkeznie [számlázási adatokat előfizetés-szintű hozzáférést](billing-manage-access.md). Csak a Fiókadminisztrátor férhet hozzá a [Account Center](https://account.azure.com/Subscriptions), módosíthatja a számlázási adatokat, és -előfizetések kezelése. A Fiókadminisztrátor az a személy, aki a regisztrációs folyamat végrehajtása. További információkért lásd: [hozzáadása vagy módosítása az előfizetést vagy szolgáltatásokat kezelő Azure-rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md).

Ha Ön a fiókadminisztrátor megtekintéséhez lépjen a [előfizetések az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Tekintse meg a hozzáfér az előfizetések listáját. Keresse meg a **a szerepkör**. Ugyanakkor *fiókadminisztrátor*, akkor Ön ok. Ugyanakkor más hasonló *tulajdonosa*, akkor nem rendelkezik teljes jogosultsággal.

![A szerepkör az előfizetések nézetben az Azure Portal képernyőképe](./media/billing-getting-started/sub-blade-view.PNG)

Ha Ön nem a fiókadminisztrátor, akkor valaki valószínűleg kaptunk részleges hozzáférés használatával [Azure Active Directory szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) (RBAC). Előfizetések és a számlázási adatokat, módosítás kezeléséhez [keresse meg a Fiókadminisztrátor](billing-subscription-transfer.md#whoisaa). Kérje meg a feladatokat végezheti el a Fiókadminisztrátor vagy [az előfizetés átvitele](billing-subscription-transfer.md).

Ha a fiókadminisztrátor már nem a szervezeténél, és Önnek kell kezelnie a számlázási [lépjen kapcsolatba velünk](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>Hogyan kérhetnek tapasztalhatott a szolgáltatás egy-egy szolgáltatásiszint-szerződés kredit

A szolgáltatói szerződések (SLA-k) rögzítik a Microsoft elkötelezettségét a folyamatos rendelkezésre állás és stabil kapcsolatok iránt. Egy szolgáltatás incidens van küldött Azure-szolgáltatások egy problémát a hatások üzemidő vagy a kapcsolatot, más néven "kimaradás." Ha azt nem érhető el, és a szolgáltatási szintek kezelése minden egyes szolgáltatás, az SLA-ban leírtak szerint, majd jogosult lehet számára egy egy része a havi szolgáltatási díját felé.

Kreditek igényléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Ha több fiókot, ellenőrizze a valamelyik Azure üzemszünet által érintett volt. Ez segít a támogatási automatikusan összegyűjti a szükséges háttér-információkkal, és így gyorsabb megoldásához.
2. Hozzon létre egy új támogatási kérelmet.
3. A **Problématípus**válassza **számlázási**.
4. A **Problématípus**válassza **visszatérítés igénylése**.
5. Adja meg, hogy Ön kéri egy SLA-t a jóváírási, a dátum/idő/időzóna-miközben (virtuális gépek, webhelyek, stb.) az érintett szolgáltatások adatainak hozzáadása
6. Ellenőrizze a kapcsolattartási adatait, és válassza ki a **létrehozás** gombra kattintva küldje el a kérést.

SLA-küszöbértékek szolgáltatás eltérőek lehetnek. Például SQL webes réteg 99,9 %-os SLA-t, virtuális gépek rendelkeznek 99,95 %-os SLA-t, pedig SQL Standard szintű 99,99 %-os SLA-t.

Egyes szolgáltatások nincsenek előfeltételei a szolgáltatói szerződés érvénybe lépéséhez. Például a virtuális gépek két vagy több példánya azonos rendelkezésre állási csoportban kell rendelkeznie.

További információkért lásd: a [szolgáltatásiszint-szerződései](https://azure.microsoft.com/en-us/support/legal/sla/) dokumentáció és a [SLA összefoglalás az Azure-szolgáltatások](https://azure.microsoft.com/en-us/support/legal/sla/summary/) dokumentációját.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
