---
title: SaaS-ajánlat, Azure Marketplace és Microsoft AppSource létrehozása
description: Az Azure piactéren elérhető, a Microsoft kereskedelmi piactér program használatával, az Azure Marketplace-en vagy a Cloud Solution Provider (CSP) programon keresztüli szolgáltatásként Microsoft AppSource nyújtott szoftveres (SaaS-) ajánlat létrehozása a Microsoft partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 08/07/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 7500eddc2416a1cb705ef207930c5fe4c6f1068a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604754"
---
# <a name="create-a-saas-offer-in-the-commercial-marketplace"></a>SaaS-ajánlat létrehozása a kereskedelmi piactéren

Ha a kereskedelmi piactéren szeretné megkezdeni a szolgáltatott szoftver-(SaaS-) ajánlatokat, először [létre kell hoznia egy partner Center-fiókot](./create-account.md) , és meg kell nyitnia a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), ahol az **Áttekintés** lapon be van jelölve.

> [!NOTE]
> Ha egy elérhető SaaS-ajánlatot hoz létre, győződjön meg arról, hogy implementálja a [SaaS-megvalósítási API](./pc-saas-fulfillment-apis.md)-kkal való integrációt.  Az API-kkal való integráció az egyetlen módszer a piactér megfelelő működéséhez. Arról is gondoskodnia kell, hogy az alkalmazás Azure AD-hitelesítést használjon egyszeri bejelentkezéssel (SSO). Tekintse [meg a kereskedelmi piactéren elérhető Azure ad-és Transact SaaS-ajánlatokat](../azure-ad-saas.md).

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **szoftver szolgáltatásként**lehetőséget.

   ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-saas.png)

> [!NOTE]
> Az ajánlat közzétételét követően a csak az ajánlat újbóli közzététele után az online áruházakban megjelenő szerkesztési lehetőségek jelennek meg a partner Centerben. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1** értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Az ajánlat aliasa nem módosítható a **Létrehozás**gombra kattintva.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-overview"></a>Ajánlat áttekintése

A **közzétételi állapot** megjeleníti az ajánlat közzétételéhez szükséges lépések vizuális megjelenítését, valamint azt, hogy az egyes lépések mennyi ideig tartanak. Hiányos közzétételi lépések ikonjai szürkén jelennek meg.

Az **ajánlat áttekintő** menüjében az ajánlaton végrehajtott műveletekre mutató hivatkozásokat tartalmazó lista szerepel. A műveletek listája az ajánlathoz választott kiválasztási alapján változik.  

- Ha az ajánlat Piszkozat törlése
- Ha az ajánlat élő leállású ajánlat eladása
- Ha az ajánlat előzetes verzióban érhető el
- Ha még nem fejezte be a közzétevő kijelentkezését – közzététel megszakítása

## <a name="offer-setup"></a>Ajánlat beállítása

Ez az oldal az alábbi információkat kéri.

- **Szeretné eladni a Microsoftot?** (Igen/Nem)
  - **Igen**, szeretnék eladni a Microsofton keresztül, és Microsoft Host-tranzakciókat használok az Ön nevében
  - **Nem**szeretném, hogy csak az ajánlatom legyen felsorolva a piactéren, és a tranzakciókat egymástól függetlenül dolgozzák fel.

### <a name="sell-through-microsoft"></a>Értékesítés a Microsofton keresztül

A Microsofton keresztüli értékesítés jobb ügyfélszolgálatot és beszerzést biztosít, lehetővé teszi a Microsoft számára, hogy az Ön nevében üzemelteti a Piactéri tranzakciókat, és kihasználja a Microsoft globálisan elérhető kereskedelmi képességei

#### <a name="saas-offer-requirements"></a>SaaS-ajánlatra vonatkozó követelmények

Ha a Service (SaaS) szolgáltatást a partner Center kereskedelmi piactérről szeretné listázni, a következő feltételeknek kell teljesülniük:

- Az ajánlatnak [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) használatát kell használnia az Identitáskezelés és a hitelesítéshez.
- Az ajánlatnak [SaaS-megvalósítási API-kat](pc-saas-fulfillment-api-v2.md) kell használnia az Azure Marketplace-szel való integrációhoz.

#### <a name="saas-pricing-and-billing-options"></a>SaaS-díjszabás és számlázási lehetőségek

A kiadó Azure-előfizetésében futó SaaS-megoldásokkal az ügyfelek által kifizetett licencek a szoftver üzembe helyezéséhez használt infrastruktúra díját is tartalmazzák. Az Azure-infrastruktúra használatának felügyelete és számlázása Önnek, a partnernek közvetlenül történik. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadóknak az Azure-infrastruktúra használati díját kell megfizetniük a szoftverlicenc-díjszabásban.

Az SaaS a mért számlázási szolgáltatással havi vagy éves számlázási támogatást biztosít, amely átalány, felhasználónkénti vagy használati díj alapján történik. A Microsoft kereskedelmi piactére egy ügynökségi modellen működik, amelynek során a kiadók díjszabást, Microsoft-számlákat és Microsoft-számlázást fizetnek a kiadónak, miközben az ügynökség díját is megtartják.

Ez egy példa a költségek és a kifizetések megoszlására az Ügynökség modelljének bemutatásához (a felsorolt árak például csak a célokat szolgálják, és nem a tényleges költségeket mutatják):

