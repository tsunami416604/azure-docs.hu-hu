---
title: Új Dynamics 365 for Operations ajánlat létrehozása a kereskedelmi piactéren
description: Új Dynamics 365 for Operations ajánlat létrehozása az Azure Marketplace-en, a AppSource-on vagy a Cloud Solution Provider (CSP) programon keresztül a Microsoft partner Center kereskedelmi piactér portálján.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 1ba7fca7576c6f015ad7bbcb47909b90c3065d58
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045957"
---
# <a name="create-a-new-dynamics-365-for-operations-offer"></a>Hozzon létre egy új Dynamics 365 for Operations ajánlatot

Ez a témakör bemutatja, hogyan hozhat létre új Dynamics 365 for Operations ajánlatot. [A Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) egy vállalati erőforrás-tervezési (ERP) szolgáltatás, amely támogatja a fejlett pénzügyi, üzemeltetési, gyártási és ellátási láncok kezelését. A Dynamics 365 for Operations összes ajánlatának a minősítési folyamaton keresztül kell haladnia.

A Dynamics 365 for Operations-ajánlatok létrehozásának megkezdéséhez először [létre kell hoznia egy partner Center-fiókot](./create-account.md) , és meg kell nyitnia a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), a kiválasztott **Áttekintés** oldalon.

![Kereskedelmi piactér irányítópultja a partner Centerben](./media/new-offer-overview.png)

>[!Note]
> Ha egy ajánlat közzé lett téve, a partner Centerben végzett ajánlat szerkesztése csak a rendszeren történik meg, és az újbóli közzététel után tárolja az előfizetéseket. Győződjön meg arról, hogy a módosítások elvégzése után elküldi az ajánlatot közzétételre.


## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Válassza az **+ új ajánlat** gombot, majd válassza ki a **Dynamics 365 for Operations** menüpontot. Ekkor megjelenik az **új ajánlat** párbeszédpanel.

### <a name="offer-id-and-alias"></a>Ajánlat azonosítója és alias

- **Ajánlat azonosítója**: egyedi azonosító a fiókban található összes ajánlathoz. Ez az azonosító látható lesz az ügyfelek számára a Piactéri ajánlat URL-címében, és Azure Resource Manager sablonokat (ha vannak ilyenek). Az ajánlat AZONOSÍTÓjának kisbetűs alfanumerikus karakternek kell lennie (beleértve a kötőjeleket és az aláhúzásokat, de nincs szóköz). Ez az azonosító legfeljebb 50 karakterből állhat, és a **Létrehozás**gombra kattintva nem módosítható.  Ha például a *test-Offer-1* értéket adja meg, az ajánlat URL-címe `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`lesz.

- **Ajánlat aliasa**: az ajánlatnak a partner Centerben való hivatkozásához használt név. Ez a név nem lesz használatban a piactéren, és nem egyezik meg az ajánlat nevével és más értékekkel, amelyek megjelennek az ügyfeleknek. Ez az érték nem módosítható a **Létrehozás**gombra kattintva.

Miután megtörtént az **ajánlat-azonosító** és az **ajánlati alias**megadása, válassza a **Létrehozás**lehetőséget. Ezt követően az ajánlat különböző részein dolgozhat.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **ajánlat beállítása** lap a következő információkat kéri. A mezők kitöltése után válassza a **Mentés** lehetőséget.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hogyan kívánja használni a potenciális ügyfeleket a jelen tőzsdei ajánlathoz?

Válassza ki az ajánlathoz használni kívánt beállítást.

#### <a name="get-it-now-free"></a>Letöltés (ingyenes)

