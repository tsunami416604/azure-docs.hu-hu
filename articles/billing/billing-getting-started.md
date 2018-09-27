---
title: Váratlan költségek megelőzése, a számlázás az Azure-ban kezelheti |} A Microsoft Docs
description: Útmutató az Azure-elszámolások a váratlan költségek elkerülése érdekében. Költségek nyomon követése és felügyeleti funkciók használata a Microsoft Azure-előfizetés.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: cwatson
ms.openlocfilehash: d17215fe05a4795bd539759d98695ec2f6b0df3d
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393499"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése

Feliratkozás az Azure-hoz, több dolgot is van az Ön által megvilágításához. A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) költségek becslést ad egy Azure-erőforrás létrehozása előtt. A [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) biztosítja az aktuális költségek részletezése és előrejelzés az előfizetéshez. Ha a csoportot, és költségeit a különböző projektek vagy fejlesztőcsapatok számára szeretne, keresse meg [erőforrás-címkézési](../azure-resource-manager/resource-group-using-tags.md). Ha a szervezete jelentési rendszer, amely szeretné használni, tekintse meg a [számlázási API-jai](billing-usage-rate-card-overview.md). 

- Ha az előfizetés egy nagyvállalati szerződés (EA), a nyilvános előzetes jelennek meg a költségek az Azure Portalon érhető el. Ha az előfizetés Cloud Solution Provider (CSP) vagy az Azure sponsorship ajánlattal keresztül, majd a következő funkciók némelyike nem alkalmazható Önnek. Lásd: [további erőforrások az EA, CSP és szponzorálási](#other-offers) további információ.

- Ha az előfizetés egy ingyenes próbaverzióra [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (aio-ra), vagy a BizSpark, az előfizetés automatikusan letiltja, ha az a kreditek felhasználása. Ismerje meg [költségkorlátozási](#spending-limit) unexpectantly le van tiltva az előfizetés ne kelljen.

- Ha a regisztrált [ingyenes Azure-fiók](https://azure.microsoft.com/free/), [néhány, a legnépszerűbb Azure-szolgáltatások 12 hónapig ingyenesen használható](billing-create-free-services-included-free-account.md). Lásd az alábbi javaslatokat, valamint [elkerülése az ingyenes fiók számolják](billing-avoid-charges-free-account.md).

> [!div class="nextstepaction"]
> [Segítsen az Azure számlázási dokumentumok fejlesztésében](https://go.microsoft.com/fwlink/p/?linkid=2010091) 

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

### <a name="set-up-billing-alerts"></a>Számlázási értesítések beállítása

Állítsa be a számlázási figyelmeztető e-mailek küldése az adathasználati költségek meghaladja az Ön által megadott. Ha rendelkezik a havi kreditek, beállítása használatakor be egy megadott mennyiséget. További információkért lásd: [elszámolási értesítések a Microsoft Azure-előfizetések beállítása](billing-set-up-alerts.md).

![A számlázási riasztási e-mail képernyőképe](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Ez a funkció egyelőre előzetes verzióként érhető így rendszeresen ellenőrizze a használatot.

Előfordulhat, hogy szeretné használni a díjkalkulátor, a Költségbecslés irányelvként az első riasztás.

### <a name="spending-limit"></a> Ellenőrizze, hogy a költségkeret maximumát

Ha egy előfizetés által használt kreditek, majd a költségkeret van kapcsolva, alapértelmezés szerint. Ezzel a módszerrel, amelyet használhat fel a kreditek, ha a megadott bankkártyára nem kell fizetnie. Tekintse meg a [teljes listája megtalálható az Azure és a költségkeret elérhetősége](https://azure.microsoft.com/support/legal/offer-details/).

Azonban ha eléri a költségkeretet, a szolgáltatások letiltásának. Ez azt jelenti, hogy a virtuális gépek felszabadított állapotban vannak. Szolgáltatás állásidő elkerülése érdekében a költségkeret ki kell kapcsolnia. Bármely kerettúllépést – a lekérdezi a hitelkártyára terheljük az alakzatot kell fizetnie. 

Ha Ön már rendelkezik költségkeretet a megtekintéséhez nyissa meg a [előfizetések megtekintéséhez az Account Center webhelyen](https://account.windowsazure.com/Subscriptions). Egy szalagcím jelenik meg, ha a költségkeretet:

![Képernyőkép a kiadásokat, korlátot a folyamatban, az Account Center webhelyen kapcsolatos figyelmeztetés](./media/billing-getting-started/spending-limit-banner.PNG)

Kattintson a szalagcímre, és útmutatást követve el kell távolítania a költségkeretet. Ha a hitelkártyaadatokat való regisztráció során adta meg, meg kell adnia, hogy el kell távolítania a költségkeretet. További információkért lásd: [az Azure-költségkeret működési elve és engedélyezése, vagy távolítsa el](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>A költségek monitorozása az Azure-szolgáltatások használatakor módjai

### <a name="tags"></a> Címkék hozzáadása az erőforrások az elszámolási adatok

A támogatott szolgáltatások címkék a számlázási adatokhoz csoport is használhatja. Például ha futtatja a különböző csapatok számára a több virtuális gépet, majd segítségével címkék költséghely (HR, marketing, pénzügyi) vagy a környezet (éles üzem előtti, test) költségek kategorizálása. 

![Képernyőkép a címkék beállítása a portálon](./media/billing-getting-started/tags.PNG)

A címkék jelennek meg egész nézetek reporting különböző költség. Például, hogy látható a [költségek elemzése nézet](#costs) azonnal és [használati .csv részletességi](#invoice-and-usage) az első számlázási időszak után.

További információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Rendszeresen ellenőrizze a portálon a költségek részletezése és írási sebesség

Miután beszerezte a futó szolgáltatások, rendszeresen ellenőrzi azok, amelyek IP-címek fenntartási ára van. Tekintse meg az aktuális költségek, és írási sebesség az Azure Portalon. 

1. Látogasson el a [Azure portal előfizetések paneljén](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , és válasszon ki egy előfizetést.

2. Tekintse meg a költségek lebontását kell, és sebesség írása a helyi menü panelen. Ez nem támogatott az ajánlatban (egy figyelmeztetés fog megjelenni tetején).

    ![Képernyőkép a írási sebesség és az Azure Portalon lebontása](./media/billing-getting-started/burn-rate.PNG)

3. Kattintson a **költségelemzés** a bal oldalon látható a költségek lebontását erőforrás szerint a listában. Várjon 24 órát, miután hozzáadta a szolgáltatás az adatok feltöltéséhez.

    ![A cost analysis nézet az Azure portal képernyőképe](./media/billing-getting-started/cost-analysis.PNG)

4. Különböző tulajdonságok, például szűrhet [címkék](#tags), erőforráscsoport és az időtartam. Kattintson a **alkalmaz** megerősítéséhez a szűrőket és **letöltése** Ha azt szeretné, exportálhatja a nézet egy Comma-Separated értékeket tartalmazó (.csv) fájlt.

5. Ezenkívül, kattintson egy erőforrás előzmények és az IP-címek fenntartási költségei az az erőforrás a minden nap napi megtekintéséhez.

    ![A költségek előzmények megtekintése az Azure portal képernyőképe](./media/billing-getting-started/costhistory.PNG)

Azt javasoljuk, hogy ellenőrizze a becsléseket látott a szolgáltatások kiválasztásakor megjelenik a költségeket. Ha a költségek becslése kampánya különböznek, gondosan ellenőrizze az erőforrások kiválasztott díjszabási csomag (a1-es vagy A0 VM, a példában). 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Fontolja meg a virtuális gépek költség általános szolgáltatások, mint az automatikus rendszerleállítás engedélyezése

A forgatókönyvtől függően automatikus leállítása sikerült konfigurálni a virtuális gépek az Azure Portalon. További információkért lásd: [az Azure Resource Manager-beli virtuális gépek automatikus leállítási](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Automatikus leállítási funkció használatát a portál képernyőképe](./media/billing-getting-started/auto-shutdown.PNG)

Automatikus leállítási nem ugyanaz, mint amikor leállítja a virtuális gépen az Energiagazdálkodási lehetőségek. Automatické vypnutí leállítja és felszabadítja a virtuális gépek leállítása további használati díjak. További információkért tekintse meg a díjszabással kapcsolatos gyakori kérdések a [Linux rendszerű virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) kapcsolatos virtuális gépek állapotáról.

További költség általános funkciókat a fejlesztési és tesztelési környezetek esetében, tekintse meg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Kapcsolja be, és tekintse meg az Azure Advisor-javaslatok

[Az Azure Advisor](../advisor/advisor-overview.md) olyan szolgáltatás, amely segít a költségek csökkentése az alacsony kihasználtságú erőforrásokat azonosításával. Az Advisor felkeresése az Azure Portalon:

![Képernyőkép az Azure Advisor gomb az Azure Portalon](./media/billing-getting-started/advisor-button.PNG)

Ezt követően kezdheti gyakorlati ajánlásokat készítünk a **költség** az Advisor irányítópult lapján:

![Képernyőkép az Advisor költség javaslat példa](./media/billing-getting-started/advisor-action.PNG)

További információkért lásd: [Advisor díjakkal kapcsolatos ajánlások](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Az Ön számlázási ciklusa végén költségek áttekintése

Után az elszámolási végén a számla válnak elérhetővé. Emellett [letöltheti a korábbi számlák és használati fájlokat részletességi](billing-download-azure-invoice-daily-usage-date.md) , győződjön meg arról, hogy helyesen lett díj felszámítva. A napi használat a számlán az összehasonlításával kapcsolatban további információkért lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md).

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

Ha a felügyeli informatikai egy nagy szervezet, javasoljuk, hogy olvassa [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) és a [vállalati informatikai tanulmány](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf letöltés, csak angol nyelven).

#### <a name="EA"></a> Előzetes verzió nagyvállalati szerződés Azure Portalon nézetek költség 

Vállalati költségek nézet jelenleg nyilvános előzetes verzióban érhető el. Figyelembe veendő elemek:
- Előfizetés költségeket a használat alapulnak, és veszi figyelembe az előre fizetett összegek, túlhasználattal, benne foglalt tranzakciómennyiségek, módosítását és adók. Tényleges költségek a regisztráció szintjén vannak kiszámítva. 
- Az Azure Portalon megjelenített értékek a vállalati portál képest késésével.  
- Ha nem jelennek meg költségeit, az alábbi okok egyike miatt lehet:
    - Nincs elegendő az RBAC-engedély az előfizetés szintjén. Vállalati költségek nézet jelenik meg, hogy egy számlázási olvasó, olvasó, közreműködő vagy tulajdonos az előfizetés szintjén kell lennie.
    - Fiók tulajdonosa, és a beléptetés adminisztrátora letiltotta a "AO költségek megtekintése" beállítást.  Hozzáférhet a költségeket a regisztráció rendszergazdájához forduljon. 
    - Részleg rendszergazdaként, és a beléptetés adminisztrátora letiltotta a "DA költségek megtekintése" beállítást.  Hozzáfér a regisztráció rendszergazdájához forduljon. 
    - Azure csatorna-partneren keresztül vásárolta meg, és a partner nem jelent meg a díjszabási információkat.  
- Költség hozzáféréssel kapcsolatos beállítások frissítése esetén a vállalati portálon, van néhány percet, mielőtt a módosítások megjelennek az Azure Portalon késés.
- A költségkeretet, számlázási figyelmeztetéseket, valamint a számla nem vonatkoznak EA-előfizetések.

### <a name="check-your-subscription-and-access"></a>Az előfizetés és a hozzáférés ellenőrzése

Megtekintésre költségeket igényelnek [számlázási adatokat előfizetés-szintű hozzáférést](billing-manage-access.md), csak a fiókadminisztrátor férhet hozzá, de a [Account Center](https://account.azure.com/Subscriptions), módosítsa a számlázási információkat és -előfizetések kezelése. A fiókadminisztrátor az a személy, aki a regisztrációs folyamat végrehajtása. További információkért lásd: [hozzáadása vagy módosítása az előfizetést vagy szolgáltatásokat kezelő Azure-rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md).

Ha Ön a fiókadminisztrátor megtekintéséhez nyissa meg a [az Azure Portal előfizetések paneljén](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) és a hely hozzáfér az előfizetések listáját. Keresse meg a **a szerepkör**. Ugyanakkor *fiókadminisztrátor*, akkor Ön ok. Ugyanakkor más hasonló *tulajdonosa*, akkor nem rendelkezik teljes jogosultsággal.

![A szerepkör az előfizetések nézetben az Azure Portal képernyőképe](./media/billing-getting-started/sub-blade-view.PNG)

Ha Ön nem a fiókadminisztrátor, akkor valaki valószínűleg kaptunk részleges elérését [Azure Active Directory szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) (RBAC). Előfizetések és a számlázási adatokat, módosítás kezeléséhez [keresse meg a fiókadminisztrátor](billing-subscription-transfer.md#whoisaa) meg, hogy a feladatok elvégzéséhez vagy [vigye át az előfizetést,](billing-subscription-transfer.md).

Ha a fiókadminisztrátor már nem a szervezeténél, és Önnek kell kezelnie a számlázási [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
