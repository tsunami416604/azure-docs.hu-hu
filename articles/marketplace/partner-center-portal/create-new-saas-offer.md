---
title: Új SaaS-ajánlat létrehozása a kereskedelmi piactéren
description: Az Azure Marketplace-en, a AppSource-on vagy a Cloud Solution Provider (CSP) programon keresztüli, a Microsoft partner Center webhelyen található kereskedelmi Piactéri portál használatával új, szolgáltatásként nyújtott szoftveres (SaaS-) ajánlat létrehozásához.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 578904d40b1354dde99644cb2fc73e0a56223b34
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376290"
---
# <a name="create-a-new-saas-offer"></a>Új SaaS-ajánlat létrehozása

A szolgáltatott szoftver (SaaS)-ajánlatok létrehozásának megkezdéséhez először létre kell [hoznia egy partner Center-fiókot](./create-account.md) , és meg kell nyitnia a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), ahol az **Áttekintés** lapon be van jelölve.

![Kereskedelmi piactér irányítópultja a partner Centerben](./media/new-offer-overview.png)

>[!Note]
> Ha egy ajánlat közzé lett téve, a partner Centerben végzett ajánlat szerkesztése csak a rendszeren történik meg, és az újbóli közzététel után tárolja az előfizetéseket. Győződjön meg arról, hogy a módosítások elvégzése után elküldi az ajánlatot közzétételre.

Válassza ki az + **új ajánlatot...** gombra, majd válassza ki a **szoftvert szolgáltatásként** menüpontot. 