Az ajánlatát ingyenesen listázhatja, ha érvényes URL-címet ad meg (a *http* vagy a *https*-től kezdve), ahol elérheti az alkalmazást.  Például:`https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)

Az ajánlat az ingyenes próbaverzióra mutató hivatkozással listázhatja az ügyfeleket, ha egy érvényes URL-címet (a *http* -t vagy a *https*-t) biztosít, ahol próbaverziót kaphat.  Például: `https://contoso.com/trial/my-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak Azure Active Directory (Azure AD) használatával szerezhetik be a felhasználói adatokat, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a jogkivonat használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Megkeresést kérek

Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: az [érdeklődők felügyeletének összekötése](#connect-lead-management). 

### <a name="test-drive"></a>Próbaüzem

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt megkeresi a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

A tesztelési meghajtó engedélyezéséhez jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet. Ezután be kell állítania egy bemutató környezetet a [Test Drive technikai konfigurációjában](#test-drive-technical-configuration) , hogy az ügyfelek meghatározott időn belül kipróbálják az ajánlatot.

#### <a name="type-of-test-drive"></a>A tesztelési meghajtó típusa

Válasszon a következő lehetőségek közül:

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

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni a mentést** .

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon megadhatja az ajánlatnak a piactéren, az alkalmazás verzióját és az ajánlatát támogató jogi szerződések alapján csoportosítani kívánt kategóriákat és iparágakat. A lap befejezése után válassza a **Mentés** lehetőséget.

### <a name="category"></a>Kategória

Válasszon legalább egyet és legfeljebb három kategóriát. Ezeket a kategóriákat az ajánlatnak a piactér megfelelő keresési területeire való elhelyezésére fogjuk használni. Ügyeljen arra, hogy az ajánlat leírásában ne adja meg, hogy az ajánlat hogyan támogatja ezeket a kategóriákat.

### <a name="industry"></a>Iparág

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek ezt a verziót fogják látni az ajánlat részleteit tartalmazó oldalon.

### <a name="standard-contract"></a>Standard szerződés

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft egy standard szintű szerződést biztosít, amely megkönnyíti a tranzakciót a piactéren.

Az egyéni használati feltételek és kikötések elvégzése helyett dönthet úgy, hogy a standard szintű szerződés keretében kínálja a szoftverét, amelyet csak egyszer kell kiadnia, és csak egyszer kell elfogadni.

A standard szintű szerződés a következő címen érhető el: https://go.microsoft.com/fwlink/?linkid=2041178

A standard szintű szerződés használatához jelölje be a **standard szintű szerződés használata** jelölőnégyzetet.

#### <a name="terms-of-use"></a>Használati feltételek

Ha nem jelöli be a **standard szintű szerződés használata?** jelölőnégyzetet, meg kell adnia a saját jogi feltételeit a **használati feltételek** mezőben. Akár 10 000 karaktert is megadhat, vagy ha a használati feltételek hosszabb leírást igényelnek, adja meg azt az URL-címet, ahol a további licencfeltételek találhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket az alkalmazás kipróbálása előtt.

## <a name="offer-listing"></a>Ajánlati lista

Az ajánlati lista oldal megjeleníti azokat a nyelveket, amelyekben az ajánlat fel lesz sorolva. Jelenleg az egyetlen elérhető lehetőség az **angol (Egyesült Államok)** .

Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, képeit stb.) az egyes nyelvekhez/piacokhoz. Válassza ki a nyelv/piac nevét az információ megadásához.

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek, a használati feltételek stb.) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] verzióban érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

### <a name="name"></a>Név

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter). Ez a leírás a piactér keresési eredményeiben használható.

### <a name="description"></a>Leírás

Adja meg az ajánlat hosszabb leírását (legfeljebb 3 000 karakter). Ez a leírás az ügyfelek számára jelenik meg a piactér listázásának áttekintésében. Adja meg az ajánlat értékeit, a főbb előnyöket, a kategóriát és/vagy az iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket és a szükséges közzétételeket.

Néhány tipp a Leírás írásához:  

- A Leírás első néhány mondatában egyértelműen ismertesse ajánlata értékét. Adja meg a következő információkat az érték-kiosztásban:
  - A termék leírása
  - A termékből származó előnyöket biztosító felhasználó típusa
  - Az ügyfélnek szüksége van a termék címére
- Ne feledje, hogy az első néhány mondat a keresőmotor eredményei között jelenhet meg.  
- Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez. Ehelyett a megadott értékre kell összpontosítania.  
- A lehető legnagyobb mértékben használja az iparági specifikus szókincset vagy a juttatás-alapú szövegezést.
- HTML-címkék használatával formázhatja a leírást, és még vonzóbbá teheti őket.

Ha szeretné, hogy az ajánlat leírása jobban megtörténjen, a Rich Text Editor használatával formázhatja a leírást.

![A Rich Text Editor használata](./media/text-editor2.png)

A Rich Text Editor használatához kövesse az alábbi utasításokat:

- A tartalom formátumának módosításához jelölje ki a formázni kívánt szöveget, és válasszon egy szövegstílus-stílust az alábbi ábrán látható módon:

     ![A Rich Text Editor használata szöveg formátumának módosításához](./media/text-editor3.png)

- Ha felsorolásjeles vagy számozott listát szeretne hozzáadni a szöveghez, használja az alábbi beállításokat:

     ![A Rich Text Editor használata a listák hozzáadásához](./media/text-editor4.png)

- A szöveg behúzásának hozzáadásához vagy eltávolításához használja az alábbi beállításokat:

     ![A Rich Text Editor használata a behúzáshoz](./media/text-editor5.png)

