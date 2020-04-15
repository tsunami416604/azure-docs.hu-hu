---
title: Új SaaS-ajánlat létrehozása a Microsoft kereskedelmi piacszámára
description: Új szoftver szolgáltatásként (SaaS) ajánlat létrehozása a Microsoft AppSource, az Azure Marketplace vagy a Felhőszolgáltató (CSP) programon keresztül a Microsoft Partner Center Microsoft kereskedelmi piactér programjának használatával történő listázáshoz vagy értékesítéshez.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 45559978f710549088d75d0f0706b798ebad3821
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314091"
---
# <a name="create-a-new-saas-offer"></a>Új SaaS-ajánlat létrehozása

A Szoftver szolgáltatásként (SaaS) ajánlatként való létrehozásának megkezdéséhez győződjön meg arról, hogy először [hozzon létre egy Partnerközpont-fiókot,](./create-account.md) és nyissa meg a [Kereskedelmi piactér irányítópultját,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)és az **Áttekintés** lapot választotta.

![Kereskedelmi piactér irányítópultja a Partnerközpontban](./media/new-offer-overview.png)

>[!Note]
> Az ajánlat közzétételét követően a Partnerközpontban tett ajánlat szerkesztése csak az újbóli közzétételt követően frissül a rendszerben és az áruházfrontokban. A módosítások után mindenképpen küldje el közzétételre az ajánlatot.

Válassza ki a + **Új ajánlatot...** gombot, majd válassza ki a **Szoftvert szolgáltatásként menüelemként.**