|**A licenc díja**|**$100/hó**|
|:---|:---|
|Azure-használati díj (D1/1-Core)|A számlázás közvetlenül a közzétevőre történik, nem az ügyfél|
|Az ügyfelet a Microsoft számlázza|$100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján)|

|**Microsoft-számlák**|**$100/hó**|
|:---|:---|
|A Microsoft a licencelési díj 80%-ában fizet <br>**A minősített SaaS-alkalmazások esetében a Microsoft a licencelési díj 90%-ában fizet*|$80,00/hó <br>*$* 90,00/hó *|

- Ebben a példában a Microsoft $100,00-es számlát küld az ügyfélnek a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.

> [!NOTE]
> **Csökkentett Piactéri szolgáltatási díj** – a kereskedelmi piactéren közzétett egyes SaaS-ajánlatok esetében a Microsoft a piactér szolgáltatási díját 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra. Az ajánlat (ok) minősítéséhez az ajánlat (oka) t a Microsoft Azure IP közös értékesítésű incentivized kell megjelölni. A jogosultságot minden naptári hónap végét megelőzően legalább öt (5) munkanapon belül teljesíteni kell, hogy megkapják a Marketplace szolgáltatási díját a hónapban. A csökkentett Piactéri szolgáltatási díj az Azure IP-incentivized virtuális gépekre, a felügyelt alkalmazásokra és minden más, a kereskedelmi piactéren elérhetővé tett IaaS-ajánlatra is vonatkozik.

### <a name="list-through-microsoft"></a>Listázás a Microsofton keresztül

A piactér-lista létrehozásával népszerűsítheti vállalkozását a Microsofttal. Ha csak az ajánlat listázását választja, és nem a Microsofttól, hanem azt is jelenti, hogy a Microsoft nem vesz részt közvetlenül a szoftverlicenc-tranzakciókban. Nincs hozzárendelt tranzakciós díj, és a közzétevő megtartja az ügyféltől begyűjtött licencelési díjak 100%-át. A közzétevő azonban felelős a szoftverlicenc-tranzakció valamennyi aspektusának támogatásában, beleértve a rendelés teljesítését, a mérést, a számlázást, a számlázást, a fizetést és a gyűjtést.

#### <a name="get-it-now-free"></a>Letöltés (ingyenes)

Ajánlatát ingyenesen listázhatja az ügyfelek számára, ha érvényes ( *http* -vagy *https*-alapú) címeket ad meg, ahol az [Azure Active Directory (Azure ad) használatával egy kattintással](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)elvégezheti a próbaverziót. Például: `https://contoso.com/saas-app`.

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)