### <a name="search-keywords"></a>Kulcsszavak keresése

Akár három keresési kulcsszót is megadhat, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmények érdekében próbálja meg használni ezeket a kulcsszavakat a leírásában is.

### <a name="products-your-app-works-with"></a>Az alkalmazás által használható termékek

Ha azt szeretné, hogy az alkalmazás az adott termékekkel működjön, adjon meg legfeljebb három terméknév-nevet.

### <a name="support-urls"></a>Támogatási URL-címek

Ez a szakasz olyan hivatkozásokat tartalmaz, amelyek segítségével az ügyfelek megismerhetik az ajánlatával kapcsolatos további információkat.

#### <a name="help-link"></a>Súgó hivatkozása

Adja meg azt az URL-címet, ahol az ügyfelek többet tudnak nyújtani az ajánlatról.

#### <a name="privacy-policy-url"></a>Adatvédelmi szabályzat URL-címe

Adja meg a szervezete adatvédelmi szabályzatának URL-címét. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

### <a name="contacts"></a>Kapcsolatok

Ebben a szakaszban adja meg a **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ezek az információk nem jelennek meg az ügyfelek számára, de a Microsoft számára elérhetővé válnak, és a CSP-partnerek számára is megadhatók.

A **támogatási kapcsolattartó** szakaszban adja meg a **támogatási URL-címet** , ahol a CSP-partnerek az ajánlathoz támogatást kaphatnak.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy (és legfeljebb három) kapcsolódó marketing-dokumentumot, például a tanulmányokat, a brosúrákat, a feladatlistákat és a bemutatókat. A dokumentumoknak. pdf formátumúnak kell lenniük.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Ebben a szakaszban megadhatja azokat az emblémákat és képeket, amelyeket az ajánlat vásárlónak való megjelenítésekor használni fog. Minden képnek. png formátumúnak kell lennie.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlat emblémáját két méretben: **kis (48 x 48)** és **nagyméretű (216 x 216)** .

#### <a name="hero"></a>Hero

A hős képe nem kötelező. Ha megadja az egyiket, a 815 x 290 képpont értéket kell mérnie.

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legalább egy képernyőképre van szükség, és legfeljebb öt adható hozzá. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár négy videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Elérhetőség

A **rendelkezésre állás** lapon megadhatja, hol és hogyan teheti elérhetővé az ajánlatát.

### <a name="markets"></a>Piacok

Ebben a szakaszban megadhatja azokat a piacokat, amelyekben ajánlatának elérhetőnek kell lennie. Ehhez válassza a **piacok szerkesztése lehetőséget,** amely megjeleníti a **piac kiválasztási** felugró ablakát.

Alapértelmezés szerint egyetlen piac sincs kiválasztva. Válasszon ki legalább egy piacot az ajánlat közzétételéhez. Az **összes kijelölése** lehetőségre kattintva elérhetővé teheti az ajánlatot minden lehetséges piacon, vagy kiválaszthatja azokat a kívánt piacokat, amelyeket hozzá szeretne adni. Ha elkészült, válassza a **Mentés**lehetőséget.

Az itt megadott beállítások csak az új beszerzésekre érvényesek; Ha valaki már rendelkezik az alkalmazással egy bizonyos piacon, és később eltávolítja ezt a piacot, akkor az adott piacon már megjelenő ügyfelek továbbra is használhatják azt, de az adott piacon nem jelennek meg új ügyfelek az ajánlatának megszerzéséhez.

> [!IMPORTANT]
> Az Ön felelőssége, hogy megfeleljen a helyi jogi követelményeknek, még akkor is, ha ezek a követelmények nem szerepelnek itt vagy a partner Centerben.

Ne feledje, hogy még akkor is, ha az összes piac lehetőséget választja, a helyi törvények és korlátozások vagy egyéb tényezők megakadályozhatják bizonyos ajánlatok egyes országokban és régiókban való listázását.

### <a name="preview-audience"></a>Előnézet célközönsége

Mielőtt közzéteszi ajánlatát a szélesebb körű Piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott **előzetes verzió célközönségét**. Itt adhatja meg az **elrejtési kulcsot** (a csak kisbetűket és/vagy számokat használó karakterláncokat). Az előzetes verzió célközönségének tagjai ezt az elrejtési kulcsot jogkivonatként használhatják a piactéren elérhető ajánlat előnézetének megtekintéséhez.

