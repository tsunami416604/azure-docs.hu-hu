---
title: Új SaaS-ajánlat létrehozása a kereskedelmi piactéren
description: Az Azure Marketplace-en, a AppSource-on vagy a Cloud Solution Provider (CSP) programon keresztüli, a Microsoft partner Center webhelyen található kereskedelmi Piactéri portál használatával új, szolgáltatásként nyújtott szoftveres (SaaS-) ajánlat létrehozásához.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 300dd634cc99e5203145619b475d813705f09553
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815695"
---
# <a name="create-a-new-saas-offer"></a>Új SaaS-ajánlat létrehozása

A szolgáltatott szoftver (SaaS)-ajánlatok létrehozásának megkezdéséhez először létre kell [hoznia egy partner Center-fiókot](./create-account.md) , és meg kell nyitnia a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), ahol az **Áttekintés** lapon be van jelölve.

![Kereskedelmi piactér irányítópultja a partner Centerben](./media/new-offer-overview.png)

Válassza ki az + **új ajánlatot...** gombra, majd válassza ki a **szoftvert szolgáltatásként** menüpontot. 

Ha kijelöl egy másik ajánlat típusát, a rendszer átirányítja a régebbi [Cloud Partner Portalra](https://cloudpartner.azure.com/).  Jelenleg csak SaaS-ajánlatok érhetők el a partner Center kereskedelmi piactér portálján. 

![Ajánlati ablak létrehozása a partner Centerben](./media/new-offer-click.png)

Megjelenik az **új ajánlat** párbeszédpanel. 

![Új ajánlat párbeszédpanel](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>Ajánlat azonosítója és alias

- **Ajánlat azonosítója**: A fiókban lévő egyes ajánlatok egyedi azonosítója. Ez az azonosító látható lesz az ügyfelek számára a Piactéri ajánlat URL-címében, és Azure Resource Manager sablonokat (ha vannak ilyenek). Az ajánlat-azonosító csak kisbetűket, alfanumerikus karaktereket (például kötőjeleket és aláhúzásokat) tartalmazhat. Ez 50 karakterre van korlátozva, és a *Létrehozás*gombra kattintva nem módosítható.  
Példa: test-Offer-1
<br>Eredményül kapott URL-cím:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ajánlat aliasa**: A partner Center portálon az ajánlatra való hivatkozáshoz használt név. Ezt a nevet nem fogja használni a piactéren, és nem egyezik meg az *ajánlat nevével* és az ügyfelek számára megjelenített egyéb értékekkel. Ez az érték nem módosítható a *Létrehozás*gombra kattintva.

<br>Példa: Tesztelési ajánlat 1&#8482;

Kattintson a **Létrehozás** gombra.  Ehhez az ajánlathoz létrejön egy **ajánlat** áttekintő lapja.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **ajánlat áttekintése** oldalon a következők szerepelnek: 

- A **közzétételi állapot** megjeleníti az ajánlat közzétételéhez szükséges lépések vizuális megjelenítését, valamint azt, hogy az egyes lépések mennyi ideig tartanak. Hiányos közzétételi lépések ikonjai szürkén jelennek meg. 

- Az **ajánlat** áttekintő menüjében az ajánlaton végrehajtott műveletekre mutató hivatkozásokat tartalmazó lista szerepel. A műveletek listája az ajánlathoz választott kiválasztási alapján változik.  
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

#### <a name="saas-on-azure-billing-infrastructure-costs"></a>SaaS Azure számlázási infrastruktúra-költségekkel
Ha az SaaS-ajánlat az Azure-ban üzemel, akkor a közzétevőnek az Azure-infrastruktúra használati díjait és a szoftverlicenc-díjat egyetlen költségként kell eltárolnia. Ez a költség az ügyfél számára átalányösszegű havi díj formájában jelenik meg. Az Azure-infrastruktúra használatának felügyelete és számlázása Önnek, a partnernek közvetlenül történik. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadók általában az Azure-infrastruktúra használati díjait az szoftverlicenc díjszabása szerint csomagolják. 

A szoftver licencelési díjait havi, ismétlődő, a telephelyen alapuló előfizetés átalánydíjas díjaként mutatjuk be.

|**A licenc díja**|**$100/hó**|
|:---|:---|
|Azure-használati díj (D1/1-Core)|A számlázás közvetlenül a közzétevőre történik, nem az ügyfél|
|Az ügyfelet a Microsoft számlázza|$100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján)|

- Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.
- Azok a partnerek, akik a **kedvezményes Piactéri szolgáltatási díjjal** rendelkeznek, csökkentett tranzakciós díjat kapnak az SaaS-ajánlatokról, a május 2019-ig, 2020-ig. Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $90,00-et a közzétevőnek.

> [!NOTE]
> **Csökkentett Piactéri szolgáltatási díj**: A kereskedelmi piactéren közzétett egyes SaaS-ajánlatok esetében a Microsoft a piactér szolgáltatási díját 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra. Ahhoz, hogy az ajánlat megfelelő legyen, legalább az egyik ajánlatát a Microsoftnak kell kijelölnie, mint az IP-címek közös értékesítésének vagy az IP-címek közös értékesítésének prioritása.  A jogosultságot az egyes naptári hónapok végét megelőzően legalább öt (5) munkanapon belül teljesíteni kell, hogy megkaphassa a Marketplace szolgáltatási díját a hónapra.  A Piactéri szolgáltatási díj nem vonatkozik a kereskedelmi Piactéren keresztül elérhető virtuális gépekre, felügyelt alkalmazásokra vagy más termékekre.  A csökkentett Piactéri szolgáltatási díj csak a Microsoft által a 2019 és a 2020. június 30. között gyűjtött licencek díjaihoz érhető el.  Ezt követően a Piactéri szolgáltatási díj visszaáll a normál mennyiségre. 

|**Microsoft-számlák**|**$100/hó**|
|:---|:---|
|A Microsoft a licencelési díj 80%-ában fizet <br>**A minősített SaaS-alkalmazások esetében a Microsoft a licencelési díj 90%-ában fizet*|$80,00/hó <br>*$* 90,00/hó *|


#### <a name="csp-program-opt-in"></a>CSP-program – opt-in
A [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program lehetővé teszi, hogy a szoftverek több millió minősített Microsoft-ügyfelet érjenek el minimális marketing-és értékesítési befektetéssel.

- **Csatornák Az ajánlatom elérhetővé tétele a CSP** programban (jelölőnégyzet)

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

##### <a name="contact-me"></a>Kapcsolatfelvétel
Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [](#connect-lead-management)az érdeklődők felügyeletének összekötése. 

## <a name="example-marketplace-offer-listing"></a>Példa a piactér ajánlatának listázására

![Példa a piactér ajánlatának listázására megjegyzésekkel](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Tesztelési meghajtó engedélyezése

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt megkeresi a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Tesztelési meghajtó engedélyezése** jelölőnégyzetet 

A test Drive engedélyezésével a rendszer arra kéri, hogy állítson be egy demonstrációs környezetet az ügyfeleknek az ajánlat meghatározott időn belül történő kipróbálásához. 

### <a name="type-of-test-drive"></a>A tesztelési meghajtó típusa

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : A Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve a kiépítést és az üzembe helyezést is) egy üzleti központi vállalati erőforrás-tervezési rendszerhez (pénzügy, műveletek, ellátási lánc, CRM stb.).  
- **[Dynamics 365 a Customer engagement szolgáltatáshoz](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : A Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve a kiépítést és az üzembe helyezést is) az ügyfél-engagement rendszer számára (értékesítés, szolgáltatás, projekt szolgáltatás, mező szolgáltatás stb.).  
- **[A Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : A Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve a kiépítést és az üzembe helyezést is) pénzügyi és üzemeltetési vállalati erőforrás-tervezési rendszerekhez (pénzügy, műveletek, gyártás, ellátási lánc stb.). 
- **[Logikai alkalmazás](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Egy központi telepítési sablon, amely magában foglalja az összes összetett megoldás architektúráját. Minden egyéni terméknek ilyen típusú tesztelési meghajtót kell használnia.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Egy egyéni beépített irányítópultra mutató beágyazott hivatkozás. Azok a termékek, amelyek interaktív Power BI vizualizációt kívánnak bizonyítani, ezt a típusú tesztelési meghajtót kell használniuk. Itt a beágyazott Power BI URL-címére kell feltöltenie.

#### <a name="additional-test-drive-resources"></a>További tesztelési meghajtó erőforrásai
- [A test Drive technikai ajánlott eljárásai](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A test Drive marketing ajánlott eljárásai](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [A test Drive áttekintése egy pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

Közvetlenül kapcsolódhat az ügyfelekhez a piactéren kínált ajánlat listázásával, és összekapcsolhatja az Ügyfélkapcsolat-kezelési (CRM) rendszerét, hogy az ügyfelek kapcsolattartási adatait azonnal megkapja, miután az ügyfél kifejezte érdeklődését, vagy üzembe helyezi a termék.

- **Érdeklődő célhelyének kiválasztása** (legördülő menü): Adja meg a kapcsolati adatokat arra a CRM-rendszerre, amelyre az ügyfél-érdeklődőket szeretné küldeni. 

A partner Center a következő CRM-rendszereket támogatja az érdeklődők felügyeletéhez. Válassza ki a telepítési utasítások hivatkozását.

- Azure Blob – adja meg a kapcsolattartási e-maileket, a tároló nevét és a Storage-fiók kapcsolati karakterláncát. 
- [Azure Table](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – adja meg a kapcsolattartási e-mail és a Storage-fiók kapcsolati sztringjét. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – adjon meg kapcsolattartási e-mailt, URL-címet és hitelesítési módot (Office 365 vagy Azure Active Directory).
- [Https-végpont](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – adja meg a kapcsolattartási e-mail és a https-végpont URL-címét. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – adja meg a kapcsolattartási e-mailt, az űrlap azonosítóját, a Munchkin-fiókot és a kiszolgáló azonosítóját.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) – adja meg a kapcsolattartási e-mailt és a szervezet azonosítóját. 

#### <a name="additional-lead-management-resources"></a>További érdeklődői felügyeleti erőforrások
- [Az érdeklődők felügyeletével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Gyakori vezető konfigurációs hibák](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Az érdeklődők felügyelete – Áttekintés egy pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

## <a name="properties"></a>properties
A **Properties (Tulajdonságok** ) lapon megtekintheti az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az ajánlatot támogató jogi szerződéseket és az alkalmazás verzióját. 

A mezők befejezése után válassza a **Mentés** lehetőséget. 

### <a name="category"></a>Category
Válasszon legalább egyet (1), és legfeljebb három (3) kategóriát, amelyek az ajánlatnak a piactér megfelelő keresési területein való csoportosításához használhatók. Kérjük, tekintse meg, hogyan támogatja az ajánlat a kategóriákat az ajánlat leírásában. 

### <a name="industry"></a>Iparág
Válasszon akár két (2) iparágat is, amelyek az ajánlatnak a piactér megfelelő keresési területein való csoportosítására szolgálnak. Ha az ajánlat nem az iparágra jellemző, ne válasszon egyet. Kérjük, hívja meg az ajánlat leírásában, hogy az ajánlat hogyan támogatja a kiválasztott iparágakat. 

### <a name="app-version"></a>Az alkalmazás verziója
Ez egy nem kötelezően kitöltendő mező, amely az AppSource piactéren az ajánlat verziószámának azonosítására szolgál. 

### <a name="standard-contract"></a>Standard szerződés

- **Standard szintű szerződést használ?**

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft egy standard szintű szerződést biztosít, amely megkönnyíti a tranzakciót a piactéren. 

Az egyéni használati feltételek és kikötések elvégzése helyett az Azure Marketplace-közzétevők dönthetnek úgy, hogy a standard szintű szerződés keretében kínálják a szoftvereket, amelyeket csak egyszer kell bemutatni és elfogadni. 

A standard szintű szerződés a következő címen érhető https://go.microsoft.com/fwlink/?linkid=2041178 el:.

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

- **Név** (kötelező): Az itt definiált név jelenik meg az ajánlatnak az Ön által választott piactéren () való listáján. A név előre fel van töltve az előző **új ajánlat** bejegyzése alapján.  Lehet, hogy ez védjeggyel van elvégezve.  Ez nem tartalmazhat szóközt, hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie.
- **Összefoglalás** (kötelező): Adja meg az ajánlat rövid leírását, amelyet a Piactéri lista (ok) keresési eredményeiben kíván használni. Ebben a mezőben legfeljebb 100 karaktert lehet megadni.
- **Leírás** (kötelező): Adja meg a piactér-lista (ek) áttekintésében megjelenítendő ajánlat leírását. Érdemes figyelembe venni az érték kiosztását, a főbb előnyöket, a kategória-vagy iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket, a szükséges közzétételeket, valamint egy hivatkozást, amely további információkat tartalmaz.
Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. További tippekért tekintse meg a [nagyszerű alkalmazás leírását](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)ismertető cikket.
- **Keresési kulcsszavak**: Adjon meg legfeljebb három olyan keresési kulcsszót, amelyet az ügyfelek az ajánlat megtalálására használhatnak a piactéren.
- **Útmutató az első lépésekhez** (kötelező): Megtudhatja, hogyan konfigurálhatja és kezdheti meg az alkalmazás használatát a potenciális ügyfelek számára.  Ez a rövid útmutató a részletesebb online dokumentációra mutató hivatkozásokat is tartalmaz. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

#### <a name="description"></a>**Leírás**

A mező kitöltése kötelező. A leírásban szerepeltetni kívánt elemek: 

* A Leírás első néhány mondatában egyértelműen ismertesse ajánlata értékét.  
* Ne feledje, hogy az első néhány mondat a keresőmotor eredményei között jelenhet meg.  
* Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez. Ehelyett a megadott értékre kell összpontosítania.  
* A lehető legnagyobb mértékben használja az iparági specifikus szókincset vagy a juttatás-alapú szövegezést. 

Az érték kiosztásának alapvető összetevőinek a következő információkat kell tartalmazniuk: 

* A termék leírása. 
* A termékből származó előnyöket biztosító felhasználó típusa. 
* Az ügyfélnek szüksége van a termék címére, vagy a fájdalomra. 

Annak érdekében, hogy az ajánlat leírása jobban megtörténjen, HTML-címkék használatával formázhatja a leírást. 

1. Ha bekezdéseket szeretne létrehozni, vegye fel `<p>` a szöveget a szöveg megadásával, és `</p>` adja hozzá a végéhez.

    **Példa**: 

    `<p>`Ez az első bekezdés. `</p>` <br>
    `<p>`Ez a második bekezdés. `</p>` <br>

    A fentiek így néznek ki:

    <p> Ez az első bekezdés. </p>
    <p> Ez a második bekezdés. </p>

1. Ha listajeles listát kíván hozzáadni az **elemek listájához**, vigye a szöveget az `<li>` alábbi címkékre. `<li>` A és `</li>` `<ul>` a címkén belül több listajeles elemet (a és a címkék közötti elemeket) is másolhat és beilleszthet. `</ul>` Ügyeljen rá, hogy hozzáadja `<ul></ul>`a-t. 

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

1. **Félkövér** tartalom hozzáadásához vegye `<b>` fel a szöveget a félkövérre szánt szöveg elejére, és `</b>` adja hozzá a félkövér betűvel szedett szöveg végéhez. 

    **Példa**: `<b>`INGYENES PRÓBAVERZIÓ`</b>`
    
    A fentiek hatására az ingyenes PRÓBAIDŐSZAKot a kirakatban található ajánlat leírásában félkövérre kell állítani. 

    **INGYENES PRÓBAVERZIÓ**

1. Ha **sortörést** szeretne hozzáadni a tartalomhoz, `<br>` adja hozzá a tartalmat az új sorban kezdeni kívánt tartalomhoz. Ha helyet szeretne hagyni, és a tartalom új sorban indul el, adja hozzá `<br><br>` a tartalmat a tartalomhoz. 

    **Példa**:

    Ez egy szöveges sor. `<br>`Ez egy olyan szövegsor, amely új sorban indul el. `<br><br>`Ez egy olyan sor, amely az alábbi két sort fogja elindítani. 

    A fentiek így néznek ki:

    Ez egy szöveges sor. <br> Ez egy olyan szövegsor, amely új sorban indul el. <br><br> Ez egy olyan sor, amely az alábbi két sort fogja elindítani. 

1. Ha szeretné megnövelni a **szöveg méretét**, először válassza ki, milyen nagy legyen a szöveg. Használja az alábbi példákat. Miután kiválasztotta a szöveg méretét, adja hozzá a megfelelő `<H*></H*>` címkéket a szöveg elejéhez és végéhez. 

    **Példa**:

    `<h1>`Ez az 1. címsor`</h1>` <br>
    `<h2>`Ez a 2. fejléc`</h2>` <br>
    `<h3>`Ez a 3. címsor`</h3>` <br>
    `<h4>`Ez a 4. címsor`</h4>` <br>
    `<h5>`Ez az 5. címsor`</h5>` <br>
    `<h6>`Ez a 6. címsor`</h6>` 

    A fentiek így néznek ki:

    <h1>Ez az 1. címsor</h1> 
    <h2>Ez a 2. fejléc</h2> 
    <h3>Ez a 3. címsor</h3> 
    <h4>Ez a 4. címsor</h4> 
    <h5>Ez az 5. címsor</h5> 
    <h6>Ez a 6. címsor</h6> 

#### <a name="links"></a>Hivatkozások

- **Adatvédelmi szabályzat** (kötelező): A szervezet adatvédelmi szabályzatára mutató hivatkozás. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson
- **CSP program marketing-anyagok** (nem kötelező): Ha úgy dönt, hogy az ajánlatot a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programra terjeszti, meg kell adnia egy hivatkozást a marketing-anyagokra. A CSP kiterjesztheti ajánlatát a minősített ügyfelek szélesebb körére azáltal, hogy lehetővé teszi a CSP-partnerek számára, hogy csomagot, piacot és viszonteladást nyújtsanak. Ezeknek a viszonteladóknak hozzá kell férniük az anyagokhoz az ajánlat marketingje érdekében. További információ: piacra jutási [szolgáltatások](https://partner.microsoft.com/reach-customers/gtm).
- **Hasznos hivatkozások** (nem kötelező): Az alkalmazással vagy a kapcsolódó szolgáltatásokkal kapcsolatos opcionális kiegészítő online dokumentumok cím és **URL-** **cím** megadásával. További hasznos hivatkozások hozzáadásához kattintson az **+ URL-cím hozzáadása**lehetőségre.

#### <a name="contact-information"></a>Kapcsolattartási adatok

- **Névjegyek**: Minden ügyfél-kapcsolattartó esetében adja meg az alkalmazott **nevét** , **telefonszámát**és **e-mail-** címét.  (Ezek *nem* lesznek nyilvánosan megjelenítve). A **támogatási kapcsolattartó** csoportnak egy **támogatási URL-címet** is meg kell adnia.  (Ez az információ nyilvánosan jelenik meg).

**Támogatási kapcsolattartó** (kötelező): Általános támogatási kérdésekre.

**Mérnöki kapcsolattartó** (kötelező): Technikai kérdésekben.

**Channel Manager-kapcsolattartó** (kötelező): A CSP programhoz kapcsolódó viszonteladói kérdésekért.

#### <a name="files-and-images"></a>Fájlok és lemezképek

- **Dokumentumok** (kötelező): Adjon hozzá kapcsolódó marketing-dokumentumokat az ajánlatához PDF formátumban, amely legalább egy (1) és legfeljebb három (3) dokumentumot biztosít az ajánlathoz.
- **Rendszerképek** (nem kötelező): Több hely is van, ahol az ajánlat emblémájának képei megjelenhetnek a piactéren (k), és a következő méretek szükségesek – kicsi: 48 x 48 képpont _(kötelező),_ közepes: 90 x 90 képpont, nagyméretű: 216 x 216 képpont _(kötelező),_ széles: 255 x 115 képpont és hős: 815 x 290 képpont. Az összes rendszerképnek a-ben kell lennie. PNG-formátum.
- **Képernyőképek** (kötelező): Az ajánlatot bemutató képernyőképeket adhat hozzá. Legfeljebb öt (5) képernyőkép adható hozzá, és 1280 x 720 képpont méretűnek kell lennie. Az összes rendszerképnek a-ben kell lennie. PNG-formátum.
- **Videók** (nem kötelező): Hivatkozásokat adhat hozzá az ajánlatát bemutató videókhoz. A YouTube-és/vagy a Vimeo-videókra mutató hivatkozásokat is használhat, amelyek az Ön ajánlatával együtt jelennek meg az ügyfelek számára. Emellett meg kell adnia a videó miniatűr képét is, amely az 1280 x 720 képpont méretű, PNG formátumú. Ajánlat legfeljebb négy videót jeleníthet meg.

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Előzetes verzió

Az **előnézet** lapon megadhat egy korlátozott **előzetes** verziót, amely az ajánlat közzétételét megelőzően közzéteszi az ajánlatot a piactér szélesebb közönsége számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után a **Go Live** -t kell választania, mielőtt az ajánlatát élőben közzé fogja tenni a piactér nyilvános célközönsége számára.

- **Előnézet célközönségének meghatározása: Adjon hozzá egy HRE/MSA-fiókhoz tartozó e-mailt soronként, valamint egy opcionális leírást.**

Akár tíz (10) e-mail-címet is hozzáadhat manuálisan, vagy húsz (20), ha egy CSV-fájlt tölt fel, a meglévő Microsoft-fiók (MSA) vagy a Azure Active Directory-fiókok esetében pedig az azonnali közzététel előtt segítséget nyújt az ajánlat érvényesítéséhez. Ezeknek a fiókoknak a hozzáadásával olyan célközönséget határozhat meg, amely előzetesen elérhetővé válik az ajánlat számára, mielőtt közzéteszi őket a piactéren. Ha az ajánlat már élő, akkor is megadhatja az előnézeti közönséget, hogy tesztelje az ajánlat módosításait és frissítéseit.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége a piactéren való élő közzététel _előtt_ jogosult az ajánlathoz való hozzáférésre. Dönthet úgy is, hogy létrehoz egy csomagot, és csak a privát célközönség számára teszi elérhetővé. A **csomag listázása** lapon megadhat egy privát célközönséget, amely az **Ez egy privát csomag** jelölőnégyzet. Az Azure-bérlői azonosítók használatával legfeljebb 20 000 ügyfelet adhat meg.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **technikai konfiguráció** lap az ajánlathoz való kapcsolódáshoz használt technikai részleteket (URL-cím, webhook, bérlői azonosító és alkalmazás-azonosító) határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék. Az összegyűjtött mezők használatát leíró diagramok a [SaaS-teljesítési API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)-k dokumentációjában találhatók.

- Kezdőlap **URL-címe** (kötelező): Adja meg a webhely URL-címét, amelyet az ügyfelek az ajánlatnak a piactéren való beszerzése után fognak kirakodni. Ez az URL-cím lesz a végpont, amely megkapja a jogkivonatot, amikor az ügyfél az oldalra irányítja. Ez a jogkivonat a teljesítési API-k feloldási funkciójával kicserélhető a kiépítési adatokra. Ezek az adatok és a gyűjtött egyéb információk a regisztráció befejezéséhez és a vásárlás aktiválásához használható, a felhasználó által interaktív weblap részeként.

- **Kapcsolat webhook** (kötelező): A Microsoft által az ügyfél nevében küldendő összes aszinkron eseményhez (például: Az SaaS-előfizetés érvénytelennek bizonyult), amelyhez kapcsolati webhookot kell megadnia. Ha még nem rendelkezik webhook-rendszerrel, a legegyszerűbb konfiguráció egy olyan http-végpont logikai alkalmazás, amely figyeli a neki küldött összes eseményt, majd megfelelően kezeli őket (például https:\//Prod-1westus.Logic.Azure.com:443/Work). További információk: munkafolyamatok [hívása, elindítása vagy beágyazása http-végpontokkal a Logic Appsben](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure ad-bérlő azonosítója** (kötelező): Azure Portalon belül létre kell hoznia [egy Azure Active Directory (ad-) alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , hogy a két szolgáltatás közötti kapcsolat ellenőrizhető legyen egy hitelesített kommunikáció mögött. A [bérlő azonosítójának](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)megkereséséhez lépjen a Azure Active Directoryra, és válassza a **Tulajdonságok**lehetőséget, majd keresse meg a megjelenő **címtár-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás azonosítója** (kötelező): Szüksége lesz az [alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) -azonosítóra és egy hitelesítési kulcsra is. Az értékek beszerzéséhez lépjen a Azure Active Directory, és válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e). A hitelesítési kulcs megkereséséhez lépjen a **Beállítások** elemre, és válassza a **kulcsok**lehetőséget. Meg kell adnia egy leírást és egy időtartamot, és ezután meg kell adni egy számértéket.

 Vegye figyelembe, hogy az Azure-alkalmazás azonosítója a közzétevő AZONOSÍTÓhoz van társítva, ezért ügyeljen arra, hogy ugyanazt az azonosítót használja az összes ajánlatában.

## <a name="plan-overview"></a>A terv áttekintése

A **terv** áttekintő lapja lehetővé teszi, hogy egy adott ajánlaton belül különböző tervezési lehetőségeket biztosítson. Ezek a csomagok (más néven SKU-ként) eltérőek lehetnek a verzió, a monetizálása vagy a szolgáltatási szintek tekintetében. Be kell állítania legalább egy csomagot, hogy eladja ajánlatát a piactéren.

A létrehozást követően megtekintheti a csomag neveit, azonosítóit, díjszabási modelljeit, rendelkezésre állását (nyilvános vagy privát), az aktuális közzétételi állapotot és az összes elérhető műveletet.

A **terv áttekintésében** elérhető **műveletek** a csomag aktuális állapotától függően változnak, és a következők lehetnek:

- Ha a terv állapota **draft** – Piszkozat törlése
- Ha a csomag állapota **élő** – eladási csomag leállítása vagy privát célközönség szinkronizálása

**Új csomag létrehozása** (legalább egy csomag azoknak, akik a Microsofton keresztül értékesítik azokat)

- **Csomag azonosítója:** Hozzon létre egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjának. Ez az azonosító látható lesz az ügyfelek számára a termék URL-címében és Azure Resource Manager-sablonokban (ha vannak ilyenek). Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon. Ehhez a csomag-AZONOSÍTÓhoz legfeljebb 50 karakter adható meg. Vegye figyelembe, hogy az azonosító nem módosítható a létrehozás kiválasztása után.
- **Csomag neve:** Az ügyfelek ezt a nevet fogják látni, amikor azt döntik el, hogy melyik tervet kell kiválasztani az ajánlaton belül. Hozzon létre egyedi nevet az ajánlat minden csomagjának. A terv neve az adott ajánlat részét képező szoftvercsomagok megkülönböztetésére szolgál (például Ajánlat neve: Windows Server; tervek Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Csomag listázása

A csomag listázása lapon azok a nyelvek (és piacok) láthatók, ahol a csomag elérhető, a jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, keresési kifejezéseit stb.) az egyes nyelvekhez/piacokhoz.

#### <a name="plan-listing-details"></a>Lista részleteinek megtervezése

A terv nyelveinek kiválasztásakor megjelennek a **csomagra** vonatkozó információk, beleértve a **nevet** és a **leírást.**

- **Név**: Előzetesen kitöltve az előzetes verziójú **új díjcsomag** alapján, és az ajánlat "szoftvercsomag" címeként jelenik meg a piactéren.
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

Az ingyenes próbaverzióban jelenleg részt vevő ügyfél-előfizetésekkel kapcsolatos információk beszerzéséhez használja `isFreeTrial`az új API-tulajdonságot, amely igaz vagy hamis értékként lesz megjelölve. További információt a [SaaS Get előfizetés API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) -val foglalkozó témakörben talál.

>[!Note]
>Az ingyenes próbaverziók nem támogatottak a piactér-mérési szolgáltatást használó csomagok esetében.

#### <a name="markets"></a>Piacok

- **Piacok szerkesztése** választható

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be annak a piaci helynek a jelölőnégyzetét, amely számára elérhetővé szeretné tenni ezt a csomagot. Egy keresőmező és egy gomb a "Tax átutalt" országok kiválasztásához, amelyben a Microsoft az Ön nevében értékesítési és használati adót alkalmaz, és a segítségére is felhasználható. 


Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A díjszabást a módosítások mentése után a "export prics (xlsx)" hivatkozással tekintheti át.

#### <a name="pricing"></a>Díjszabás

- **Díjszabási modell**: Átalány vagy ülőhely-alapú

**Átalány:** Az ajánlathoz való hozzáférés engedélyezése egyetlen havi vagy éves díjszabási díjszabással. Ezt más néven a hely alapú díjszabásnak is nevezzük. Ezzel az árképzési modellel meghatározhat olyan mért csomagokat is, amelyek a Piactéri mérési szolgáltatás API-ját használják, hogy a nem standard egységek alapján felszámolják az ügyfeleket.  A mért számlázással kapcsolatos további információkért lásd: [mért számlázás a Marketplace-mérési szolgáltatás használatával](./saas-metered-billing.md).

**Felhasználónként:** Az ajánlathoz való hozzáférést az ajánlatot vagy a foglalt munkaállomásokat elérő felhasználók számán alapuló díj alapján teheti meg. Ez a felhasználó-alapú modell lehetővé teszi, hogy beállítsa az árak alapján engedélyezett felhasználók minimális és maximális számát. Így a különböző díjszabási pontok a felhasználók számától függően konfigurálhatók több csomag konfigurálásával.  Ezeket a mezőket nem kötelező megadni. Ha nem jelöli be a jelölőnégyzetet, a rendszer a felhasználók számát úgy értelmezi, hogy nem rendelkezik korláttal (az 1. és a maximális érték legfeljebb annyit tud támogatni). Ezeket a mezőket a csomag frissítésének részeként lehet szerkeszteni.

A közzétételt követően a számlázási díjszabási modell választása nem módosítható. Emellett az ugyanarra az ajánlatra vonatkozó összes csomagnak ugyanazzal az árképzési modellel kell rendelkeznie.

- **Számlázási időszak**: Havi vagy éves

Válassza ki azt a gyakoriságot, amellyel az ügyfeleknek meg kell fizetniük a felsorolt árakat. Legalább egy havi vagy éves árat meg kell adni, vagy mindkét lehetőséget elérhetővé kell tenni az ügyfelek számára.

- **Ár**: USD/hó vagy USD/év

A helyi pénznemben (USD = Egyesült Államok dollár) beállított díjak az összes kiválasztott piac helyi pénznemére lesznek átalakítva a telepítés során elérhető aktuális árfolyamok használatával. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot. A jelen díjszabás és a saját beállítások ellenőrzése a felelős.
**Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását.*

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások vonatkoznak a csomag közzététele után megjelenő változásokra:

- A csomag közzétételekor a díjszabási modell nem módosítható.
- Miután közzétett egy számlázási időszakot egy csomaghoz, később nem távolítható el.
- Miután közzétette a csomag egy piacának árát, később nem módosítható.

### <a name="plan-audience"></a>Célközönség megtervezése

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Az Azure AD-bérlői azonosítók használatával a korlátozott célközönséghez is hozzárendelhet tagságot.

#### <a name="privacy"></a>Személyes adatok védelme

- **Ez egy privát csomag** (Nem kötelező jelölőnégyzet)

Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a terv magánjellegű legyen, és csak a választott célközönség számára legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatónak kell maradnia. (A csomag nem konfigurálható újra privát csomagként).

- **Korlátozott célközönség (bérlői azonosítók)**

Rendelje hozzá azt a célközönséget, amely hozzáfér ehhez a privát csomaghoz. A hozzáférés hozzá van rendelve a bérlői azonosítók használatával, hogy tartalmazza a hozzájuk rendelt összes bérlői azonosító leírását. . Csv számolótábla-fájl importálásakor legfeljebb 10 bérlői azonosító adható hozzá, vagy 20 000 ügyfél bérlői azonosítóját.

A bérlő egy szervezet képviselete, amelynek azonosítója GUID (globálisan egyedi azonosító, az erőforrások azonosítására szolgáló 128 bites egész szám). Az Azure AD egy dedikált példánya, amelyet a szervezetek vagy alkalmazásfejlesztők kapnak, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak az Azure, a Microsoft Intune vagy a Microsoft 365 szolgáltatásra. Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. A bérlő vizsgálatához jelentkezzen be a Azure Portalba az alkalmazás kezeléséhez használni kívánt fiókkal. Ha rendelkezik bérlővel, automatikusan bejelentkezik, és közvetlenül a fióknév alatt láthatja a bérlő nevét. Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtár-/bérlőazonosítója (egy GUID), valamint a tartománya. Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik. A szervezet bérlői AZONOSÍTÓját a következő helyen található tartománynév URL-cím használatával is megkeresheti: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Míg a SaaS a bérlői azonosítókat használja a privát célközönség definiálásához, az egyéb ajánlati típusok az Azure-előfizetési azonosítókat (amelyek GUID-ként is jelölik) használhatják.

> [!NOTE]
> A privát célközönség (vagy a korlátozott célközönség) eltér az előzetes verzió célközönségtől. Az **[előnézet](#preview)** lapon megadhatja az előnézeti célközönséget. Az előzetes verzió célközönsége a piactéren élőben közzétett ajánlat *előtt* jogosult az ajánlathoz való hozzáférésre. Bár a magánjellegű célközönség megjelölése csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (privát vagy nem) is megtekintheti, de csak a korlátozott előzetes verzióban, a terv tesztelése és ellenőrzése közben.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Példa a piactéren belüli csomagok listájára

![Példa a Piactéri csomagokra – megjegyzések](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Próbaüzem

A **tesztvezetés** lapon egy bemutatót (vagy "tesztelési meghajtót") állíthat be, amely lehetővé teszi az ügyfeleknek, hogy a megvásárlása előtt kipróbálják az ajánlatot. További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)című cikkben talál. Ha már nem szeretne tesztelési meghajtót biztosítani az ajánlatához, térjen vissza az **[ajánlat beállítása](#offer-setup)** lapra, és törölje a **Test Drive engedélyezése**jelölőnégyzet jelölését.

### <a name="technical-configuration"></a>Technikai konfiguráció
A következő típusú tesztelési meghajtók érhetők el, amelyek mindegyike saját technikai konfigurációs követelményekkel rendelkezik.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (Technikai konfiguráció nem szükséges)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó technikai konfigurációja

Egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. További információ [Azure Resource Manager tesztelési meghajtó](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező): Jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.

- **Példányok**: Válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

**Gyors**: Ez a típusú példány üzembe van helyezve, és a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A hosszabb, hogy ezek a példányok mindig futtat az Azure-előfizetést, akkor számítunk fel a költségek nagyobb rendelkezésre állását. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

**Hideg**: Az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon**: Töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt.  További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)című cikkben található.

- **Teszt meghajtójának időtartama** (kötelező): Adja meg azt az időtartamot, ameddig a test meghajtója aktív marad, az órák száma szerint. A Test Drive Ez az időtartam lejárta után automatikusan leáll. Ez az időtartam csak egész számú órával állítható be (például a "2" óra ("1,5") nem érvényes.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció a Dynamics 365 Test Drive-hoz

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét azáltal, hogy a szolgáltatás üzembe helyezését és üzembe helyezését az ilyen típusú tesztelési meghajtó használatával végzi el és tartja karban. Az ilyen típusú üzemeltetett tesztvezetés konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés üzleti központi, vevői szerepvállalási vagy műveleti célközönségre irányul.

- **Egyidejű tesztelési meghajtók maximális** száma (kötelező): Állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért biztosítania kell, hogy elegendő licenc álljon rendelkezésre a maximálisan engedélyezett készlet támogatásához. A 3-5 javasolt értéke.

- **Teszt meghajtójának időtartama** (kötelező): Adja meg azt az időtartamot, ameddig a tesztelési meghajtó aktív marad az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ez az időtartam csak egész számú órával állítható be (például a "2" óra ("1,5") nem érvényes.  Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező): Az URL-cím, amelyen az ügyfél elkezdi a tesztelési meghajtót. A Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja https://testdrive.crm.dynamics.com) , és a mintaadatok telepítve vannak (például:.

- **Példány webes API URL-címe** (kötelező): A Dynamics 365-példány webes API URL-címének lekéréséhez jelentkezzen be a Microsoft 365-fiókjába, és navigáljon a **Beállítások** \&gt; **Testreszabás** \&gt; **Fejlesztői erőforrások** \&gt; A **példány webes API-ját (szolgáltatás gyökerének URL-címe)** másolja az itt https://testdrive.crm.dynamics.com/api/data/v9.0) található URL-címet (például:.

- **Szerepkör neve** (kötelező): Adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét. Ezt a rendszer a tesztelési meghajtó (például a test-Drive-role) során rendeli hozzá a felhasználóhoz.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Technikai konfiguráció a Logic app Test Drive-hoz

Minden egyéni terméknek ezt a típusú tesztelési meghajtó-telepítési sablont kell használnia, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyetlen kijelölésű legördülő lista): Jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. Ennek a legjobb módja a logikai alkalmazás helyi üzembe helyezése az Azure-előfizetésben a portálon, és annak ellenőrzése, hogy az megfelelően működik-e a kijelölés előtt.

- **Egyidejű tesztelési meghajtók maximális** száma (kötelező): Állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Teszt meghajtójának időtartama** (kötelező): Adja meg azt az időtartamot, ameddig a test meghajtója aktív marad, az órák száma szerint. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező): Adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, ahol a logikai alkalmazás tesztelési meghajtóját menti.

- **Azure Logic App-alkalmazás neve** (kötelező): Adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót rendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének kiépítése** (kötelező): Adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót felépíti az ügyfél befejeződése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI tesztelési meghajtókhoz nem szükséges technikai konfiguráció

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. További információ a[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) template-alkalmazások beállításáról.

### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Ha a tesztelési meghajtót az Ön nevében szeretné üzembe helyezni, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést. (Power BI tesztelési meghajtók esetében nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges): Adja meg az előfizetés AZONOSÍTÓját, hogy hozzáférést biztosítson az Azure-fiók szolgáltatásaihoz az erőforrás-használati jelentéskészítéshez és a számlázáshoz. Javasoljuk, hogy hozzon [létre egy külön Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) -előfizetést, amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a Azure Portalba való [](https://portal.azure.com/) bejelentkezéssel és a bal oldali menü előfizetések lapján érheti el. A lap kiválasztása esetén megjelenik az előfizetési azonosító (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező): Adja meg a Azure Active Directory (AD [](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)) BÉRLŐi azonosítóját. Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok** elemet, majd keresse meg a felsorolt 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címével is megkeresheti a [https://www.whatismytenantid.com](https://www.whatismytenantid.com)következő helyen:.

- **Azure ad-bérlő neve** (a dinamikus 365 szükséges): Adja meg a Azure Active Directory (AD) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad-alkalmazás azonosítója** (kötelező): Adja meg a Azure Active Directory-(AD-) [alkalmazás azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás-ügyfél titka** (kötelező): Adja meg az Azure AD-alkalmazás [ügyfelének titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. (Ne navigáljon el az oldalról, mielőtt ezt megtenné, különben nem lesz hozzáférése az értékhez.)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

### <a name="test-drive-listings-optional"></a>Tesztelési meghajtók listázása (nem kötelező)

A test Drive (tesztelési meghajtó) lapon található tesztelési **meghajtók listázása** lehetőség megjeleníti azokat a nyelveket (és piacokat), ahol a tesztelési meghajtó elérhető, jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a tesztvezetés részleteit (Leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): Írja le a tesztelési meghajtót, a bemutatott funkciókat, a felhasználó által a kísérletezéshez szükséges célokat, a felderített szolgáltatásokat és a megfelelő információkat, amelyekkel a felhasználó eldöntheti, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges): Magyarázza el, hogy mit kell tudnia az ügyfélnek a tesztelési meghajtó eléréséhez és használatához. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező): Részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók Videók** hozzáadása (nem kötelező): A videók feltölthetők a YouTube-ra vagy a Vimeo-ra, és itt egy hivatkozással és egy miniatűr képpel (533 x 324 képpont) jeleníthetők meg, így az ügyfelek megtekinthetik az információk megtekintését, így könnyebben megismerhetik a tesztelési meghajtót, beleértve a funkcióinak sikeres használatát ajánljuk és értse az előnyeit kiemelő forgatókönyveket.
  - **Név** szükséges
  - **URL-cím (csak YouTube vagy Vimeo)** szükséges
  - **Miniatűr (533 x 324px)** : A képfájlnak PNG formátumúnak kell lennie.

A mezők befejezése után válassza a **Mentés** lehetőséget.

## <a name="publish"></a>Közzététel

#### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután elvégezte az ajánlat összes szükséges szakaszt, válassza a **Közzététel** elemet a portál jobb felső sarkában. A rendszer átirányítja a **felülvizsgálati és a közzétételi** oldalra. 

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - *Nincs* elindítva – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
    - *Hiányos* – ez azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információra van szüksége. Lépjen vissza a szakasz (ok) ra, és frissítse azt.
    - *Complete (kész* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- Adjon meg tesztelési útmutatást a minősítési csapatnak, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez hasznos kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Vissza kell térnie a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat közzétételéhez a nyilvános (vagy ha egy privát ajánlat, a privát közönség) számára.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