Az ingyenes próbaverzióra mutató hivatkozást tartalmazó ajánlat listázása egy érvényes címen (a *http* vagy a *https*verziótól kezdődően), ahol az [Azure Active Directory (Azure ad) használatával egyetlen kattintással](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)elvégezheti a próbaverziót. Például: `https://contoso.com/trial/saas-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak a felhasználói adatok Azure AD-n keresztüli beszerzéséhez használhatók, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok (MSA-EK) nem támogatottak a jogkivonat használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolatfelvétel

Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [ügyfél-érdeklődők](#customer-leads).

#### <a name="example-marketplace-offer-listing"></a>Példa a piactéri hirdetésekre

Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban:

:::image type="content" source="media/example-appsource-saas.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Nagyméretű embléma
2. Kategóriák
3. Iparágak
4. Támogatási címe (hivatkozás)
5. Használati feltételek
6. Adatvédelmi irányelvek
7. Ajánlat neve
8. Összefoglalás
9. Leírás
10. Képernyőképek/videók
11. Dokumentumok

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Azure Portalban:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Bemutatja, hogyan jelennek meg az ajánlat a Azure Portalban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Cím
2. Leírás
3. Hasznos hivatkozások
4. Képernyőképek

## <a name="enable-a-test-drive"></a>Tesztelési meghajtó engedélyezése

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt kipróbálhatja a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról](../what-is-test-drive.md).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

További információ: az [ajánlat tesztelése a kereskedelmi piactéren](test-drive.md).

### <a name="test-drive-resources"></a>A meghajtó erőforrásainak tesztelése

- [Mit jelent a tesztverzió?](../what-is-test-drive.md)
- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

### <a name="customer-leads"></a>Ügyfél-érdeklődők

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>További érdeklődői felügyeleti erőforrások
- [Érdeklődői felügyelet – gyakori kérdések](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [Gyakori vezető konfigurációs hibák](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [Az érdeklődők felügyelete – Áttekintés egy pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen az oldalon megtekintheti az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az ajánlatát támogató jogi szerződéseket és az alkalmazás verzióját.

### <a name="category"></a>Kategória

Ajánlata a AppSource vagy az Azure Marketplace-en lesz közzétéve, az ajánlathoz kapcsolódó tranzakciós képességek és a kategória kiválasztása alapján. További részletekért lásd: [Microsoft AppSource és az Azure Marketplace összehasonlítása](../comparing-appsource-azure-marketplace.md) . Válassza ki az ajánlathoz legjobban illeszkedő kategóriákat és alkategóriákat, valamint a célközönséget. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható**lehetőséget.

Tekintse meg az [ajánlott eljárásokat felsoroló ajánlatban](../gtm-offer-listing-best-practices.md)az egyes online áruházakhoz tartozó kategóriák és alkategóriák teljes listáját.

### <a name="industries"></a>Iparágak

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

Az iparág kiválasztása csak a AppSource közzétett ajánlatokra vonatkozik.

### <a name="app-version"></a>Az alkalmazás verziója

Ez a mező nem kötelező, és a AppSource piactéren az ajánlat verziószámának azonosítására szolgál.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard szintű szerződés a Microsoft kereskedelmi piactérről

A Microsoft szabványos szerződéssablon-sablont biztosít.

- **Szabványos szerződést használ a Microsoft kereskedelmi piactérről?**

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést biztosít a Microsoft kereskedelmi piactérről, amely megkönnyíti a tranzakciók megkönnyítése a piactéren. Az egyéni használati feltételek és kikötések elvégzése helyett a kereskedelmi piactér-közzétevők dönthetnek úgy, hogy a standard szintű szerződés keretében kínálják a szoftvereket, amelyeket csak egyszer kell bemutatni és elfogadni. A standard szintű szerződés a következő címen érhető el: https://go.microsoft.com/fwlink/?linkid=2041178 .

A normál szerződést úgy is kiválaszthatja, hogy a saját használati feltételeinek megadása helyett a "szabványos szerződés használata a kereskedelmi piactérhez" jelölőnégyzetet válassza.

![A standard szerződés használata jelölőnégyzet](./media/use-standard-contract.png)

> [!NOTE]
> Miután közzétette az ajánlatot a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződéssel, nem használhatja saját használati feltételeit és kikötéseit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés **vagy** a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szerződés feltételeit, ezt a standard szintű szerződés módosításain keresztül teheti meg.

#### <a name="standard-contract-amendments"></a>Standard szintű szerződés módosításai

A standard szintű szerződések módosításai lehetővé teszik a kiadók számára, hogy a szokásos szerződési feltételeket használják az egyszerűség kedvéért, és testre szabják a termék vagy a vállalat használati Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a Microsoft standard szerződést.

A kereskedelmi piactér-közzétevők számára két fajta módosítás érhető el:

- Univerzális módosítások: ezeket a módosításokat a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az univerzális módosítások az ajánlat minden ügyfelének a vásárlás folyamatában jelennek meg. Az ügyfeleknek el kell fogadniuk a standard szerződés és a módosítás feltételeit, mielőtt felhasználhatják az ajánlatot.
- Egyéni módosítások: ezek a módosítások a standard szerződés speciális módosításai, amelyek csak az Azure-bérlői azonosítók használatával vannak megcélozva az egyes ügyfelekre. A kiadók kiválaszthatják, hogy melyik bérlőt szeretnék megcélozni. Az ajánlat vásárlási folyamatában csak a bérlő ügyfelei jelennek meg az egyéni módosítási feltételekkel.  Az ügyfeleknek el kell fogadniuk a standard szerződés feltételeit és a módosítás (oka) t, mielőtt felhasználhatják az ajánlatot.

>[!NOTE]
> Ez a két típusú módosítás egymásra épül. Az egyéni módosításokkal rendelkező ügyfelek a vásárlás során általános módosítást is kapnak a standard szerződéshez.

**Általános módosítási feltételek a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződéshez** – ebben a mezőben adja meg az univerzális módosítási feltételeket. Ajánlathoz egyetlen általános módosítást is megadhat. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy a Azure Portal a felderítési és vásárlási folyamat során az ügyfelek számára jelennek meg.

**Egyéni módosítási feltételek a Microsoft kereskedelmi Marketplace standard szerződéséhez** – először válassza az **Egyéni módosítási feltételek hozzáadása**lehetőséget. Ajánlatunk legfeljebb 10 egyéni módosítási feltételt adhat meg.

- **Egyéni módosítási feltételek** – adja meg az egyéni módosítási feltételeket az egyéni módosítási feltételek mezőben. Ebben a mezőben korlátlan számú karaktert adhat meg. Csak az egyéni feltételekhez megadott bérlői azonosítók ügyfelei jelennek meg az ajánlat vásárlási folyamatában az Azure Portalban.  
- **Bérlői azonosítók** (kötelező) – minden egyéni módosítás legfeljebb 20 bérlői azonosítót célozhat meg. Ha egyéni módosítást ad hozzá, meg kell adnia legalább egy bérlői azonosítót. A bérlő azonosítója azonosítja az ügyfelet az Azure-ban. Megkérheti az ügyfelet erre az AZONOSÍTÓra, és megkeresheti a portal.azure.com > Azure Active Directory > a Tulajdonságok lehetőségre kattintva. A címtár-azonosító értéke a bérlő azonosítója (például 50c464d3-4930-494c-963c-1e951d15360e). A szervezet bérlői AZONOSÍTÓját is megkeresheti a saját tartománynév URL-címével, [Mi az a Microsoft Azure és az Office 365-bérlő azonosítója?](https://www.whatismytenantid.com)
- **Leírás** (nem kötelező) – opcionálisan megadhatja a bérlői azonosító rövid leírását, amely segít azonosítani a módosítással megcélzott ügyfelet.

#### <a name="terms-and-conditions"></a>használati feltételei

Ha meg szeretné adni saját használati feltételeit, megadhatja őket a feltételek és kikötések mezőben. Ebben a mezőben legfeljebb 10 000 karakter hosszúságú szöveget adhat meg. Ha a feltételek és kikötések további leírást igényelnek, adjon meg egy URL-hivatkozást ebbe a mezőbe, ahol a feltételek és kikötések megtalálhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Ezen a lapon láthatók azok a nyelvek (és piacok), ahol az ajánlat elérhető, jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, keresési kifejezéseit stb.) az egyes nyelvekhez/piacokhoz.

> [!NOTE]
> A tartalom listázása (például az ajánlat leírása, a dokumentumok, a képernyőképek, a használati feltételek és az adatvédelmi szabályzatok) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

### <a name="offer-listings"></a>Ajánlatok listázása

Adja meg a piactéren megjelenítendő adatokat, beleértve az ajánlat és a marketing-eszközök leírását.

- **Név** (kötelező) – az itt definiált név jelenik meg az ajánlatnak az Ön által választott piactéren (ko) szereplő címében. A név előre fel van töltve az előző **új ajánlat** bejegyzése alapján. A név lehet védjeggyel ellátott. Nem tartalmazhat hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie.
- **Összefoglalás** (kötelező) – adja meg az ajánlat rövid leírását, amelyet a Piactéri lista (ek) keresési eredményeiben kíván használni. Ebben a mezőben legfeljebb 100 karaktert lehet megadni.
- **Leírás** (kötelező) – adja meg a piactér-lista (ek) áttekintésében megjelenítendő ajánlat leírását. Érdemes figyelembe venni az érték kiosztását, a főbb előnyöket, a kategória-vagy iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket, a szükséges közzétételeket, valamint egy hivatkozást, amely további információkat tartalmaz. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni, beleértve a jelölést is. További tippeket a [nagyszerű alkalmazás leírásának írása](/windows/uwp/publish/write-a-great-app-description)című témakörben talál.
- **Kulcsszavak keresése** – adjon meg legfeljebb három olyan keresési kulcsszót, amelyet az ügyfelek az ajánlat megtalálására használhatnak a piactéren.
- **Első lépéseket ismertető utasítások** (kötelező) – magyarázza el, hogyan konfigurálhatja és indíthatja el az alkalmazás használatát a potenciális ügyfelek számára.  Ez a rövid útmutató a részletesebb online dokumentációra mutató hivatkozásokat is tartalmaz. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni.

#### <a name="description"></a>Leírás

A mező kitöltése kötelező.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>Hivatkozások

- **Adatvédelmi szabályzat** (kötelező) – a szervezet adatvédelmi szabályzatára mutató hivatkozás. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson
- **CSP program marketing anyagok** (nem kötelező) – adjon meg egy hivatkozást a marketing-anyagokra, ha úgy dönt, hogy kiterjeszti az ajánlatot a [Cloud Solution Provider (CSP)](../cloud-solution-providers.md) programra. A CSP kiterjesztheti ajánlatát a minősített ügyfelek szélesebb körére azáltal, hogy lehetővé teszi a CSP-partnerek számára, hogy csomagot, piacot és viszonteladást nyújtsanak. Ezeknek a viszonteladóknak hozzá kell férniük az anyagokhoz az ajánlat marketingje érdekében. További információ: piacra jutási [szolgáltatások](https://partner.microsoft.com/reach-customers/gtm).
- **Hasznos hivatkozások** (nem kötelező) – opcionális kiegészítő online dokumentumok az alkalmazással vagy a felsorolt kapcsolódó szolgáltatásokkal kapcsolatban a cím és az **URL-** **cím** megadásával. További hasznos hivatkozások hozzáadásához kattintson az  **+ URL-cím hozzáadása**lehetőségre.

#### <a name="contact-information"></a>Kapcsolattartási adatok

- **Névjegyek** – minden ügyfél-kapcsolattartóhoz adja meg az alkalmazott  **nevét**,  **telefonszámát**és **e-mail-** címét (ezeket a *rendszer nem* jeleníti meg nyilvánosan). Támogatási **URL-cím**  szükséges a **támogatási kapcsolattartó** csoport számára (ez nyilvánosan látható *lesz* ).

  - **Támogatási kapcsolattartó** (kötelező) – általános támogatási kérdésekre.
  - **Engineering Contact** (kötelező) – technikai kérdésekben.
  - **Channel Manager-kapcsolat** (kötelező) – a CSP-programhoz kapcsolódó viszonteladói kérdésekért.

#### <a name="files-and-images"></a>Fájlok és lemezképek

- **Dokumentumok** (kötelező) – kapcsolódó marketing-dokumentumok hozzáadása az ajánlathoz, PDF formátumban, legalább egy és legfeljebb három dokumentumhoz.
- **Képek**  – emblémaként szolgáló képek feltöltése PNG formátumban:

  - **Kicsi** (48 x 48 képpont, kötelező)
  - **Közepes** (90 x 90 képpont, kötelező)
  - **Nagyméretű** (216 x 216 képpont, kötelező)
  - **Széles** (255 x 115 képpont) (nem kötelező)

   Ezt a három embléma-méretet az online áruházak különböző helyein használják:

   -   A kis embléma (48 x 48 képpont) megjelenik az Azure piactér keresési eredményei között, valamint a Microsoft AppSource főoldalon és a keresési eredmények oldalon. 
   -   A közepes embléma (90 x 90 képpont) akkor jelenik meg, amikor új erőforrást hoz létre Microsoft Azureban.
   -   A nagyméretű embléma (216 x 216 és 350 x 350 képpont között) megjelenik az ajánlati lista lapján az Azure Marketplace-en és a Microsoft AppSourceban.

- **Képernyőképek** (kötelező) – adjon meg legfeljebb öt képernyőképet, amely az ajánlatát mutatja be, a mérete 1280 x 720 képpont. Az összes rendszerképnek a-ben kell lennie. PNG-formátum.
- **Videók** (nem kötelező) – az ajánlatot bemutató videókra mutató hivatkozásokat adhat hozzá. A YouTube-és/vagy a Vimeo-videókra mutató hivatkozásokat is használhat, amelyek az Ön ajánlatával együtt jelennek meg az ügyfelek számára. Emellett meg kell adnia a videó miniatűr képét is, amely az 1280 x 720 képpont méretű, PNG formátumú. Ajánlat legfeljebb négy videót jeleníthet meg.

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](../gtm-offer-listing-best-practices.md)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="preview-audience"></a>Előnézet célközönsége

Ezen a lapon megadhatja, hogy az ajánlat közzétételét megelőzően egy korlátozott **előzetes** verziót hozzon létre a piactéren elérhető szélesebb közönség számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után válassza az **élő** verzió lehetőséget, hogy ajánlatát élőben is közzé lehessen tenni a piactér nyilvános célközönsége számára.

Adjon hozzá egy HRE/MSA-fiókhoz tartozó e-mailt soronként, valamint egy opcionális leírást.

Akár 10 e-mail-címet is hozzáadhat manuálisan, vagy 20 ha egy CSV-fájlt tölt fel, a meglévő Microsoft-fiók (MSA) vagy a Azure Active Directory-fiókok esetében, amelyek segítséget nyújtanak az ajánlat érvényesítéséhez az élő közzététel előtt. Ezeknek a fiókoknak a hozzáadásával olyan célközönséget határozhat meg, amely előzetesen elérhetővé válik az ajánlat számára, mielőtt közzéteszi őket a piactéren. Ha az ajánlat már élő, akkor is megadhatja az előnézeti közönséget, hogy tesztelje az ajánlat módosításait és frissítéseit.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége a piactéren való élő közzététel _előtt_ jogosult az ajánlathoz való hozzáférésre. Dönthet úgy is, hogy létrehoz egy csomagot, és csak a privát célközönség számára teszi elérhetővé. A **csomag listázása** lapon megadhat egy privát célközönséget, amely az **Ez egy privát csomag** jelölőnégyzet. Az Azure-bérlői azonosítók használatával legfeljebb 20 000 ügyfelet adhat meg.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **technikai konfiguráció** lap a piactér által az SaaS-szolgáltatással való kommunikációhoz használt technikai részleteket határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezze és felügyelje azt.

>[!NOTE]
>A részletek az ajánlat részleteiben való konfigurálása előtt meg kell valósítania a [SaaS-megvalósítás API](./pc-saas-fulfillment-api-v2.md) -kkal való integrációját. Létre kell hoznia egy kezdőlapot is, és az alkalmazásnak az Azure AD-hitelesítést kell használnia egyszeri bejelentkezéssel (SSO). További információ: [Az Azure ad és a transactd SaaS-ajánlatok a kereskedelmi piactéren](../azure-ad-saas.md).

Az összegyűjtött mezők használatát leíró diagramok és részletes magyarázatok az [API](./pc-saas-fulfillment-api-v2.md)-k dokumentációjában találhatók.

- Kezdőlap **URL-címe** (kötelező) – határozza meg a SaaS-webhely URL-címét (például: `https://contoso.com/signup` ), hogy a végfelhasználók az ajánlatot a piactéren szerezték be, és a konfigurációs folyamatot az újonnan létrehozott SaaS-előfizetésből aktiválják.  Ezt az URL-címet a piactér-vásárlási azonosító jogkivonat paraméterrel hívja meg a rendszer, amely egyedileg azonosítja az adott végfelhasználó SaaS-vásárlását.  Ezt a tokent a megfelelő SaaS-előfizetés részleteire kell cserélnie az API [feloldása](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) paranccsal.  Ezeket a részleteket és minden más összegyűjteni kívánt ügyfelet az ügyfél-interaktív weblap részeként kell használni, amely a végfelhasználói regisztráció befejezéséhez és a vásárlás aktiválásához szükséges.  Ezen az oldalon a felhasználónak az Azure Active Directory (Azure AD) használatával egy kattintással történő hitelesítéssel kell regisztrálnia. <br> <br> Ez az URL-cím a piactér-beli vásárlási azonosító jogkivonat paraméterrel is meghívható, ha a végfelhasználó a felügyelt SaaS-élményt Azure Portal vagy M365 felügyeleti központból indítja el. Mindkét folyamatot kezelni kell, ha a jogkivonatot az új ügyfelek megvásárlása után első alkalommal adták meg, és ha az SaaS-t kezelő meglévő ügyfél számára van megadva. <br> <br> Az itt konfigurált kezdőlapon a 24/7-es verzióját kell futtatnia. Ez az egyetlen módszer, amikor értesítést kap a SaaS-ajánlatok piactéren végzett új vásárlásáról, vagy egy ajánlat aktív előfizetésének konfigurációs kéréseiről.