Ezután, amikor készen áll az ajánlat elérhetővé tételére és az előzetes verzió korlátozásának eltávolítására, el kell távolítania az **elrejteni kívánt kulcsot** , és újra közzé kell tennie.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **technikai konfiguráció** lap az ajánlathoz való kapcsolódáshoz használt technikai részleteket határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék.

### <a name="solution-identifier"></a>Megoldás azonosítója

Adja meg a megoldás azonosítóját (GUID).

A megoldás azonosítójának megkeresése:
1. A Microsoft Dynamics Lifecycle Services (LCS) szolgáltatásban válassza a **megoldás-kezelés**lehetőséget.
2. Válassza ki a megoldást, majd keresse meg a **megoldás azonosítóját** a **csomag áttekintésében**. Ha az azonosító üres, válassza a **Szerkesztés** lehetőséget, és tegye közzé újra a csomagot, majd próbálkozzon újra.

### <a name="release-version"></a>Kiadás verziója

Válassza ki a Dynamics 365-verziót a Pénzügy és a megoldás által használható műveletek számára.

## <a name="test-drive-technical-configuration"></a>Tesztelési meghajtó technikai konfigurációja

Ha az [ajánlat beállítása](#offer-setup) lapon a **tesztvezetés engedélyezése** lehetőséget választotta, itt meg kell adnia a részleteket, hogy az ügyfelek az ajánlat tesztelésére képesek legyenek.

A **tesztvezetés** oldal lehetővé teszi egy bemutató (vagy "tesztelési meghajtó") beállítását, amely lehetővé teszi az ügyfeleknek, hogy a megvásárlása előtt kipróbálják az ajánlatot. További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)című cikkben talál. Ha már nem szeretne tesztelési meghajtót biztosítani az ajánlatához, térjen vissza az **[ajánlat beállítása](#offer-setup)** lapra, és törölje a **Test Drive engedélyezése**jelölőnégyzet jelölését.

A következő típusú tesztelési meghajtók érhetők el, amelyek mindegyike saját technikai konfigurációs követelményekkel rendelkezik.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technikai konfiguráció nem szükséges)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó technikai konfigurációja

Egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. További információ [Azure Resource Manager tesztelési meghajtó](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező): jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.

- **Példányok**: válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

**Gyors**: Ez a típusú példány üzembe van helyezve, és a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A kompromisszum azt eredményezi, hogy ezek a példányok mindig az Azure-előfizetésen futnak, így a költségek nagyobb üzemidőt jelentenek. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

**Hideg**: az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon**: töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt.  További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)című cikkben található.

- **Tesztelési meghajtó időtartama** (kötelező): adja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra; "1,5" érvénytelen).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció a Dynamics 365 Test Drive-hoz

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét azáltal, hogy a szolgáltatás üzembe helyezését és üzembe helyezését az ilyen típusú tesztelési meghajtó használatával végzi el és tartja karban. Az ilyen típusú üzemeltetett tesztvezetés konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés üzleti központi, vevői szerepvállalási vagy műveleti célközönségre irányul.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező): adja meg, hogy legfeljebb hány ügyfél használhatja egyszerre a tesztelési meghajtót. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért biztosítania kell, hogy elegendő licenc álljon rendelkezésre a maximálisan engedélyezett készlet támogatásához. A 3-5 javasolt értéke.

- **Tesztelési meghajtó időtartama** (kötelező): adja meg, hogy mennyi ideig maradjon aktív a tesztvezetés az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra; "1,5" érvénytelen).  Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező): az az URL-cím, amelyben az ügyfél el fogja kezdeni a tesztelési meghajtót. Általában a Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja, és a mintaadatok telepítve vannak (például https://testdrive.crm.dynamics.com).

- **Példány webes API URL-címe** (kötelező): a Dynamics 365-példány webes API URL-címének lekéréséhez jelentkezzen be a Microsoft 365-fiókjába, és navigáljon a **Beállítások** \&gt; **Testreszabás** \&gt; **Fejlesztői erőforrások** \&gt; A **példány webes API-ját (szolgáltatás gyökerének URL-címe)** másolja az itt található URL-címet (például https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Szerepkör neve** (kötelező): adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét. Ezt a rendszer a tesztelési meghajtó (például a test-Drive-role) során rendeli hozzá a felhasználóhoz.

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technikai konfiguráció a Logic app Test Drive-hoz

Minden egyéni terméknek ezt a típusú tesztelési meghajtó-telepítési sablont kell használnia, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyszer használatos legördülő lista): jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. A legjobb módszer annak biztosítására, hogy az Ön régiójának egyéni erőforrásai is elérhetők legyenek, hogy az Azure-előfizetésében helyileg telepítse a logikai alkalmazást a portálon, és ellenőrizze, hogy megfelelően működik-e a kijelölés előtt.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező): adja meg, hogy legfeljebb hány ügyfél használhatja egyszerre a tesztelési meghajtót. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Tesztelési meghajtó időtartama** (kötelező): adja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező): adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, amelybe a rendszer menti a logikai alkalmazás tesztelési meghajtóját.