Ha más ajánlat típusát választja, akkor a rendszer átirányítja a régebbi [Cloud Partner Portalra](https://cloudpartner.azure.com/). Jelenleg csak a SaaS-és Dynamics 365-ajánlatok érhetők el a partner Center kereskedelmi piactér portálján.

![Ajánlati ablak létrehozása a partner Centerben](./media/new-offer-click.png)

Megjelenik az **új ajánlat** párbeszédpanel. 

![Új ajánlat párbeszédpanel](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>Ajánlat azonosítója és alias

- **Ajánlat azonosítója**: egyedi azonosító a fiókban található összes ajánlathoz. Ez az azonosító látható lesz az ügyfelek számára a Piactéri ajánlat URL-címében, és Azure Resource Manager sablonokat (ha vannak ilyenek). Az ajánlat-azonosító csak kisbetűket, alfanumerikus karaktereket (például kötőjeleket és aláhúzásokat) tartalmazhat. Ez 50 karakterre van korlátozva, és a *Létrehozás*gombra kattintva nem módosítható.  
Példa: test-Offer-1
<br>Eredményül kapott URL-cím: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ajánlat aliasa**: az ajánlatra való hivatkozáshoz használt név a partner Center portálon. Ezt a nevet nem fogja használni a piactéren, és nem egyezik meg az *ajánlat nevével* és az ügyfelek számára megjelenített egyéb értékekkel. Ez az érték nem módosítható a *Létrehozás*gombra kattintva.

<br>Példa: 1. tesztelési ajánlat&#8482;

Kattintson a **Létrehozás** gombra.  Ehhez az ajánlathoz létrejön egy **ajánlat áttekintő** lapja.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **ajánlat áttekintése** oldalon a következők szerepelnek: 

- A **közzétételi állapot** megjeleníti az ajánlat közzétételéhez szükséges lépések vizuális megjelenítését, valamint azt, hogy az egyes lépések mennyi ideig tartanak. Hiányos közzétételi lépések ikonjai szürkén jelennek meg. 

- Az **ajánlat áttekintő** menüjében az ajánlaton végrehajtott műveletekre mutató hivatkozásokat tartalmazó lista szerepel. A műveletek listája az ajánlathoz választott kiválasztási alapján változik.  
    - Ha az ajánlat Piszkozat – delete Piszkozat 
    - Ha az ajánlat élőben van – eladási ajánlat leállítása 
    - Ha az ajánlat előzetes verzióban érhető el – Go-Live 
    - Ha még nem fejezte be a közzétevő kijelentkezését – közzététel megszakítása

## <a name="offer-setup"></a>Ajánlat beállítása

Az **ajánlat beállítása** lap az alábbi információkat kéri. A mezők befejezése után válassza a **Mentés** lehetőséget.

- **Szeretné eladni a Microsoftot?** (Igen/nem)
    - **Igen**, szeretné értékesíteni ajánlatát a Microsoftnál, és a Microsoft üzemeltetési piactér-tranzakciókat az Ön nevében. vagy 
    - **Nem**, inkább a piactéren szeretné kilistázni az ajánlatot, és a Microsofttól függetlenül bármilyen pénzügyi tranzakciót feldolgozni.    

### <a name="sell-through-microsoft"></a>Értékesítés a Microsofton keresztül

A Microsofton keresztüli értékesítés jobb ügyfélszolgálatot és beszerzést biztosít, lehetővé teszi a Microsoft számára, hogy az Ön nevében üzemelteti a Piactéri tranzakciókat, és kihasználja a Microsoft globálisan elérhető kereskedelmi képességei

#### <a name="saas-offer-requirements"></a>SaaS-ajánlatra vonatkozó követelmények

Ha a Service (SaaS) szolgáltatást a partner Center kereskedelmi piactérről szeretné listázni, a következő feltételeknek kell teljesülniük:

- Az ajánlatnak [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) használatát kell használnia az Identitáskezelés és a hitelesítéshez.
- Az ajánlatnak [SaaS-megvalósítási API-kat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) kell használnia az Azure Marketplace-szel való integrációhoz.
- Részletesebb követelmények: [SaaS-ajánlat közzétételi útmutatója](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>SaaS-díjszabás és számlázási lehetőségek
A kiadó Azure-előfizetésében futó SaaS-megoldásokkal az ügyfelek által kifizetett licencek a szoftver üzembe helyezéséhez használt infrastruktúra díját is tartalmazzák. Az Azure-infrastruktúra használatának felügyelete és számlázása Önnek, a partnernek közvetlenül történik. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadóknak az Azure-infrastruktúra használati díját kell megfizetniük a szoftverlicenc-díjszabásban. 

Az SaaS a mért számlázási szolgáltatással havi vagy éves számlázási támogatást biztosít, amely átalány, felhasználónkénti vagy használati díj alapján történik. A Microsoft kereskedelmi piactére egy ügynökségi modellen működik, amelynek során a kiadók díjszabást, Microsoft-számlákat és Microsoft-számlázást fizetnek a kiadónak, miközben az ügynökség díját is megtartják.

Az alábbi táblázat egy példát mutat be a költségek és a kifizetések lebontására az Ügynökség modelljének bemutatására.

|**A licenc díja**|**$100/hó**|
|:---|:---|
|Azure-használati díj (D1/1-Core)|A számlázás közvetlenül a közzétevőre történik, nem az ügyfél|
|Az ügyfelet a Microsoft számlázza|$100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján)|

|**Microsoft-számlák**|**$100/hó**|
|:---|:---|
|A Microsoft a licencelési díj 80%-ában fizet <br>@no__t – 0*a minősített SaaS-alkalmazások esetében a Microsoft a licencelési díj 90%-ában fizet*|$80,00/hó <br>*@no__t – 1*90,00/hó *|

- Ebben a példában a Microsoft $100,00-es számlát küld az ügyfélnek a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.
- Azok a partnerek, akik a **kedvezményes Piactéri szolgáltatási díjjal** rendelkeznek, csökkentett tranzakciós díjat kapnak az SaaS-ajánlatokról, a május 2019-ig, 2020-ig. Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $90,00-et a közzétevőnek.

> [!NOTE]
> **Csökkentett Piactéri szolgáltatási díj**: a kereskedelmi piactéren közzétett egyes SaaS-ajánlatok esetében a Microsoft a piactér szolgáltatási díját 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra. Ahhoz, hogy az ajánlat megfelelő legyen, legalább az egyik ajánlatát a Microsoftnak kell kijelölnie, mint az IP-címek közös értékesítésének vagy az IP-címek közös értékesítésének prioritása.  A jogosultságot az egyes naptári hónapok végét megelőzően legalább öt (5) munkanapon belül teljesíteni kell, hogy megkaphassa a Marketplace szolgáltatási díját a hónapra.  A Piactéri szolgáltatási díj nem vonatkozik a kereskedelmi Piactéren keresztül elérhető virtuális gépekre, felügyelt alkalmazásokra vagy más termékekre.  A csökkentett Piactéri szolgáltatási díj csak a Microsoft által a 2019 és a 2020. június 30. között gyűjtött licencek díjaihoz érhető el.  Ezt követően a Piactéri szolgáltatási díj visszaáll a normál mennyiségre. 




#### <a name="csp-program-opt-in"></a>CSP-program – opt-in
A [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program lehetővé teszi, hogy a szoftverek több millió minősített Microsoft-ügyfelet érjenek el minimális marketing-és értékesítési befektetéssel.

- **Csatornák: az ajánlatom elérhetővé tétele a CSP programban** (jelölőnégyzet)

Ha azt szeretné, hogy az ajánlata elérhető legyen a CSP programban, a Cloud Solution Providers lehetővé teszi, hogy a terméket egy csomagban lévő megoldás részeként értékesítse az ügyfeleiknek. 

### <a name="list-through-microsoft"></a>Listázás a Microsofton keresztül

A piactér-lista létrehozásával népszerűsítheti vállalkozását a Microsofttal. Ha csak az ajánlat listázását választja, és nem a Microsofttól, hanem azt is jelenti, hogy a Microsoft nem vesz részt közvetlenül a szoftverlicenc-tranzakciókban. Nincs hozzárendelt tranzakciós díj, és a közzétevő megtartja az ügyféltől begyűjtött licencelési díjak 100%-át. A közzétevő azonban felelős a szoftverlicenc-tranzakció valamennyi aspektusának támogatásában, beleértve a rendelés teljesítését, a mérést, a számlázást, a számlázást, a fizetést és a gyűjtést. 

- **Hogyan kívánja használni a potenciális ügyfeleket a jelen tőzsdei ajánlathoz?**

##### <a name="get-it-now-free"></a>Letöltés (ingyenes)
Az ajánlatát ingyenesen listázhatja, ha érvényes URL-címet ad meg (a *http* vagy a *https*-től kezdve), ahol elérheti az alkalmazást.  Például:`https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)
Az ingyenes próbaverzióra mutató hivatkozást tartalmazó ajánlat listázása egy érvényes URL-cím megadásával (a *http* vagy a *https*verziótól kezdődően), ahol az [Azure Active Directory (Azure ad) használatával egyetlen kattintással](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)elvégezheti a próbaidőszakos hitelesítést.  Például: `https://contoso.com/trial/saas-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak a felhasználói adatok Azure AD-n keresztüli beszerzéséhez használhatók, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok (MSA-EK) nem támogatottak a jogkivonat használatával történő hitelesítéshez.

##### <a name="contact-me"></a>Megkeresést kérek
Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: az [érdeklődők felügyeletének összekötése](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Példa a piactér ajánlatának listázására

![Példa a piactér ajánlatának listázására megjegyzésekkel](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Tesztelési meghajtó engedélyezése

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt megkeresi a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Tesztelési meghajtó engedélyezése** (jelölőnégyzet) 

A test Drive engedélyezésével a rendszer arra kéri, hogy állítson be egy demonstrációs környezetet az ügyfeleknek az ajánlat meghatározott időn belül történő kipróbálásához. 

### <a name="type-of-test-drive"></a>A tesztelési meghajtó típusa

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : a Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve az üzembe helyezést és a telepítést is) egy üzleti központi vállalati erőforrás-tervezési rendszerhez (pénzügy, műveletek, ellátási lánc, CRM stb.).  
- **[Dynamics 365 az ügyfelek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** bevonásához: a Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve az üzembe helyezést és a telepítést is) az ügyfél-engagement rendszer (értékesítés, szolgáltatás, projekt szolgáltatás, mező szolgáltatás stb.) számára.  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : a Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve a kiépítést és az üzembe helyezést) pénzügyi és üzemeltetési vállalati erőforrás-tervezési rendszerekhez (pénzügy, műveletek, gyártás, ellátási lánc stb.). 
- **[Logikai alkalmazás](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : egy központi telepítési sablon, amely az összes összetett megoldás-architektúrát magában foglalja. Minden egyéni terméknek ilyen típusú tesztelési meghajtót kell használnia.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : beágyazott hivatkozás egy egyéni kialakítású irányítópultra. Azok a termékek, amelyek interaktív Power BI vizualizációt kívánnak bizonyítani, ezt a típusú tesztelési meghajtót kell használniuk. Itt a beágyazott Power BI URL-címére kell feltöltenie.

#### <a name="additional-test-drive-resources"></a>További tesztelési meghajtó erőforrásai
- [A test Drive technikai ajánlott eljárásai](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A test Drive marketing ajánlott eljárásai](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [A test Drive áttekintése egy pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>További érdeklődői felügyeleti erőforrások
- [Az érdeklődők felügyeletével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Gyakori vezető konfigurációs hibák](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Az érdeklődők felügyelete – Áttekintés egy pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

## <a name="properties"></a>Tulajdonságok
A **Properties (Tulajdonságok** ) lapon megtekintheti az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az ajánlatot támogató jogi szerződéseket és az alkalmazás verzióját. 

A mezők befejezése után válassza a **Mentés** lehetőséget. 

### <a name="category"></a>Kategória
Válasszon legalább egyet (1), és legfeljebb három (3) kategóriát, amelyek az ajánlatnak a piactér megfelelő keresési területein való csoportosításához használhatók. Kérjük, tekintse meg, hogyan támogatja az ajánlat a kategóriákat az ajánlat leírásában. 

### <a name="industry"></a>Iparág
Válasszon akár két (2) iparágat is, amelyek az ajánlatnak a piactér megfelelő keresési területein való csoportosítására szolgálnak. Ha az ajánlat nem az iparágra jellemző, ne válasszon egyet. Kérjük, hívja meg az ajánlat leírásában, hogy az ajánlat hogyan támogatja a kiválasztott iparágakat. 

### <a name="app-version"></a>Az alkalmazás verziója
Ez egy nem kötelezően kitöltendő mező, amely az AppSource piactéren az ajánlat verziószámának azonosítására szolgál. 

### <a name="standard-contract"></a>Standard szerződés

- **Standard szintű szerződést használ?**

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft egy standard szintű szerződést biztosít, amely megkönnyíti a tranzakciót a piactéren. 

Az egyéni használati feltételek és kikötések elvégzése helyett az Azure Marketplace-közzétevők dönthetnek úgy, hogy a standard szintű szerződés keretében kínálják a szoftvereket, amelyeket csak egyszer kell bemutatni és elfogadni. 

A standard szintű szerződés itt található: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Használati feltételek

Ha a licencfeltételek eltérnek a standard szerződéstől, dönthet úgy, hogy megadja a saját jogi használati feltételeit itt. Ebben a mezőben legfeljebb 10 000 karakter hosszúságú szöveget adhat meg. Ha a használati feltételek több leírást igényelnek, adjon meg egy URL-hivatkozást ebbe a mezőbe, ahol a további licencfeltételek is megtalálhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket az alkalmazás kipróbálása előtt. 

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

## <a name="offer-listing"></a>Ajánlati lista

Az ajánlati lista lapon láthatók azok a nyelvek (és piacok), ahol az ajánlat elérhető, jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, keresési kifejezéseit stb.) az egyes nyelvekhez/piacokhoz.

> [!NOTE]
> A tartalom listázása (például az ajánlat leírása, a dokumentumok, a képernyőképek, a használati feltételek és az adatvédelmi szabályzatok) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

### <a name="offer-listings"></a>Ajánlatok listázása

Adja meg a piactéren megjelenítendő adatokat, beleértve az ajánlat és a marketing-eszközök leírását.

- **Név** (kötelező): az itt definiált név jelenik meg az ajánlatnak az Ön által választott piactéren (ko) lévő listáján. A név előre fel van töltve az előző **új ajánlat** bejegyzése alapján.  Lehet, hogy ez védjeggyel van elvégezve.  Ez nem tartalmazhat hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie.
- **Összefoglalás** (kötelező): adja meg az ajánlat rövid leírását, amelyet a Piactéri listák keresési eredményeiben kíván használni. Ebben a mezőben legfeljebb 100 karaktert lehet megadni.
- **Leírás** (kötelező): adja meg a piactér-lista (ek) áttekintésében megjelenítendő ajánlat leírását. Érdemes figyelembe venni az érték kiosztását, a főbb előnyöket, a kategória-vagy iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket, a szükséges közzétételeket, valamint egy hivatkozást, amely további információkat tartalmaz.
Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. További tippekért tekintse meg a [nagyszerű alkalmazás leírását](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)ismertető cikket.
- **Kulcsszavak keresése**: legfeljebb három olyan keresési kulcsszót adhat meg, amelyeket az ügyfelek a piactér (ek) ben használhatnak fel az ajánlat megtalálására.
- **Útmutató az első lépésekhez** (kötelező): az alkalmazás konfigurálásának és használatának megkezdése a potenciális ügyfelek számára.  Ez a rövid útmutató a részletesebb online dokumentációra mutató hivatkozásokat is tartalmaz. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

#### <a name="description"></a>**Leírás**

Ez egy kötelező mező. A leírásban szerepeltetni kívánt elemek: 

* A Leírás első néhány mondatában egyértelműen ismertesse ajánlata értékét.  
* Ne feledje, hogy az első néhány mondat a keresőmotor eredményei között jelenhet meg.  
* Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez. Ehelyett a megadott értékre kell összpontosítania.  
* A lehető legnagyobb mértékben használja az iparági specifikus szókincset vagy a juttatás-alapú szövegezést. 

Az érték kiosztásának alapvető összetevőinek a következő információkat kell tartalmazniuk: 

* A termék leírása. 
* A termékből származó előnyöket biztosító felhasználó típusa. 
* Az ügyfélnek szüksége van a termék címére, vagy a fájdalomra. 

Annak érdekében, hogy az ajánlat leírása jobban megtörténjen, HTML-címkék használatával formázhatja a leírást. 

1. Ha bekezdéseket szeretne létrehozni, adja hozzá a `<p>` parancsot a szöveg megadásához, és adja hozzá a `</p>` értéket a végén.

    **Példa**: 

    @no__t – 0 ez az első bekezdésem. `</p>` <br>
    @no__t – 0 Ez a második bekezdés. `</p>` <br>

    A fentiek így néznek ki:

    <p> Ez az első bekezdés. </p>
    <p> Ez a második bekezdés. </p>

1. Ha **listajeles listát kíván hozzáadni az elemek listájához**, helyezze a szöveget az alábbi `<li>` címkén belül. A `<ul>` és a `</ul>` címkén belül több listajeles elemet (a `<li>` és a `</li>` címke közötti elemeket) másolhat és illeszthet be. Ügyeljen arra, hogy hozzáadja a `<ul></ul>` értéket. 

    **Példa**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    A fentiek így néznek ki:
    <ul> 
        <li>Ide írja be a szöveget</li> 
        <li> Ide írja be a szöveget </li> 
        <li> Ide írja be a szöveget </li> 
    </ul> 

1. **Félkövér** tartalom hozzáadásához vegye fel `<b>` betűt a félkövérre állítani kívánt szöveg elejére, és vegye fel `</b>` szöveget a félkövérre szedett szöveg végén. 

    **Példa**: `<b>` ingyenes próbaverzió `</b>`
    
    A fentiek hatására az ingyenes PRÓBAIDŐSZAKot a kirakatban található ajánlat leírásában félkövérre kell állítani. 

    **INGYENES PRÓBAVERZIÓ**

1. Ha sortörést szeretne hozzáadni a tartalomhoz, adja hozzá a `<br>` **parancsot** az új sorban elindítani kívánt tartalomhoz. Ha helyet szeretne hagyni, és gondoskodjon arról, hogy a tartalom új sorban induljon el, adja hozzá a `<br><br>` értéket a tartalom előtt. 

    **Példa**:

    Ez egy szöveges sor. @no__t – 0 ez egy olyan szövegsor, amely új sorban indul el. @no__t – 0 – ez egy sor, amely az alábbi két sort fogja elindítani. 

    A fentiek így néznek ki:

    Ez egy szöveges sor. <br> Ez egy olyan szövegsor, amely új sorban indul el. <br><br> Ez egy olyan sor, amely az alábbi két sort fogja elindítani. 

1. Ha szeretné **megnövelni a szöveg méretét**, először válassza ki, milyen nagy legyen a szöveg. Használja az alábbi példákat. Miután kiválasztotta a szöveg méretét, adja hozzá a megfelelő @no__t – 0 címkét a szöveg elejéhez és végéhez. 

    **Példa**:

    `<h1>`This az 1 @ no__t-1 fejléc <br>
    @no__t – a 0This a 2. no__t-1. <br>
    @no__t – a 0This a 3. no__t-1. <br>
    @no__t – a 0This a 4. @ no__t-1. <br>
    @no__t – a 0This 5. címsor @ no__t-1. <br>
    @no__t – a 0This a 6 @ no__t-1. 

    A fentiek így néznek ki:

    ![Minta fejlécek](./media/heading.png)

#### <a name="links"></a>Hivatkozások

- **Adatvédelmi szabályzat** (kötelező): a szervezet adatvédelmi szabályzatára mutató hivatkozás. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson
- **CSP program marketing anyagok** (nem kötelező): meg kell adnia egy hivatkozást a marketing-anyagokra, ha úgy dönt, hogy kiterjeszti az ajánlatot a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programra. A CSP kiterjesztheti ajánlatát a minősített ügyfelek szélesebb körére azáltal, hogy lehetővé teszi a CSP-partnerek számára, hogy csomagot, piacot és viszonteladást nyújtsanak. Ezeknek a viszonteladóknak hozzá kell férniük az anyagokhoz az ajánlat marketingje érdekében. További információ: piacra jutási [szolgáltatások](https://partner.microsoft.com/reach-customers/gtm).
- **Hasznos hivatkozások** (nem kötelező): opcionális kiegészítő online dokumentumok az alkalmazással vagy a felsorolt kapcsolódó szolgáltatásokkal kapcsolatban a cím és az **URL-** **cím** megadásával. További hasznos hivatkozások hozzáadásához kattintson az **+ URL-cím hozzáadása**lehetőségre.

#### <a name="contact-information"></a>Kapcsolattartási adatok

- **Névjegyek**: minden ügyfél-kapcsolattartó esetében adja meg az alkalmazott **nevét** , **telefonszámát**és **e-mail-** címét.  (Ezek *nem* lesznek nyilvánosan megjelenítve). A **támogatási kapcsolattartó** csoportnak egy **támogatási URL-címet** is meg kell adnia.  (Ez az *információ nyilvánosan jelenik meg* ).

**Támogatási kapcsolattartó** (kötelező): általános támogatási kérdések.

**Engineering Contact** (kötelező): technikai kérdésekben.

**Channel Manager-kapcsolat** (kötelező): a CSP-programhoz kapcsolódó viszonteladói kérdések.

#### <a name="files-and-images"></a>Fájlok és lemezképek

- **Dokumentumok** (kötelező): kapcsolódó marketing-dokumentumok hozzáadása az ajánlathoz PDF formátumban, legalább egy (1) és legfeljebb három (3) dokumentum biztosításával.
- **Képek** (nem kötelező): több hely is van, ahol az ajánlat emblémájának képei megjelenhetnek a piactéren, a következő méretek megkövetelésével: 48 x 48 képpont _(kötelező),_ közepes: 90 x 90 képpont, nagyméretű: 216 x 216 képpont _( kötelező),_ széles: 255 x 115 képpont és hős: 815 x 290 képpont. Az összes rendszerképnek a-ben kell lennie. PNG-formátum.
- **Képernyőképek** (kötelező): az ajánlatot bemutató képernyőképeket adhat hozzá. Legfeljebb öt (5) képernyőkép adható hozzá, és 1280 x 720 képpont méretűnek kell lennie. Az összes rendszerképnek a-ben kell lennie. PNG-formátum.
- **Videók** (nem kötelező): az ajánlatot bemutató videókra mutató hivatkozásokat adhat hozzá. A YouTube-és/vagy a Vimeo-videókra mutató hivatkozásokat is használhat, amelyek az Ön ajánlatával együtt jelennek meg az ügyfelek számára. Emellett meg kell adnia a videó miniatűr képét is, amely az 1280 x 720 képpont méretű, PNG formátumú. Ajánlat legfeljebb négy videót jeleníthet meg.

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Előzetes verzió

Az **előnézet** lapon megadhat egy korlátozott **előzetes** verziót, amely az ajánlat közzétételét megelőzően közzéteszi az ajánlatot a piactér szélesebb közönsége számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után a **Go Live** -t kell választania, mielőtt az ajánlatát élőben közzé fogja tenni a piactér nyilvános célközönsége számára.

- **Előzetes verzió célközönségének definiálása: egyetlen HRE/MSA-fiók e-mail-címe soronként, valamint egy opcionális leírás.**

Akár tíz (10) e-mail-címet is hozzáadhat manuálisan, vagy húsz (20), ha egy CSV-fájlt tölt fel, a meglévő Microsoft-fiók (MSA) vagy a Azure Active Directory-fiókok esetében pedig az azonnali közzététel előtt segítséget nyújt az ajánlat érvényesítéséhez. Ezeknek a fiókoknak a hozzáadásával olyan célközönséget határozhat meg, amely előzetesen elérhetővé válik az ajánlat számára, mielőtt közzéteszi őket a piactéren. Ha az ajánlat már élő, akkor is megadhatja az előnézeti közönséget, hogy tesztelje az ajánlat módosításait és frissítéseit.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége a piactéren való élő közzététel _előtt_ jogosult az ajánlathoz való hozzáférésre. Dönthet úgy is, hogy létrehoz egy csomagot, és csak a privát célközönség számára teszi elérhetővé. A **csomag listázása** lapon megadhat egy privát célközönséget, amely az **Ez egy privát csomag** jelölőnégyzet. Az Azure-bérlői azonosítók használatával legfeljebb 20 000 ügyfelet adhat meg.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **technikai konfiguráció** lap az ajánlathoz való kapcsolódáshoz használt technikai részleteket (URL-cím, webhook, bérlői azonosító és alkalmazás-azonosító) határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék. Az összegyűjtött mezők használatát leíró diagramok a [SaaS-teljesítési API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)-k dokumentációjában találhatók.

- Kezdőlap **URL-címe** (kötelező): adja meg a webhely URL-címét, amelyet az ügyfelek az ajánlatnak a piactéren való beszerzése után fognak leszállni. Ez az URL-cím lesz a végpont, amely megkapja a jogkivonatot, amikor az ügyfél az oldalra irányítja. Ez a jogkivonat a teljesítési API-k feloldási funkciójával kicserélhető a kiépítési adatokra. Ezek az adatok és a gyűjtött egyéb információk a regisztráció befejezéséhez és a vásárlás aktiválásához használható, a felhasználó által interaktív weblap részeként.

- **Kapcsolati webhook** (kötelező): a Microsoft által az ügyfél nevében küldendő összes aszinkron eseményhez (példa: az SaaS-előfizetés érvénytelennek bizonyult) a kapcsolat webhookot kell megadnia. Ha még nem rendelkezik webhook-rendszerrel, a legegyszerűbb konfiguráció egy olyan HTTP-végpont logikai alkalmazás, amely figyeli a neki küldött összes eseményt, majd megfelelően kezeli őket (például https: \//Prod-1westus. Logic. Azure. com: 443/Work ). További információk: [munkafolyamatok hívása, elindítása vagy beágyazása http-végpontokkal a Logic Appsben](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure ad-bérlő azonosítója** (kötelező): Azure Portalon belül [létre kell hoznia egy Azure Active Directory (ad) alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , hogy a két szolgáltatás közötti kapcsolat ellenőrizhető legyen egy hitelesített kommunikáció mögött. A [bérlő azonosítójának](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)megkereséséhez lépjen a Azure Active Directoryra, és válassza a **Tulajdonságok**lehetőséget, majd keresse meg a megjelenő **címtár-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás azonosítója** (kötelező): az alkalmazás- [azonosítóra](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) és egy hitelesítési kulcsra is szüksége lesz. Az értékek beszerzéséhez lépjen a Azure Active Directory, és válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e). A hitelesítési kulcs megkereséséhez lépjen a **Beállítások** elemre, és válassza a **kulcsok**lehetőséget. Meg kell adnia egy leírást és egy időtartamot, és ezután meg kell adni egy számértéket.

 Vegye figyelembe, hogy az Azure-alkalmazás azonosítója a közzétevő AZONOSÍTÓhoz van társítva, ezért ügyeljen arra, hogy ugyanazt az azonosítót használja az összes ajánlatában.

## <a name="plan-overview"></a>A terv áttekintése

A **terv áttekintő** lapja lehetővé teszi, hogy egy adott ajánlaton belül különböző tervezési lehetőségeket biztosítson. Ezek a csomagok (más néven SKU-ként) eltérőek lehetnek a verzió, a monetizálása vagy a szolgáltatási szintek tekintetében. Be kell állítania legalább egy csomagot, hogy eladja ajánlatát a piactéren.

A létrehozást követően megtekintheti a csomag neveit, azonosítóit, díjszabási modelljeit, rendelkezésre állását (nyilvános vagy privát), az aktuális közzétételi állapotot és az összes elérhető műveletet.

A **terv áttekintésében** elérhető **műveletek** a csomag aktuális állapotától függően változnak, és a következők lehetnek:

- Ha a terv állapota **draft** – Piszkozat törlése
- Ha a csomag állapota **élő** – eladási csomag leállítása vagy privát célközönség szinkronizálása

**Új csomag létrehozása** (legalább egy csomag a Microsofton keresztüli értékesítésre kiválasztott felhasználók számára)

- **Csomag azonosítója:** Hozzon létre egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjának. Ez az azonosító látható lesz az ügyfelek számára a termék URL-címében és Azure Resource Manager-sablonokban (ha vannak ilyenek). Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon. Ehhez a csomag-AZONOSÍTÓhoz legfeljebb 50 karakter adható meg. Vegye figyelembe, hogy az azonosító nem módosítható a létrehozás kiválasztása után.
- **Csomag neve:** Az ügyfelek ezt a nevet fogják látni, amikor azt döntik el, hogy melyik tervet kell kiválasztani az ajánlaton belül. Hozzon létre egyedi nevet az ajánlat minden csomagjának. A terv neve az adott ajánlat részét képező szoftvercsomagok megkülönböztetésére szolgál (például Ajánlat neve: Windows Server; csomagok: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Csomag listázása

A csomag **listázása** lapon azok a nyelvek (és piacok) láthatók, ahol a csomag elérhető, a jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, keresési kifejezéseit stb.) az egyes nyelvekhez/piacokhoz.

#### <a name="plan-listing-details"></a>Lista részleteinek megtervezése

A terv nyelveinek kiválasztásakor megjelennek a **csomagra** vonatkozó információk, beleértve a **nevet** és a **leírást.**

- **Név**: előre kitöltve az előzetes verziójú **új díjcsomag** alapján, és a piactéren megjelenő "szoftvercsomag" címmel fog megjelenni.
- **Leírás:** Ez a leírás egy olyan lehetőség, amely ismerteti, hogy mi teszi ezt a csomagot egyedivé, és hogy az Ön által kínált egyéb szoftverek milyen eltéréseket biztosítanak. Legfeljebb 500 karaktert tartalmazhat.

A mezők befejezése után válassza a **Mentés** lehetőséget.

#### <a name="plan-pricing-and-availability"></a>A díjszabás és a rendelkezésre állás megtervezése

A **díjszabás és rendelkezésre állás** lapon konfigurálhatja azokat a piacokat, amelyekre ez a csomag elérhető lesz, a kívánt bevételi modell, ár és számlázási időszak alapján. Emellett azt is megadhatja, hogy a terv mindenki számára elérhető legyen-e, vagy csak bizonyos ügyfeleknek (privát célközönségnek).

##### <a name="enabling-free-trials"></a>Ingyenes próbaverziók engedélyezése

A kereskedelmi Piactéren keresztül elérhető SaaS-ajánlatok lehetővé teszik, hogy egy hónapos ingyenes próbaidőszakot biztosítson a Microsofton keresztüli értékesítés során. Az összes számlázási modellhez és használati feltételhez a mért csomagok kivételével az ingyenes próbaverziók támogatottak. Ez a beállítás lehetővé teszi, hogy az ügyfelek egy hónapon keresztül alacsony korláttal rendelkezzenek a belépéshez.  Ha úgy dönt, hogy engedélyezi az ajánlaton belüli csomagok ingyenes próbaverzióját, az ügyfél nem fog tudni áttérni fizetős előfizetésre a kezdeti egy hónapos időszak lejárta előtt.  Ebben az időszakban az ajánlatát vásárló ügyfelek kipróbálhatja a támogatott csomagokat, amelyeken engedélyezve van az ingyenes próbaverzió, és átválthat közöttük.  A díjköteles előfizetésre való áttérés a kifejezés végén automatikusan történik.

>[!Note]
>Ha az ügyfél úgy dönt, hogy ingyenes próbaverziók nélkül szeretne átalakítani egy csomagra, az átalakítás megtörténik, de az ingyenes próbaverzió azonnal elvész.  Azt is megteheti, hogy ha egy ügyfél megkezdi a csomag megfizetését, már nem kaphat ingyenes próbaidőszakot ugyanarra az előfizetésre, még akkor is, ha az ingyenes próbaverziókat támogató SKU-ra vált.

Az ingyenes próbaverzió konfigurálásának lehetősége az ajánlat minden csomagjában elérhető. Egyszerűen keresse meg az egyes ajánlatok díjszabását és rendelkezésre állását, és jelölje be a jelölőnégyzetet egy hónapos próbaverzió engedélyezéséhez.

![Egy hónapos ingyenes próbaverzió jelölőnégyzet](./media/free-trial-enable.png)

>[!Note]
>Ha a visszagörgethető ajánlat ingyenes próbaverzióval lett közzétéve, az adott csomag esetében nem tiltható le. Győződjön meg arról, hogy ez a beállítás megfelelő az első közzétételhez, hogy ne kelljen újból létrehoznia a csomagot.

Az ingyenes próbaverzióban jelenleg részt vevő ügyfél-előfizetésekkel kapcsolatos információk beszerzéséhez használja az új API-tulajdonságot @no__t – 0, amely igaz vagy hamis értékként lesz megjelölve. További információt a [SaaS Get előfizetés API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) -val foglalkozó témakörben talál.

>[!Note]
>Az ingyenes próbaverziók nem támogatottak a piactér-mérési szolgáltatást használó csomagok esetében.

#### <a name="markets"></a>Piacok

- **Piacok szerkesztése** (nem kötelező)

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be annak a piaci helynek a jelölőnégyzetét, amely számára elérhetővé szeretné tenni ezt a csomagot. Egy keresőmező és egy gomb a "Tax átutalt" országok kiválasztásához, amelyben a Microsoft az Ön nevében értékesítési és használati adót alkalmaz, és a segítségére is felhasználható. 


Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A díjszabást a módosítások mentése után a "export prics (xlsx)" hivatkozással tekintheti át.

#### <a name="pricing"></a>Díjszabás

- **Díjszabási modell**: átalánydíjas vagy Seat-alapú

**Átalány:** Az ajánlathoz való hozzáférés engedélyezése egyetlen havi vagy éves díjszabási díjszabással. Ezt más néven a hely alapú díjszabásnak is nevezzük. Ezzel az árképzési modellel meghatározhat olyan mért csomagokat is, amelyek a Piactéri mérési szolgáltatás API-ját használják, hogy a nem standard egységek alapján felszámolják az ügyfeleket.  A mért számlázással kapcsolatos további információkért lásd: [mért számlázás a Marketplace-mérési szolgáltatás használatával](./saas-metered-billing.md).

**Felhasználónként:** Az ajánlathoz való hozzáférést az ajánlatot vagy a foglalt munkaállomásokat elérő felhasználók számán alapuló díj alapján teheti meg. Ez a felhasználó-alapú modell lehetővé teszi, hogy beállítsa az árak alapján engedélyezett felhasználók minimális és maximális számát. Így a különböző díjszabási pontok a felhasználók számától függően konfigurálhatók több csomag konfigurálásával.  Ezeket a mezőket nem kötelező megadni. Ha nem jelöli be a jelölőnégyzetet, a rendszer a felhasználók számát úgy értelmezi, hogy nem rendelkezik korláttal (az 1. és a maximális érték legfeljebb annyit tud támogatni). Ezeket a mezőket a csomag frissítésének részeként lehet szerkeszteni.

A közzétételt követően a számlázási díjszabási modell választása nem módosítható. Emellett az ugyanarra az ajánlatra vonatkozó összes csomagnak ugyanazzal az árképzési modellel kell rendelkeznie.

- **Számlázási időszak**: havi vagy éves

Válassza ki azt a gyakoriságot, amellyel az ügyfeleknek meg kell fizetniük a felsorolt árakat. Legalább egy havi vagy éves árat meg kell adni, vagy mindkét lehetőséget elérhetővé kell tenni az ügyfelek számára.

- **Díj**: USD/hó vagy USD/év

A helyi pénznemben (USD = Egyesült Államok dollár) beállított díjak az összes kiválasztott piac helyi pénznemére lesznek átalakítva a telepítés során elérhető aktuális árfolyamok használatával. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot. A jelen díjszabás és a saját beállítások ellenőrzése a felelős.
*@no__t – a 1You először mentenie kell a díjszabási módosításokat, hogy lehetővé váljon az árképzési adatmennyiség exportálása.*

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások vonatkoznak a csomag közzététele után megjelenő változásokra:

- A csomag közzétételekor a díjszabási modell nem módosítható.
- Miután közzétett egy számlázási időszakot egy csomaghoz, később nem távolítható el.
- Miután közzétette a csomag egy piacának árát, később nem módosítható.

### <a name="plan-audience"></a>Célközönség megtervezése

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Az Azure AD-bérlői azonosítók használatával a korlátozott célközönséghez is hozzárendelhet tagságot.

#### <a name="privacy"></a>Adatvédelem

- **Ez egy privát csomag** (opcionális jelölőnégyzet)

Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a terv magánjellegű legyen, és csak a választott célközönség számára legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatónak kell maradnia. (A csomag nem konfigurálható újra privát csomagként).

- **Korlátozott célközönség (bérlői azonosítók)**

Rendelje hozzá azt a célközönséget, amely hozzáfér ehhez a privát csomaghoz. A hozzáférés hozzá van rendelve a bérlői azonosítók használatával, hogy tartalmazza a hozzájuk rendelt összes bérlői azonosító leírását. . Csv számolótábla-fájl importálásakor legfeljebb 10 bérlői azonosító adható hozzá, vagy 20 000 ügyfél bérlői azonosítóját.

A bérlő egy szervezet képviselete, amelynek azonosítója GUID (globálisan egyedi azonosító, az erőforrások azonosítására szolgáló 128 bites egész szám). Az Azure AD egy dedikált példánya, amelyet a szervezetek vagy alkalmazásfejlesztők kapnak, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak az Azure, a Microsoft Intune vagy a Microsoft 365 szolgáltatásra. Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. A bérlő vizsgálatához jelentkezzen be a Azure Portalba az alkalmazás kezeléséhez használni kívánt fiókkal. Ha rendelkezik bérlővel, automatikusan bejelentkezik, és közvetlenül a fióknév alatt láthatja a bérlő nevét. Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtár-/bérlőazonosítója (egy GUID), valamint a tartománya. Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik. A szervezet bérlői AZONOSÍTÓját a következő helyen található tartománynév URL-címével is megkeresheti: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Míg a SaaS a bérlői azonosítókat használja a privát célközönség definiálásához, az egyéb ajánlati típusok az Azure-előfizetési azonosítókat (amelyek GUID-ként is jelölik) használhatják.

> [!NOTE]
> A privát célközönség (vagy a korlátozott célközönség) eltér az előzetes verzió célközönségtől. Az **[előnézet](#preview)** lapon megadhatja az előnézeti célközönséget. Az előzetes verzió célközönsége a piactéren élőben közzétett ajánlat *előtt* jogosult az ajánlathoz való hozzáférésre. Bár a magánjellegű célközönség megjelölése csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (privát vagy nem) is megtekintheti, de csak a korlátozott előzetes verzióban, a terv tesztelése és ellenőrzése közben.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Példa a piactéren belüli csomagok listájára

![Példa a Piactéri csomagokra – megjegyzések](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Próbaüzem

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="publish"></a>Közzététel

#### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután elvégezte az ajánlat összes szükséges szakaszt, válassza a **Közzététel** elemet a portál jobb felső sarkában. A rendszer átirányítja a **felülvizsgálati és a közzétételi** oldalra. 

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - *Nincs elindítva* – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
    - *Hiányos* – ez azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információra van szüksége. Lépjen vissza a szakasz (ok) ra, és frissítse azt.
    - *Complete (kész* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- Adjon meg tesztelési útmutatást a minősítési csapatnak, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez hasznos kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Vissza kell térnie a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat közzétételéhez a nyilvános (vagy ha egy privát ajánlat, a privát közönség) számára.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