- **Kapcsolat webhook** (kötelező) – minden olyan aszinkron esemény esetében, amelyet a Microsoftnak el kell küldenie Önnek (például az SaaS-előfizetés meg lett szakítva), meg kell adnia egy kapcsolat webhook URL-címét. Meghívjuk ezt az URL-címet, hogy értesítse Önt az eseményről. <br> <br> Az Ön által megadott webhooknak a 24/7-as verzióját kell futtatnia, mivel ez az egyetlen módja, ha értesítést szeretne kapni az ügyfelek által a piactéren vásárolt SaaS-előfizetésekről.  Ha még nem rendelkezik webhook-rendszerrel, a legegyszerűbb konfiguráció egy olyan HTTP-végpont logikai alkalmazás, amely figyeli a neki küldött összes eseményt, majd megfelelően kezeli őket (például: `https://prod-1westus.logic.azure.com:443/work` ). További információk: [munkafolyamatok hívása, elindítása vagy beágyazása http-végpontokkal a Logic Appsben](../../logic-apps/logic-apps-http-endpoint.md).

- **Azure ad-bérlő azonosítója** (kötelező) – a Azure Portalon belül [létre kell hoznia egy Azure Active Directory (ad-) alkalmazást](../../active-directory/develop/howto-create-service-principal-portal.md) , hogy a két szolgáltatás közötti kapcsolat ellenőrizhető legyen egy hitelesített kommunikáció mögött. A [bérlő azonosítójának](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)megkereséséhez nyissa meg a Azure Active Directory, és válassza a **Tulajdonságok**lehetőséget, és keresse meg a felsorolt **címtár-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás azonosítója** (kötelező) – az alkalmazás- [azonosítóra](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)is szüksége lesz. Az érték beszerzéséhez lépjen a Azure Active Directory, és válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazás-azonosító** számát (például: `50c464d3-4930-494c-963c-1e951d15360e` ).