Ha másik ajánlattípust választ, előfordulhat, hogy a program átirányítja a régebbi [Cloud Partner Portal-ra.](https://cloudpartner.azure.com/) Jelenleg csak a SaaS és a Dynamics 365 ajánlatok érhetők el a Partner Center Kereskedelmi piactéren.

![Ajánlatablak létrehozása a Partnerközpontban](./media/new-offer-click.png)

Megjelenik az **Új ajánlat** párbeszédpanel.

![Új ajánlat párbeszédpanel](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

- **Ajánlatazonosító:** A fiókban lévő minden egyes ajánlat egyedi azonosítója. Ez az azonosító látható lesz az ügyfelek számára a piactéri ajánlat és az Azure Resource Manager-sablonok URL-címében (ha van ilyen). Az ajánlatazonosítónak kisbetűsnek, alfanumerikusnak kell lennie (beleértve a kötőjeleket és az aláhúzásjeleket, de nem lehet szóköz). Az **ajánlatazonosító** legfeljebb 50 karakterből állhat, és a *Létrehozás*lehetőséget követően nem módosítható.  
Példa: teszt-ajánlat-1
<br>Az URL-cím eredménye:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ajánlat alias**: A Partnerközpont portálon az ajánlatra való hivatkozáshoz használt név. Ez a név nem lesz használva a piactéren, és eltér az *ajánlat neve* és egyéb értékek jelennek meg az ügyfelek számára. Ez az érték nem módosítható a *Létrehozás (Létrehozás) lehetőséget*követően.

<br>Példa: Tesztajánlat 1&#8482;

Kattintson a **Létrehozás** gombra.  Ehhez az ajánlathoz létrejön egy **ajánlatáttekintő** lap.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintő** oldala a következőket tartalmazza:

- A **Közzététel állapot** az ajánlat közzétételéhez szükséges lépések vizuális megjelenítését jeleníti meg, és azt, hogy az egyes lépések mennyi ideig tart a művelet. A hiányos közzétételi lépés ikonjai szürkén jelennek meg.

- Az **Ajánlat áttekintése** menü az ajánlaton végzett műveletekhez kapcsolódó hivatkozások listáját tartalmazza. Ez a műveletlista az ajánlathoz megadott beállításoktól függően változik.  
    - Ha az ajánlat piszkozat - Vázlat törlése
    - Ha az ajánlat élő - Stop eladási ajánlat
    - Ha az ajánlat előzetes verzióban érhető el - Go-live
    - Ha még nem fejezte be a közzétevőkijelentkezést - Közzététel meglemondása

## <a name="offer-setup"></a>Ajánlat beállítása

Az **Ajánlat beállítása** lap a következő információkat kéri. A mezők **kitöltése** után válassza a Mentés lehetőséget.

- **Szeretne eladni a Microsofton keresztül?** (Igen/Nem)
    - **Igen,** ajánlatát a Microsofton keresztül szeretné értékesíteni, az Ön nevében a Microsoft piactéri tranzakciókat üzemeltet; Vagy 
    - **Nem**, inkább csak az ajánlatát szeretné felsorolni a piactereken keresztül, a Pénzügyi tranzakciókat a Microsofttól függetlenül feldolgozva.

### <a name="sell-through-microsoft"></a>Értékesítés a Microsofton keresztül

A Microsofton keresztül történő értékesítés jobb ügyfélfelderítést és -beszerzést tesz lehetővé, lehetővé teszi a Microsoft számára, hogy az Ön nevében marketplace-tranzakciókat bonyolítson le, és kihasználja a Microsoft globálisan elérhető kereskedelmi képességeit.

#### <a name="saas-offer-requirements"></a>SaaS ajánlati követelmények

Annak érdekében, hogy a Szoftver szolgáltatásként (SaaS) a Partnercenter kereskedelmi piactérén keresztül szerepeljen, a következő feltételeknek kell teljesülniük:

- Az ajánlatnak [az Azure Active Directoryt (Azure AD)](https://azure.microsoft.com/services/active-directory/) kell használnia az identitáskezeléshez és -hitelesítéshez.
- Az ajánlat nak [saas-teljesítési API-kat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) kell használnia az Azure Piactérrel való integrációhoz.
- A szélesebb körű követelményekért tekintse meg a [SaaS ajánlatközzétételi útmutatóját.](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)

#### <a name="saas-pricing-and-billing-options"></a>SaaS-díjszabási és számlázási lehetőségek

A kiadó Azure-előfizetésében futó SaaS-megoldásokkal az ügyfelek által fizetett licencdíjak tartalmazzák annak az infrastruktúrának a költségét, amelyre a szoftver telepítve van. Az Azure-infrastruktúra használatát közvetlenül Ön, a partner kezeli és számlázja. A tényleges infrastruktúra-használati díjakat az ügyfél nem látja. A kiadóknak az Azure-infrastruktúra használati díjait a szoftverlicenc-díjszabásukba kell befizetniük. 

A SaaS havi vagy éves számlázást kínál átalánydíj alapján, felhasználónként, vagy a forgalmi díjas számlázási szolgáltatás használatával a fogyasztási díjak alapján. A Microsoft kereskedelmi piactere egy ügynökségi modellen működik, amelynek során a kiadók beállítják az árakat, a Microsoft kiszámlázza az ügyfeleket, a Microsoft pedig bevételt fizet a kiadónak, miközben visszatartja az ügynökségi díjat.

Az alábbi táblázat a költségek és a kifizetések bontását mutatja be az ügynökségi modell bemutatására.

|**A licenc költsége**|**$100 havonta**|
|:---|:---|
|Az Azure használati költsége (D1/1-Core)|Közvetlenül a kiadónak számlázva, nem az ügyfélnek|
|Az ügyfél számláját a Microsoft számlája|$100.00 havonta (A Kiadónak figyelembe kell vennie a licencdíjban felmerült vagy átmenő infrastrukturális költségeket)|

|**Microsoft-számlák**|**$100 havonta**|
|:---|:---|
|A Microsoft a licencköltség 80%-át fizeti <br>**Minősített SaaS-alkalmazások esetén a Microsoft a licencköltség 90%-át fizeti*|$80.00 havonta <br>*$* 90.00 havonta*|

- Ebben a példában a Microsoft 100,00 USD-t számláz az ügyfélnek a szoftverlicencért, és 80,00 USD-t fizet ki a közzétevőnek.
- Azok a partnerek, akik kvalifikálták magukat a **Csökkentett piactér szolgáltatási díjára,** 2019 májusától 2020 júniusáig csökkentett tranzakciós díjat kapnak a SaaS-ajánlatokon. Ebben az esetben a Microsoft 100,00 USD-t számláz a szoftverlicencért, és 90,00 USD-t fizet ki a közzétevőnek.

> [!NOTE]
> **Csökkentett Piactéri szolgáltatási díj:** A Kereskedelmi piactéren közzétett bizonyos SaaS-ajánlatok esetében a Microsoft a Marketplace szolgáltatási díját 20%-ról (a Microsoft kiadói szerződésében leírtak szerint) 10%-ra csökkenti. Ahhoz, hogy az ajánlat jogosult legyen, legalább az egyik ajánlatnak a Microsoft által kijelölt IP-társértékesítésre vagy ip-társértékesítésre vonatkozó prioritásként kell megfelelnie.  A jogosultságnak minden naptári hónap vége előtt legalább öt (5) munkanapon belül teljesítenie kell, hogy megkapja ezt a kedvezményes Piactér-szolgáltatási díjat az egy hónapra.  A csökkentett piactéri szolgáltatási díj nem vonatkozik a virtuális gépekre, felügyelt alkalmazásokra vagy a kereskedelmi piactéren elérhetővé tett bármely más termékre.  A Csökkentett piactéri szolgáltatási díj csak minősített ajánlatokban érhető el a Microsoft által 2019.  Ezt követően a Marketplace szolgáltatási díj a normál összeghez fog visszatérni.

### <a name="list-through-microsoft"></a>Lista a Microsofton keresztül

Népszerűsítse vállalkozását a Microsofttal egy piactéri adatlap létrehozásával. Ha csak az ajánlat listázásához, és nem a Microsofton keresztül történő tranzakcióhoz választ, az azt jelenti, hogy a Microsoft nem vesz részt közvetlenül a szoftverlicenc-tranzakciókban. Nincs kapcsolódó tranzakciós díj, és a kiadó megtartja az ügyféltől beszedett szoftverlicencelési díjak 100%-át. A közzétevő azonban felelős a szoftverlicenc-tranzakció minden aspektusának támogatásáért, beleértve, de nem kizárólagosan: a megrendelések teljesítését, a mérést, a számlázást, a számlázást, a fizetést és a beszedést.

- **Hogyan szeretné, hogy a potenciális ügyfelek kapcsolatba lépjenek ezzel a hirdetésajánlattal?**

#### <a name="get-it-now-free"></a>Szerezd meg most (ingyenes)

Az ügyfeleknek szóló ajánlat ingyenes listázása egy érvényes URL-cím megadásával *(a http* vagy *https-től*kezdve), ahol [az Azure Active Directory (Azure AD) használatával egykattintásos hitelesítéssel](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)kaphatnak próbaverziót.  Például:`https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (lista)

Az ingyenes próbaverzióra mutató hivatkozással sorolja fel az ügyfeleknek adott ajánlatát egy érvényes URL-cím megadásával *(a http* vagy *https*kezdetével), ahol [az Azure Active Directory (Azure AD) használatával egykattintásos hitelesítéssel](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)kaphatnak próbaverziót.  Például: `https://contoso.com/trial/saas-app`. Az ingyenes próbaverziókat a szolgáltatás hozlétre, kezeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> A jogkivonatok az alkalmazás kap a próba-linken keresztül csak akkor használható, hogy a felhasználói adatok az Azure AD-n keresztül, hogy automatizálják a fiók létrehozása az alkalmazásban. A Microsoft-fiókok (MSA) nem támogatottak a token használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolat

Az ügyfélkapcsolati adatok at az ügyfélkapcsolat-kezelés (CRM) rendszer összekapcsolásával gyűjtheti. Az ügyfélengedélyt kér adatai megosztásához. Ezek az ügyféladatok, valamint az ajánlat neve, azonosítója és piactéri forrás, ahol az ajánlat található, a rendszer elküldi a CRM rendszer, amely már konfigurált. A CRM konfigurálásáról további információt az [Érdeklődőkezelés csatlakoztatása](#connect-lead-management)című témakörben talál.

## <a name="example-marketplace-offer-listing"></a>Példa a piactéri hirdetésekre

![Példa piactéri ajánlathirdetés jegyzetekkel](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Tesztvezetés engedélyezése

A tesztvezetés nagyszerű módja annak, hogy bemutassa ajánlatát a potenciális ügyfeleknek azáltal, hogy lehetőséget ad nekik arra, hogy "vásárlás előtt próbáljanak ki", ami fokozott konverziót és magasan képzett érdeklődők létrehozását eredményezi. További információ: [Az ügyfelek tesztelhetik az ajánlat vezetését.](./test-drive.md)

- **Tesztvezetés engedélyezése** (jelölőnégyzet)

A tesztvezetés engedélyezésével a rendszer megkéri, hogy állítson be egy demonstrációs környezetet az ügyfelek számára, hogy meghatározott ideig kipróbálhatják az ajánlatot. 

### <a name="test-drive-resources"></a>Tesztmeghajtó-erőforrások

- [A Test Drive marketinggel kapcsolatos bevált gyakorlatai a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- [Test Drive műszaki bevált módszerei](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A Test Drive áttekintése (PDF letöltés)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>További érdeklődőkezelési erőforrások
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Érdeklődőkezelés – áttekintés egy oldalazór](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Ne felejtse el **menteni,** mielőtt továbblépne a következő szakaszra.

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon megkell határoznia az ajánlat csoportosításához használt kategóriákat és iparágakat a piactereken, az ajánlatot támogató jogi szerződéseket és az alkalmazás verzióját.

A mezők **kitöltése** után válassza a Mentés lehetőséget.

### <a name="category"></a>Kategória

Válasszon ki legalább egy (1) és legfeljebb három (3) kategóriát az ajánlat megfelelő piactérkeresési területekre csoportosításához. Hívja ki, hogy az ajánlat támogatja ezeket a kategóriákat az ajánlat leírásában.

### <a name="industry"></a>Iparág

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Az alkalmazás verziója

Ez a mező nem kötelező, és az AppSource-piactéren használja az ajánlat verziószámának azonosítására.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piacterének általános szerződése

A Microsoft szabványos szerződéssablont biztosít.

- **Használja a Microsoft kereskedelmi piactér rekedező szerződését?**

Az ügyfelek beszerzési folyamatának egyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft a Microsoft kereskedelmi piacterére vonatkozó általános szerződést kínál a tranzakciók megkönnyítése érdekében a piacon. Ahelyett, hogy egyedi feltételeket gyártanának, a kereskedelmi piactér kiadói dönthetnek úgy, hogy az Általános Szerződés alapján kínálják szoftvereiket, amelyet az ügyfeleknek csak egyszer kell ellenőrizniük és elfogadniuk. Az alapszerződés itt található: https://go.microsoft.com/fwlink/?linkid=2041178.

A "Szabványos szerződés használata a kereskedelmi piachoz" jelölőnégyzet bejelölésével választhatja ki az Általános szerződés használatát a saját egyéni feltételek megadása helyett.

![A Szabványos szerződés jelölőnégyzet használata](./media/use-standard-contract.png)

> [!NOTE]
> Miután közzétett egy ajánlatot a Microsoft kereskedelmi piactér általános szerződésével, nem tudja használni a saját egyéni szerződéseit. Ez egy "vagy" forgatókönyv. Vagy a Standard Szerződés, **vagy** a saját szerződési feltételek alapján kínálja a megoldást. Ha módosítani szeretné az általános szerződés feltételeit, ezt a Szerződés általános módosításai révén teheti meg.

#### <a name="standard-contract-amendments"></a>Általános szerződésmódosítások

Az általános szerződésmódosítások lehetővé teszik a megjelenítők számára, hogy az egyszerűség kedvéért kiválasszák az általános szerződési feltételeket, és testre szabják a termékük vagy vállalkozásuk feltételeit. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már felülvizsgálták és elfogadták a Microsoft általános szerződését.

A kereskedelmi piactér kiadói számára kétféle módosítás érhető el:

- Univerzális módosítások: Ezek a módosítások általánosan alkalmazzák az általános szerződés minden ügyfél számára. Az univerzális módosításokat az ajánlat minden ügyfele számára bemutatják a beszerzési folyamatban. Az ügyfeleknek el kell fogadniuk az Általános Szerződés feltételeit és a módosítást, mielőtt felhasználhatnák az ajánlatot.
- Egyéni módosítások: Ezek a módosítások a standard szerződés speciális módosításai, amelyek csak az Azure-beli bérlői azonosítókon keresztül irányulnak bizonyos ügyfelekre. A közzétevők kiválaszthatják a megcélozni kívánt bérlőt. Csak a bérlői ügyfelek kapnak egyéni módosítási feltételeket az ajánlat beszerzési folyamatában.  Az ügyfeleknek el kell fogadniuk az Általános Szerződés feltételeit és a módosítás(oka)t, mielőtt felhasználhatják az ajánlatot.

>[!NOTE]
> Ez a két típusú módosítások verem egymásra. Az egyéni módosításokkal megcélzott ügyfelek a vásárlás során az Általános Szerződés általános módosítását is megkapják.

**A Microsoft kereskedelmi piacterének általános módosítási feltételei**: Ebben a mezőben adhatja meg az általános módosítási feltételeket. Ajánlatonként egyetlen általános módosítást is megadhat. Ebben a mezőben korlátlan számú karaktert írhat be. Ezek a feltételek az AppSource, az Azure Marketplace és/vagy az Azure Portalon jelennek meg az ügyfelek számára a felderítési és vásárlási folyamat során.

**A Microsoft kereskedelmi piacterének általános szerződésének egyéni módosítási feltételei**: Először válassza **az Egyéni módosítási feltételek hozzáadása lehetőséget**. Ajánlatonként legfeljebb 10 egyéni módosítási feltételt adhat meg.

- **Egyéni módosítási feltételek**: Adja meg az egyéni módosításfeltételeit az egyéni módosítási feltételek mezőben. Ebben a mezőben korlátlan számú karaktert írhat be. Csak a bérlői azonosítók adja meg ezeket az egyéni feltételeket az ajánlat vásárlási folyamat az Azure Portalon jelennek meg.  
- **Bérlői azonosítók** (szükséges): Minden egyéni módosítás legfeljebb 20 bérlői azonosítók megcélozható. Ha egyéni módosítást ad hozzá, legalább egy bérlői azonosítót meg kell adnia. A bérlői azonosító azonosítja az ügyfelet az Azure-ban. Kérheti az ügyfél ezt az azonosítót, és ők is megtalálhatja azt a portal.azure.com > Azure Active Directory > properties. A címtárazonosító értéke a bérlőazonosító (például 50c464d3-4930-494c-963c-1e951d15360e). A szervezet ügyfél bérlői azonosítóját is megkeresheti a Domainname URL-címük használatával [a Mi a Microsoft Azure és az Office 365-ös bérlői azonosítóm?](https://www.whatismytenantid.com).
- **Leírás** (nem kötelező): Opcionálisan adjon meg egy rövid leírást a bérlői azonosítóhoz, amely segít azonosítani a módosítással megcélzott ügyfelet.

#### <a name="terms-and-conditions"></a>Feltételek és kikötések

Ha saját egyéni feltételeket szeretne megadni, megadhatja azokat a Feltételek mezőben. Ebben a mezőben legfeljebb 10 000 karakternyi szöveget írhat be. Ha a szerződési feltételek hosszabb leírást igényelnek, adjon meg egy URL-hivatkozást ebbe a mezőbe, ahol a feltételek megtalálhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt megpróbálhatnák az ajánlatot.

Ne felejtse el **menteni,** mielőtt továbblépne a következő szakaszra.

## <a name="offer-listing"></a>Ajánlat lista

Az Ajánlat listalap azokat a nyelveket (és piacokat) jeleníti meg, ahol az ajánlat elérhető, jelenleg az angol (Egyesült Államok) az egyetlen elérhető hely. Ezenkívül ezen az oldalon megjelenik a nyelvspecifikus listaállapot, valamint a hozzáadott dátum/idő. Meg kell határoznia a piactér adatait (ajánlat neve, leírása, keresési kifejezések stb.) az egyes nyelvekhez / piacokhoz.

> [!NOTE]
> Az ajánlat listai tartalma (például az ajánlat leírása, dokumentumok, képernyőképek, használati feltételek és adatvédelmi irányelvek) nem szükséges angol nyelven, amennyiben az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Az is elfogadható, hogy *egy hasznos link URL-t* kínál tartalmat más nyelven, mint amelyet az ajánlat lista tartalmát.

### <a name="offer-listings"></a>Ajánlati listák

Adja meg a piactéren megjelenítendő részleteket, beleértve az ajánlat leírását és a marketingeszközöket.

- **Név** (kötelező): Az itt megadott név jelenik meg az ajánlat hirdetésének címeként a kiválasztott piactéren.Name (required): The name defined here will appear as the title of your offer listing on the marketplace(s) you selected. A név előre ki van töltve a korábbi **Új ajánlat** bejegyzés alapján. A név védjeggyel is védelmű lehet. Nem tartalmazhat emojikat (kivéve, ha azok a védjegy és a szerzői jogi szimbólumok), és legfeljebb 50 karakter lehet.
- **Összegzés** (kötelező): Adja meg az ajánlat rövid leírását, amelyet a piactéri adatlap(ok)ban kell használnia. Ebben a mezőben legfeljebb 100 karakter nyi szöveg adható meg.
- **Leírás** (kötelező): Adja meg az ajánlat leírását, amely megjelenik a piactéri lista(ok) áttekintésében. Fontolja meg egy értékajánlat, a legfontosabb előnyök, bármely kategória vagy iparági szövetség, alkalmazáson belüli vásárlási lehetőség, a szükséges közzététel és egy link felfedése, hogy többet tudjon meg.
Ebben a mezőben legfeljebb 3000 karakter nyi szöveg adható meg, beleértve a jelölést is. További tippeket az [Az alkalmazás leírásának írása](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)című témakörben talál.
- Keresési kulcsszavak: Adjon meg legfeljebb három keresési kulcsszót, amelyek segítségével az ügyfelek megtalálhatják az ajánlatot a piactéren.Search **keywords:** Enter up to three search keywords that customers can use to find your offer in the marketplace(s).
- **Első lépésekre vonatkozó utasítások** (szükséges): Az alkalmazás konfigurálásának és használatának megkezdése a potenciális ügyfelek számára.  Ez a rövid útmutató részletesebb online dokumentációra mutató hivatkozásokat tartalmazhat. Ebben a mezőben legfeljebb 3000 karakter nyi szöveg adható meg.

#### <a name="description"></a>**Leírás**

Ez a mező kötelező. A **leírásban**szerepeltetni fogandó elemek:

* Egyértelműen írja le az ajánlat értékajánlatát a leírás első néhány mondatában.  
* Ne feledje, hogy az első néhány mondat megjelenhet a keresőmotor találatai között.  
* Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson a szállít.  
* A lehető legnagyobb mértékben használjon iparág-specifikus szókincset vagy haszonalapú megfogalmazást.

Az értékajánlat alapvető összetevőinek a következő információkat kell tartalmazniuk:

* A termék leírása.
* A termék előnyeit élvező felhasználó típusa.
* Vásárlói igények vagy fájdalom, hogy a termék foglalkozik.

Ahhoz, hogy az ajánlat **leírása** vonzóbbá, használja a rich text szerkesztő formázni a leírást.

![A Rich Text szerkesztő használata](./media/text-editor2.png)

A Rich Text szerkesztő használatához kövesse az alábbi utasításokat:

- A tartalom formátumának módosításához jelölje ki a formázni kívánt szöveget, és jelöljön ki egy szövegstílust az alábbi módon:

     ![Szövegformátum módosítása a Rich Text szerkesztővel](./media/text-editor3.png)

- Ha listajeles vagy számozott listát szeretne hozzáadni a szöveghez, használja az alábbi beállításokat:

     ![Listák hozzáadása a Rich Text szerkesztővel](./media/text-editor4.png)

- A szöveg behúzásának hozzáadásához vagy eltávolításához használja az alábbi beállításokat:

     ![A Rich Text szerkesztő behúzása](./media/text-editor5.png)

#### <a name="links"></a>Hivatkozások

- **Adatvédelmi szabályzat** (kötelező): Hivatkozás a szervezet adatvédelmi irányelveire. Ön felelős annak biztosításáért, hogy alkalmazása megfeleljen az adatvédelmi törvényeknek és előírásoknak, és hogy érvényes adatvédelmi irányelveket biztosítson
- **CsP Program Marketing Materials** (nem kötelező): Adjon meg egy linket a marketing anyagok, ha úgy dönt, hogy kiterjeszti az ajánlatot a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program. A CSP kiterjeszti az ajánlatot a minősített ügyfelek szélesebb körére azáltal, hogy lehetővé teszi a CSP-partnerek számára az ajánlat összeegyeztetését, forgalmazását és viszonteladását. Ezeknek a viszonteladóknak hozzá kell férniünk az ajánlatmarketinghez szükséges anyagokhoz. További információ: [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Hasznos linkek** (nem kötelező): Választható kiegészítő online dokumentumok az alkalmazásodról vagy a kapcsolódó szolgáltatásokról, amelyek **cím** és **URL**megadásával szerepelnek. Adjon hozzá további hasznos hivatkozásokat a **+ URL hozzáadása**gombra kattintva.

#### <a name="contact-information"></a>Kapcsolattartási adatok

- **Kapcsolattartók:** Minden ügyfélkapcsolattartóhoz adjon meg egy alkalmazott **nevét,** **telefonszámát**és **e-mail címét.**  (Ezek *nem jelennek* meg nyilvánosan). A **támogatási kapcsolattartó** csoporthoz **támogatási URL-re** is szükség van.  (Ez az információ nyilvánosan *jelenik* meg).

**Támogatási kapcsolattartó** (szükséges): Általános támogatási kérdések esetén.

**Műszaki kapcsolat** (szükséges): Műszaki kérdések esetén.

**Csatornakezelő-kapcsolattartó** (kötelező): A kriptaprogrammal kapcsolatos viszonteladói kérdések esetén.

#### <a name="files-and-images"></a>Fájlok és képek

- **Dokumentumok** (kötelező): Az ajánlathoz kapcsolódó marketingdokumentumokat adhat hozzá PDF formátumban, ajánlatonként legalább egy (1) és legfeljebb három (3) dokumentumot biztosítva.
- **Képek** (nem kötelező): Több olyan hely van, ahol az ajánlat logóképei megjelenhetnek a piactér(ek) között, amelyekhez a következő méretek szükségesek - Kicsi: 48 x 48 képpont _(kötelező),_ Közepes: 90 x 90 képpont _(szükséges)_, Nagy: 216 x 216 képpont _(szükséges),_ Széles: 255 x 115 képpont és Hős: 815 x 290 képpont. Minden képnek a ban kell lennie. PNG formátumban.
- **Képernyőképek** (kötelező): Adjon hozzá képernyőképeket az ajánlatról. Legfeljebb öt (5) képernyőkép adható hozzá, és 1280 x 720 képpont méretűnek kell lennie. Minden képnek a ban kell lennie. PNG formátumban.
- **Videók** (nem kötelező): Az ajánlatot bemutató videókhoz mutató hivatkozásokat adhatsz hozzá. YouTube- és/vagy Vimeo-videókra mutató hivatkozásokat is használhatsz, amelyek az ügyfeleknek szóló ajánlatoddal együtt jelennek meg. Meg kell adnia a videó miniatűr képét is, amely 1280 x 720 képpontméretű PNG formátumban. Ajánlatonként legfeljebb négy videót jeleníthet meg.

Ne felejtse el **menteni,** mielőtt továbblépne a következő szakaszra.

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

- [Gyakorlati tanácsok a piactéri ajánlatlistákhoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Előzetes verzió

Az **Előnézet** lapon korlátozott **előnézeti közönséget** határozhat meg az ajánlat közzétételéhez, mielőtt élőben közzétenné az ajánlatot a szélesebb piactéri közönség számára.

> [!IMPORTANT]
> Miután ellenőrizte az ajánlatot az Előzetes verzióban, válassza az **Élő adás** lehetőséget, hogy az ajánlat élőben közzétehesse a piactér nyilvános közönségét.

- **Előnézeti célközönség definiálása: Soronként egyetlen AAD/MSA-fióke-mail-cím hozzáadása, valamint egy nem kötelező leírás.**

Adjon hozzá legfeljebb 10 e-mail címet manuálisan, vagy 20-at CSV-fájl feltöltésekor a meglévő Microsoft-fiókhoz (MSA) vagy Azure Active Directory-fiókokhoz, hogy segítsen az ajánlat érvényesítésében az élő közzététel előtt. Ezekkel a fiókokkal olyan közönséget határoz meg, amely előzetes hozzáférést kap az ajánlathoz, mielőtt közzétesszük a piactér(ek)ben. Ha az ajánlat már él, továbbra is megadhat egy előnézeti közönséget az ajánlat módosításainak vagy frissítéseinek teszteléséhez.

> [!NOTE]
> Az előnézeti közönség eltér a privát közönségtől. Az előnézeti közönség hozzáférhet az ajánlathoz, _mielőtt_ élőben közzétesszük őket a piactereken. Dönthet úgy is, hogy létrehoz egy csomagot, és csak privát közönség számára teszi elérhetővé. A **Terv listaelemlapján** privát közönséget definiálhat az **Ez egy privát csomag** jelölőnégyzet. Ezután az Azure tenant-id-azonosítók használatával legfeljebb 20 000 ügyfélből álló privát közönséget határozhat meg.

## <a name="technical-configuration"></a>Műszaki konfiguráció

A **Technikai konfiguráció** lap határozza meg az ajánlathoz való csatlakozáshoz használt technikai részleteket (URL-elérési út, webhook, bérlői azonosító és alkalmazásazonosító). Ez a kapcsolat lehetővé teszi számunkra, hogy az Ön ajánlatát a végfelhasználó számára, ha úgy döntenek, hogy megszerzi azt. Az összegyűjtött mezők használatát leíró diagramok a [SaaS teljesítési API-k](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)dokumentációjában érhetők el.

- **Céloldal URL-címe** (kötelező): Adja meg azt a webhely URL-címét, amelyen az ügyfelek az ajánlat marketplace-ről történő beszerzése után landolnak. Ez az URL lesz a végpont, amely egy jogkivonatot kap, amikor egy ügyfél az oldalra irányítja. Ez a jogkivonat a teljesítési API-k feloldása használatával kiépíthető részletekért. Ezek az adatok és minden más, amit gyűjt, felhasználható az Ön tapasztalatába épített ügyfél-interaktív weboldal részeként a regisztráció befejezéséhez és a vásárlás aktiválásához.

- **Kapcsolat webhook** (szükséges): Minden aszinkron esemény, hogy a Microsoft kell küldeni önnek az ügyfél nevében (például: SaaS-előfizetés érvénytelenné vált), meg kell adnia egy kapcsolat webhook. Ha még nem rendelkezik webhook-rendszerrel, a legegyszerűbb konfiguráció egy HTTP Endpoint Logic Alkalmazás, amely figyeli a feladott eseményeket, majd megfelelően kezeli őket (például https:\//prod-1westus.logic.azure.com:443/work). További információ: [Hívás, eseményindító vagy beágyazott munkafolyamatok HTTP-végpontokkal a logikai alkalmazásokban.](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)

- **Azure AD-bérlői azonosító** (kötelező): Az Azure Portalon belül egy [Azure Active Directory (AD) alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) kell létrehoznia, hogy ellenőrizhessük a két szolgáltatás közötti kapcsolatot egy hitelesített kommunikáció mögött. A [bérlői azonosító](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)megkereséséhez nyissa meg az Azure Active Directoryt, és válassza a **Tulajdonságok lehetőséget,** majd keresse meg a felsorolt **címtárazonosító** számot (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-alkalmazásazonosító** (kötelező): Az [alkalmazásazonosítóra](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) és egy hitelesítési kulcsra is szüksége van. Ezeknek az értékeknek a beírásához nyissa meg az Azure Active Directoryt, és válassza **az Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazásazonosítószámot** (például 50c464d3-4930-494c-963c-1e951d15360e). A hitelesítési kulcs megkereséséhez nyissa meg a **Beállítások lapot,** és válassza a **Kulcsok lehetőséget.** Meg kell adnia egy leírást és időtartamot, majd meg kell adnia egy számértéket.

>[!Note]
>Az Azure-alkalmazásazonosító társítva van a közzétevő azonosítójához, ezért győződjön meg arról, hogy ugyanazt az alkalmazásazonosítót használja az összes ajánlatban.

## <a name="plan-overview"></a>Terv – áttekintés

A **Tervezés áttekintése** lapon számos tervbeállítást biztosíthat ugyanazon az ajánlaton belül. Ezek a csomagok (más néven SK-k) verzió, monetizálás vagy szolgáltatási szintek tekintetében eltérőek lehetnek. Legalább egy csomagot be kell állítania ahhoz, hogy az ajánlata a piacon értékesíthesse az ajánlatot.

A létrehozás után látni fogja a csomagneveket, azonosítókat, díjszabási modelleket, elérhetőségi (nyilvános vagy privát), aktuális közzétételi állapotot és az esetleges enelérhető műveleteket.

**A** Terv **áttekintésében** elérhető műveletek a terv aktuális állapotától függően változnak, és a következőket tartalmazhatják:

- Ha a terv állapota **Vázlat** - Piszkozat törlése
- Ha a csomag állapota **Élő** – Stop sell csomag vagy Privát közönség szinkronizálása

**Új csomag létrehozása** (legalább egy csomag azok számára, akik a Microsofton keresztül kívánnak értékesíteni)

- **A terv azonosítója:** Hozzon létre egy egyedi tervazonosítót az ajánlatban szereplő minden csomaghoz. Ez az azonosító látható lesz az ügyfelek számára a termék URL-címében és az Azure Resource Manager-sablonokban (ha van ilyen). Csak kisbetűs, alfanumerikus karaktereket, kötőjeleket vagy aláhúzásjeleket használjon. Ehhez a csomagazonosítóhoz legfeljebb 50 karakter engedélyezett. Az azonosító nem módosítható a létrehozás kiválasztása után.
- **Terv neve:** Az ügyfelek ezt a nevet fogják látni, amikor eldöntik, hogy melyik csomagot válasszák ki az ajánlaton belül. Hozzon létre egy egyedi ajánlat nevét az ajánlatban szereplő minden csomaghoz. A csomag neve az ugyanazon ajánlat részét használó szoftvercsomagok (például Ajánlat neve: Windows Server; tervek: Windows Server 2016, Windows Server 2019) megkülönböztetésére szolgál.

### <a name="plan-listing"></a>A terv listája

A **Terv listaelem** lap azokat a nyelveket (és piacokat) jeleníti meg, ahol a csomag elérhető, jelenleg az angol (Egyesült Államok) az egyetlen elérhető hely. Ezenkívül ezen az oldalon megjelenik a nyelvspecifikus listaállapot, valamint a hozzáadott dátum/idő. Meg kell határoznia a piactér adatait (ajánlat neve, leírása, keresési kifejezések stb.) az egyes nyelvekhez / piacokhoz.

#### <a name="plan-listing-details"></a>A csomag listaadatai

A terv nyelveinek kiválasztásával megjelennek a **terv listaelemadatai,** beleértve a **nevet** és **a leírást.**

- **Név**: Előre kitöltött az előzetes **verzió új tervbejegyzése** alapján, és az ajánlat marketplace-en megjelenő "Szoftvercsomag" címeként jelenik meg.
- **Leírás:** Ez a leírás lehetőséget ad arra, hogy elmagyarázza, mi teszi ezt a szoftvertervet egyedivé, és miben különbözik az ajánlaton belüli egyéb szoftvertervektől. Legfeljebb 500 karakter lehet.

A mezők **kitöltése** után válassza a Mentés lehetőséget.

#### <a name="plan-pricing-and-availability"></a>Díjszabás és elérhetőség tervezése

A **Díjszabás és rendelkezésre állás** lapon konfigurálhatja azon piacokat, ahol ez a csomag elérhető lesz, a kívánt monetizálási modellt, árat és számlázási időszakot. Ezenkívül azt is jelezheti, hogy a tervet mindenki számára láthatóvá kívánja-e tenni, vagy csak bizonyos ügyfelek (privát közönség) számára.

##### <a name="enabling-free-trials"></a>Ingyenes próbaverziók engedélyezése

A Kereskedelmi piacon keresztül kínálson SaaS-ajánlatokat, amelyek lehetővé teszik, hogy egy hónapos ingyenes próbaverziót biztosítson a Microsofton keresztül történő értékesítés során. A forgalmi díjas csomagok kivételével minden számlázási modell és feltétel esetében az ingyenes próbaverziók támogatottak. Ez az opció lehetővé teszi az ügyfelek számára, hogy egy hónapos ingyenes hozzáférésen keresztül alacsony korláttal lépjenek be.  Ha úgy dönt, hogy engedélyezi az ajánlaton belüli csomagok ingyenes próbaidőszakát, az ügyfél nem tud fizetős előfizetéssé konvertálni a kezdeti egy hónapos időszak vége előtt.  Ez idő alatt az ajánlatot vásárló ügyfelek kipróbálhatják azokat a támogatott csomagokat, amelyeken engedélyezve van az ingyenes próbaverzió, és konvertálhatnak közöttük.  A fizetett előfizetésre való átváltás automatikusan történik a futamidő végén.

>[!Note]
>Ha az ügyfél úgy dönt, hogy ingyenes próbaverzió nélkül konvertál egy csomagra, az átalakítás megtörténik, de az ingyenes próbaverzió azonnal elveszik.  Is, ha egy ügyfél elkezd fizetni egy csomag, akkor már nem kap ingyenes próbaugyanazon az előfizetést újra, akkor is, ha konvertálni egy termékváltozat, amely támogatja az ingyenes próbaverziók.

Az ingyenes próbaverzió konfigurálásának lehetősége az ajánlat minden egyes csomagja számára elérhető. Keresse meg az egyes ajánlatdíjszabást és elérhetőséget, és jelölje be a jelölőnégyzetet, ha engedélyezni szeretné az egy hónapos próbaverziót.

![Egy hónapos ingyenes próbaverzió jelölőnégyzet](./media/free-trial-enable.png)

>[!Note]
>Miután a tranzakciós ajánlat megjelent egy ingyenes próbaverzió, nem lehet letiltani, hogy a terv. Győződjön meg arról, hogy ez a beállítás az első közzétételkor megfelelő, hogy ne kelljen újra létrehoznia a tervet.

Az ingyenes próbaverzióban jelenleg részt vevő ügyfél-előfizetésekkel kapcsolatos `isFreeTrial`információk megszerzéséhez használja az új API-tulajdonságot, amely igazként vagy hamisként lesz megjelölve. További információt a [SaaS Előfizetés beolvassa.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription)

>[!Note]
>Az ingyenes próbaverziók nem támogatottak a piactéri mérési szolgáltatást kihasználó csomagok esetében.

#### <a name="markets"></a>Piacok

- **Piacok szerkesztése** (nem kötelező)

Minden tervnek legalább egy piacon elérhetőnek kell lennie. Jelölje be a jelölőnégyzetet minden olyan piacon, ahol ezt a csomagot elérhetővé szeretné tenni. A súgó ban szerepel egy keresőmező és gomb az "Adóelengedési" országok kiválasztásához, amelyekben a Microsoft az Ön nevében átutalja az értékesítési és használati adót.

Ha már beállította a terv árait amerikai dollárban (USD), és egy másik piaci helyszínt ad meg, az új piac árát az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt tekintse át az egyes piacok árát. Az árak felülvizsgálhatók az "Export árak (xlsx)" hivatkozással a módosítások mentése után.

#### <a name="pricing"></a>Díjszabás

- **Árképzési modell**: Átalánydíjas vagy Seat alapú

**Átalánydíj:** Egyetlen havi vagy éves ár-átalányárral engedélyezheti az ajánlathoz való hozzáférést. Ezt néha webhelyalapú árképzésnek is nevezik. Ezzel a díjszabási modellel opcionálisan megadhatja a díjmérési csomagok, amelyek a piactéri mérési szolgáltatás API-t a nem szabványos egységek szerint az ügyfelek.  A forgalmi díjas számlázással kapcsolatos további információkért tekintse meg [a forgalmi díjas számlázást a piactér-mérési szolgáltatás használatával.](./saas-metered-billing.md)

**Felhasználónként:** Engedélyezze a hozzáférést az ajánlathoz az ajánlathoz hozzáférő vagy a foglaló ülőhelyeket elfoglaló felhasználók száma alapján. Ez a felhasználóalapú modell lehetővé teszi a felhasználók minimális és maximális számára az ár alapján megadott értéket. Így a különböző árpontok több csomag konfigurálásával konfigurálhatók a felhasználók száma alapján.  Ezek a mezők nem kötelezőek. Ha a felhasználó nincs bejelölve, a rendszer úgy értelmezi a felhasználók számát, hogy nincs korlátjuk (a rendszer által támogatott abból a mennyiségből legfeljebb 1, amennyi a rendszer ből áll). Ezeket a mezőket a terv frissítése ként szerkesztheti.

A közzétételt követően a számlázási díjszabási modell választása nem módosítható. Ezenkívül az ugyanazon ajánlatra vonatkozó összes csomagnak azonos árképzési modellel kell rendelkeznie.

- **Számlázási időszak**: Havi vagy éves

Válassza ki azt a gyakoriságot, amelyet a vevőknek a felsorolt árért fizetniük kell. Legalább egy havi vagy éves árat kell megadni, vagy mindkét opció elérhetővé tehető az ügyfelek számára.

- **Ár:** USD havonta vagy USD évente

A helyi pénznemben (USD = amerikai dollár) meghatározott árakat a rendszer a beállítás során rendelkezésre álló aktuális árfolyamok alapján az összes kiválasztott piac helyi pénznemére váltja át. A közzététel előtt ellenőrizze ezeket az árakat az árképzési táblázat exportálásával és az egyes piacok árainak áttekintésével. Ha egyéni árakat szeretne beállítani egy adott piacon, módosítsa és importálja az árképzési táblázatot. Ön a felelős az árak érvényesítéséért, és a beállítások a saját tulajdonában van.
*\*Az árképzési adatok exportálásának engedélyezéséhez először mentenie kell az árképzési módosításokat.*

A közzététel előtt alaposan tekintse át az árakat, mivel a terv közzététele után bizonyos korlátozások vonatkoznak arra, hogy mi változhat:

- A terv közzététele után a díjszabási modell nem módosítható.
- Ha egy csomag számlázási kifejezését közzétesz, később nem távolítható el.
- Ha a csomagban megjelenik egy piac ára, később nem módosítható.

### <a name="plan-audience"></a>Célközönség megtervezése

Lehetősége van úgy konfigurálni az egyes terveket, hogy mindenki vagy csak egy általad választott közönség számára látható legyen. Ehhez a korlátozott közönséghez rendelhet tagságot az Azure AD-bérlői azonosítók használatával.

#### <a name="privacy"></a>Adatvédelem

- **Ez egy privát csomag** (választható jelölőnégyzet)

Jelölje be ezt a jelölőnégyzetet, ha a csomagot privátá szeretné tenni, és csak az Ön által kiválasztott korlátozott közönség számára szeretné láthatóvá tenni. A privát csomagként való közzétételt követően frissítheted a célközönséget, vagy dönthetsz úgy, hogy a csomagot mindenki számára elérhetővé tesszük. Ha egy tervet mindenki számára láthatóként tesz közzé, annak mindenki számára láthatónak kell maradnia. (A terv nem konfigurálható újra magáncsomagként).

- **Korlátozott közönség (bérlői azonosítók)**

Rendelje hozzá azt a közönséget, amely nek hozzáférése lesz ehhez a privát csomaghoz. Az access bérlői azonosítók használatával van hozzárendelve, és minden hozzárendelt bérlői azonosító leírását tartalmazza. Legfeljebb 10 bérlői azonosító adható hozzá, vagy 20 000 ügyfél bérlői azonosítót adhat hozzá .csv számolótábla-fájl importálása esetén.

A bérlő egy szervezet reprezentációja, amelynek azonosítója GUID azonosítóként jelenik meg (globálisan egyedi azonosító, egy 128 bites egész szám, amely et az erőforrások azonosítására használják). Az Azure AD egy dedikált példánya, amelyet egy szervezet vagy alkalmazásfejlesztő kap, amikor a szervezet vagy az alkalmazás fejlesztője kapcsolatot hoz létre a Microsofttal, például amikor regisztrál az Azure-ra, a Microsoft Intune-ra vagy a Microsoft 365-re. Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. A bérlő ellenőrzéséhez jelentkezzen be az Azure Portalra az alkalmazás felügyeletéhez használni kívánt fiókkal. Ha rendelkezik bérlővel, automatikusan bejelentkezik, és közvetlenül a fióknév alatt láthatja a bérlő nevét. Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtár-/bérlőazonosítója (egy GUID), valamint a tartománya. Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik. A szervezet bérlői azonosítóját a tartománynév URL-címének [https://www.whatismytenantid.com](https://www.whatismytenantid.com)használatával is megkeresheti a következő címen: .

Míg az SaaS-ajánlatok bérlői azonosítók használatával definiálják a privát közönséget, más ajánlattípusok azure-előfizetési azonosítókat használhatnak (amelyek GUID-ként is vannak jelölve).

> [!NOTE]
> A privát közönség (vagy a korlátozott közönség) eltér az előnézeti közönségtől. Az **[Előnézet](#preview)** lapon megadhat egy előnézeti közönséget. Az előnézeti közönség hozzáférhet az *ajánlathoz, mielőtt* az ajánlatot élőben közzétették volna a piactéren. Bár a privát közönség megjelölése csak egy adott csomagra vonatkozik, az előnézeti közönség megtekintheti az összes csomagot (privát vagy nem), de csak a korlátozott előnézeti időszakban, amíg a terv tesztelése és érvényesítése folyamatban van.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Példa a piactéri ajánlaton belüli tervek listájára

![Példa piactéri csomag hirdetése jegyzetekkel](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Felhőszolgáltató (CSP) viszonteladói közönsége

Ha úgy dönt, hogy ajánlatát elérhetővé teszi a CSP-programban, a felhőszolgáltató lehetővé teszi, hogy a terméket egy csomagban kínált megoldás részeként értékesítsék ügyfeleiknek. További információ: [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Közzététel

Miután elvégezte az ajánlat összes szükséges szakaszát, válassza a **közzététel** lehetőséget a portál jobb felső sarkában. A bizottság átirányítja a **Véleményezés és közzététel** lapra.

### <a name="submit-offer-to-preview"></a>Ajánlat küldése előnézetbe

Ha ez az első alkalom, hogy közzéteszi ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát.
    - *Nincs elindítva* - azt jelenti, hogy a szakaszt nem érintette meg, és be kell fejezni.
    - *Hiányos* - azt jelenti, hogy a szakasz hibákat, amelyeket ki kell javítani, vagy több információt kell megadni. Vissza kell mennie a szakaszba, és frissítenie kell azt.
    - *Kész* - azt jelenti, hogy a szakasz befejeződött, az összes szükséges adatot megadta, és nincsenek hibák. Az ajánlat minden szakaszának teljes állapotban kell lennie az ajánlat elküldése előtt.
- Adjon vizsgálati utasításokat a minősítő csoportnak, hogy az alkalmazás megfelelő tesztelése mellett adjon meg olyan kiegészítő megjegyzéseket is, amelyek hasznosak az alkalmazás megértéséhez.
- Küldje el az ajánlatot közzétételre a **Küldés**lehetőség kiválasztásával. Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előnézeti verziója elérhető, hogy áttekintsd és jóváhagyd. Vissza kell térnie a Partnerközpontba, és válassza a **Go-live** lehetőséget az ajánlat közzétételéhez a nyilvánosság számára (vagy ha egy privát ajánlat, a privát közönség számára).

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