- **Azure Logic App-alkalmazás neve** (kötelező): adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót hozzárendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének** megszüntetése (kötelező): adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót felépíti az ügyfél befejezése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI tesztelési meghajtókhoz nem szükséges technikai konfiguráció

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. További információ a[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) template-alkalmazások beállításáról.

### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Ha a tesztelési meghajtót az Ön nevében szeretné üzembe helyezni, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést. (Power BI tesztelési meghajtók esetében nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges): adja meg az előfizetés azonosítóját, amely hozzáférést biztosít az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](https://docs.microsoft.com/azure/billing/billing-create-subscription) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztásával megjelenítheti az előfizetés-AZONOSÍTÓját (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező): adja meg a Azure Active Directory (ad) [bérlői azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok**elemet, majd keresse **meg a listában** szereplő 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címével is megkeresheti a következő helyen: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges): adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad** -alkalmazás azonosítója (kötelező): adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás ügyfél-titka** (kötelező): adja meg az Azure ad-alkalmazás [ügyfél-titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. (Ne navigáljon el az oldalról, mielőtt az értéket jegyezze fel, vagy nem fér hozzá az értékhez.)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

### <a name="test-drive-marketplace-listings"></a>A Marketplace-beli piactér-listák tesztelése

A piactér **lapon található** **Marketplace listázási** lehetőség megjeleníti azokat a nyelveket, amelyeken a tesztelési meghajtó elérhető. Jelenleg az egyetlen elérhető hely az **angol (Egyesült Államok)** . Válassza ki a nyelv nevét a test Drive-élményt leíró információk megadásához.

- **Leírás** (kötelező): írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célokat, a felderített funkciókat, valamint minden olyan releváns információt, amellyel a felhasználó eldöntheti, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni.

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges): ismertesse, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező): részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók: videók hozzáadása** (nem kötelező): a videók a YouTube vagy a Vimeo webhelyre tölthetők fel, és a hivatkozás és a miniatűr képét (533 x 324 képpont) is feltölthetik, így az ügyfél megtekintheti az információk áttekintését, így könnyebben megismerheti a tesztelési meghajtót, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyveket.
  - **Név** (kötelező)
  - **URL-cím (csak YouTube vagy Vimeo)** (kötelező)
  - **Miniatűr (533 x 324px)** : a képfájlnak png formátumúnak kell lennie.

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat adhat meg az ajánlatáról, hogy segítsen az ajánlat érvényesítésében. Ezek az információk nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="validation-assets"></a>Érvényesítési eszközök

Töltse fel a [testreszabási elemzési jelentést (Car)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) ebben a szakaszban. Ez a jelentés a testreszabási és bővítményi modellek elemzésével jön létre, az ajánlott eljárási szabályok előre meghatározott készlete alapján.

Ennek a fájlnak. xls vagy. xlsx formátumúnak kell lennie. Ha több jelentés is van, feltöltheti az összes jelentést tartalmazó. zip-fájlt.

### <a name="does-solution-include-localizations"></a>A megoldás tartalmaz-e honosítást?

Válassza az **Igen** lehetőséget, ha a megoldás lehetővé teszi a helyi szabványok és házirendek használatát (például ha a különböző, különböző országokban/régiókban megkövetelt bérlista-szabályokat tartalmazza). Ellenkező esetben válassza **Nem** lehetőséget.

### <a name="does-solution-enable-translations"></a>Engedélyezi a megoldás a fordításokat?

Válasz **Igen** , ha a megoldás szövege más nyelvekre is fordítható. Ellenkező esetben válassza **Nem** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután befejezte az ajánlat összes szükséges szakaszt, válassza a **Közzététel** lehetőséget a portál jobb felső sarkában. A rendszer átirányítja a **felülvizsgálat és közzététel** lapra.

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - *Nincs elindítva* – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
    - *Hiányos* – azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információkat kell megadni. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
    - *Complete (Befejezés* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- A **minősítési megjegyzések** szakaszban adja meg a minősítési csoport tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez szükséges kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvános közzétételére (vagy ha egy privát ajánlatra a privát közönség számára).

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