>[!NOTE]
>Az Azure AD-alkalmazás azonosítója a partner Center-fiókban található közzétevői AZONOSÍTÓhoz van társítva.  Győződjön meg arról, hogy az összes ajánlatban ugyanaz az alkalmazás-azonosító van használatban.

>[!NOTE]
>Ha a közzétevő két vagy több különböző fiókkal rendelkezik a partner Centerben, akkor az egyik fiókhoz két vagy több különböző Azure AD-alkalmazás-azonosítót kell használni. A fiókpartner minden fiókpartner-fiókjának egyedi Azure AD-alkalmazás-azonosítót kell használnia a fiókon keresztül közzétett SaaS-ajánlatok esetében.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="plan-overview"></a>A terv áttekintése

Ezen az oldalon számos különböző terv közül választhat az adott ajánlaton belül. Ezek a csomagok (más néven SKU-ként) eltérőek lehetnek a verzió, a monetizálása vagy a szolgáltatási szintek tekintetében. Be kell állítania legalább egy csomagot, hogy eladja ajánlatát a piactéren.

Az egyes ajánlatokhoz legfeljebb 100 csomagot hozhat létre: ezek közül legfeljebb 45 lehet privát. További információ a [Microsoft kereskedelmi piactéren](../private-offers.md)elérhető privát csomagokról.

A létrehozást követően megtekintheti a csomag neveit, azonosítóit, díjszabási modelljeit, rendelkezésre állását (nyilvános vagy privát), az aktuális közzétételi állapotot és az összes elérhető műveletet.

A **terv áttekintésében** elérhető **műveletek** a csomag aktuális állapotától függően változnak, és a következők lehetnek:

- Ha a terv állapota **Piszkozat** -delete Piszkozat
- Ha a terv állapota **Live** -stop eladási terv vagy privát célközönség szinkronizálása

**Új csomag létrehozása** (legalább egy csomag a Microsofton keresztüli értékesítésre kiválasztott felhasználók számára)

- **Csomag azonosítója:** Hozzon létre egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjának. Ez az azonosító látható lesz az ügyfelek számára a termék URL-címében és Azure Resource Manager-sablonokban (ha vannak ilyenek). Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon. Ehhez a csomag-AZONOSÍTÓhoz legfeljebb 50 karakter adható meg. Az azonosító nem módosítható a létrehozás kiválasztása után.
- **Csomag neve:** Az ügyfelek ezt a nevet fogják látni, amikor azt döntik el, hogy melyik tervet kell kiválasztani az ajánlaton belül. Hozzon létre egyedi nevet az ajánlat minden csomagjának. A terv neve az adott ajánlat részét képező szoftvercsomagok megkülönböztetésére szolgál (például az ajánlat neve: Windows Server; csomagok: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Csomag listázása

Ezen a lapon megadhatja a csomag nevét és leírását. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **Név** – előre feltöltve az előzetes verziójú **új díjcsomag** alapján, és a piactéren megjelenő "szoftvercsomag" címet fogja látni.
- **Leírás** – ez a leírás egy olyan lehetőség, amely ismerteti, hogy mi teszi ezt a szoftvercsomagot egyedivé, és hogy az Ön által kínált egyéb szoftvercsomagok milyen eltéréseket biztosítanak. Legfeljebb 500 karaktert tartalmazhat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

#### <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Ezen a lapon konfigurálhatja azokat a piacokat, amelyekre a csomag elérhető lesz, a kívánt bevételi modellt, árat és számlázási időszakot. Emellett azt is megadhatja, hogy a terv mindenki számára elérhető legyen-e, vagy csak bizonyos ügyfeleknek (privát célközönségnek).

#### <a name="markets-optional"></a>Piacok (nem kötelező)

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Válassza a **piacok szerkesztése** lehetőséget, és jelölje be bármely olyan piaci helyhez tartozó jelölőnégyzetet, ahol elérhetővé szeretné tenni ezt a csomagot. Ez az oldal egy keresőmezőt és egy olyan lehetőséget tartalmaz, amellyel kiválaszthatja a "beérkező adók" országokat/régiókat, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és használati adót.

Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt tekintse át az egyes piacok árát. Tekintse meg a díjszabást a módosítások mentése után a "export Prices (xlsx)" hivatkozás használatával.

A folytatás előtt válassza a **Mentés** lehetőséget.

#### <a name="pricing"></a>Díjszabás

##### <a name="pricing-model"></a>Díjszabási modell

**Átalány** – az ajánlathoz való hozzáférés engedélyezése egyetlen havi vagy éves díjszabási díjszabással. Ezt más néven a hely alapú díjszabásnak is nevezzük. Ezzel az árképzési modellel meghatározhat olyan mért csomagokat is, amelyek a Piactéri mérési szolgáltatás API-ját használják, hogy a nem standard egységek alapján felszámolják az ügyfeleket.  A mért számlázással kapcsolatos további információkért lásd: [mért számlázás a Marketplace-mérési szolgáltatás használatával](./saas-metered-billing.md).  Ezt a beállítást akkor is érdemes használni, ha a használati viselkedés a SaaS-szolgáltatáshoz kapcsolódóan működik.  Az ügyfélnek nem ajánlott napi vagy óránkénti váltást végeznie.

**Felhasználónkénti** – lehetővé teszi, hogy az ajánlathoz való hozzáférést az ajánlatot vagy az elfoglaló munkaállomásokat elérő felhasználók száma alapján biztosítsa. Ez a felhasználó-alapú modell lehetővé teszi, hogy beállítsa az árak alapján engedélyezett felhasználók minimális és maximális számát. Így a különböző díjszabási pontok a felhasználók számától függően konfigurálhatók több csomag konfigurálásával.  Ezeket a mezőket nem kötelező megadni. Ha nem jelöli be a jelölőnégyzetet, a rendszer a felhasználók számát úgy értelmezi, hogy nem rendelkezik korláttal (az 1. és a maximális érték legfeljebb annyit tud támogatni). Ezeket a mezőket a csomag frissítésének részeként lehet szerkeszteni.

A közzétételt követően a számlázási díjszabási modell választása nem módosítható. Emellett az ugyanarra az ajánlatra vonatkozó összes csomagnak ugyanazzal az árképzési modellel kell rendelkeznie.

##### <a name="user-limits"></a>Felhasználókra vonatkozó korlátozások

Ha alkalmazható, válassza ki és állítsa be a minimális és a maximális felhasználói korlátot.

##### <a name="billing-term-and-price"></a>Számlázási időszak és ár

Válassza ki azt a **kifejezést** és **árat** , amelyre az ügyfeleknek meg kell fizetniük a felsorolt árakat. Legalább egy havi vagy éves árat meg kell adni, vagy mindkét lehetőséget elérhetővé kell tenni az ügyfelek számára.

Az USD-ben beállított árak (USD = Egyesült Államok dollár) az összes kiválasztott piac helyi pénznemére lesznek átalakítva, az aktuális árfolyamok alapján, a mentéskor. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot. A jelen díjszabás és a saját beállítások ellenőrzése a felelős.
*\*Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását.*

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások vonatkoznak a csomag közzététele után megjelenő változásokra:

- A csomag közzétételekor a díjszabási modell nem módosítható.
- Miután közzétett egy számlázási időszakot egy csomaghoz, később nem távolítható el.
- Miután közzétette a csomag egy piacának árát, később nem módosítható.

#### <a name="free-trial"></a>Ingyenes próba

A kereskedelmi Piactéren keresztül elérhető SaaS-ajánlatok egy hónapos ingyenes próbaverziót biztosítanak a Microsofton keresztüli értékesítéshez. Az összes számlázási modellhez és használati feltételhez a mért csomagok kivételével az ingyenes próbaverziók támogatottak. Ez a beállítás lehetővé teszi, hogy az ügyfelek egy hónapon keresztül alacsony korláttal rendelkezzenek a belépéshez.  Ha úgy dönt, hogy engedélyezi az ajánlaton belüli csomagok ingyenes próbaverzióját, az ügyfél nem fog tudni áttérni fizetős előfizetésre a kezdeti egy hónapos időszak lejárta előtt.  Ebben az időszakban az ajánlatát vásárló ügyfelek kipróbálhatja a támogatott csomagokat, amelyeken engedélyezve van az ingyenes próbaverzió, és átválthat közöttük.  A díjköteles előfizetésre való áttérés a kifejezés végén automatikusan történik.

>[!NOTE]
>Ha az ügyfél úgy dönt, hogy ingyenes próbaverziók nélkül szeretne átalakítani egy csomagra, az átalakítás megtörténik, de az ingyenes próbaverzió azonnal elvész. Azt is megteheti, hogy ha egy ügyfél megkezdi a csomag megfizetését, már nem kaphat ingyenes próbaidőszakot ugyanarra az előfizetésre, még akkor is, ha az ingyenes próbaverziókat támogató SKU-ra vált.

Az ajánlat minden egyes csomagjának ingyenes próbaverzióját itt állíthatja be. Jelölje be a jelölőnégyzetet egy hónapos próbaverzió engedélyezéséhez.

![Egy hónapos ingyenes próbaverzió jelölőnégyzet](./media/free-trial-enable.png)

>[!NOTE]
>Ha a visszagörgethető ajánlat ingyenes próbaverzióval lett közzétéve, az adott csomag esetében nem tiltható le. Győződjön meg arról, hogy ez a beállítás megfelelő az első közzétételhez, hogy ne kelljen újból létrehoznia a csomagot.

Az ingyenes próbaverzióban jelenleg részt vevő ügyfél-előfizetésekkel kapcsolatos információk beszerzéséhez használja az új API-tulajdonságot `isFreeTrial` , amely igaz vagy hamis értékként lesz megjelölve. További információ: [SaaS Get előfizetés API](pc-saas-fulfillment-api-v2.md#get-subscription).

>[!NOTE]
>Az ingyenes próbaverziók nem támogatottak a piactér-mérési szolgáltatást használó csomagok esetében.

#### <a name="plan-visibility"></a>Terv láthatósága

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Az Azure AD-bérlői azonosítók használatával a korlátozott célközönséghez is hozzárendelhet tagságot.

##### <a name="privacy"></a>Adatvédelem

Válassza **ezt a csomagot** , hogy a terv magánjellegű legyen, és csak az Ön által választott korlátozott közönség számára legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatóvá kell tenni (nem konfigurálható újra privát csomagként).

##### <a name="restricted-audience-tenant-ids"></a>**Korlátozott célközönség (bérlői azonosítók)**

Rendelje hozzá azt a célközönséget, amely hozzáfér ehhez a privát csomaghoz. A hozzáférés hozzá van rendelve a bérlői azonosítók használatával, hogy tartalmazza a hozzájuk rendelt összes bérlői azonosító leírását. . Csv számolótábla-fájl importálásakor legfeljebb 10 bérlői azonosító adható hozzá, vagy 20 000 ügyfél bérlői azonosítóját.

A bérlő egy szervezet képviselete, amelynek azonosítója GUID (globálisan egyedi azonosító, az erőforrások azonosítására szolgáló 128 bites egész szám). Ez az Azure AD dedikált példánya, amelyet a szervezet vagy az alkalmazás fejlesztője akkor kap, ha a szervezet vagy az alkalmazás fejlesztője kapcsolatot létesít a Microsofttal, például az Azure-ra való regisztráláskor, Microsoft Intune vagy Microsoft 365. Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. A bérlő ellenőrzéséhez jelentkezzen be az Azure Portalra az alkalmazás felügyeletéhez használni kívánt fiókkal. Ha rendelkezik bérlővel, automatikusan bejelentkezik, és közvetlenül a fióknév alatt láthatja a bérlő nevét. Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtár-/bérlőazonosítója (egy GUID), valamint a tartománya. Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik. A szervezet bérlői AZONOSÍTÓját a (z) tartománynév URL-címének használatával is megkeresheti [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

Míg a SaaS a bérlői azonosítókat használja a privát célközönség definiálásához, az egyéb ajánlati típusok az Azure-előfizetési azonosítókat (amelyek GUID-ként is jelölik) használhatják.

> [!NOTE]
> A privát célközönség (vagy a korlátozott célközönség) eltér az előzetes verzió célközönségtől. Az **[előnézet](#preview-audience)** lapon megadhatja az előnézeti célközönséget. Az előzetes verzió célközönsége a piactéren élőben közzétett ajánlat *előtt* jogosult az ajánlathoz való hozzáférésre. Bár a magánjellegű célközönség megjelölése csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (privát vagy nem) is megtekintheti, de csak a korlátozott előzetes verzióban, a terv tesztelése és ellenőrzése közben.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Példa a piactéren belüli csomagok listájára

:::image type="content" source="media/marketplace-plan.png" alt-text="Példa a Marketplace-tervre a megjegyzések listájával.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Csomag neve
2. Csomag leírása

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Cloud Solution Provider (CSP) viszonteladói közönség

Ha azt szeretné, hogy az ajánlata elérhető legyen a CSP programban, a Cloud Solution Providers lehetővé teszi, hogy a terméket egy csomagban lévő megoldás részeként értékesítse az ügyfeleiknek. További információ: [felhőalapú megoldás-szolgáltatók](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Közzététel

Miután elvégezte az ajánlat összes szükséges részét, válassza a **felülvizsgálat és közzététel** elemet a portál jobb felső sarkában.

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
  - **Nincs elindítva** – a szakasz nem lett megérintve, és el kell végezni.
  - **Hiányos** – a szakasznak olyan hibákkal kell rendelkeznie, amelyeket meg kell oldani, vagy további információkat kell megadnia. Vissza kell térnie a szakaszhoz, és frissítenie kell.
  - **Complete (Befejezés** ) – a szakasz elkészült, az összes szükséges adattal megadták, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- Adjon meg tesztelési utasításokat a minősítési csapatnak, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez hasznos kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget, ha közzé szeretné tenni az ajánlatát a nyilvános (vagy ha egy privát ajánlat, a privát célközönség) számára.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
