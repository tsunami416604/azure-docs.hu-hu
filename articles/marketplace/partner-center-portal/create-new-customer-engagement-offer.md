---
title: Dynamics 365 létrehozása a Customer engagement & PowerApps ajánlathoz a Microsoft kereskedelmi piactéren
description: Új Dynamics 365 létrehozása a Customer engagement & PowerApps az Azure Marketplace-en, a AppSource-on vagy a partner centeren keresztül a Cloud Solution Provider (CSP) programon keresztül történő listázásához és értékesítéséhez.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 51df588b1ccd41bf05d8851f00399b61292b0736
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214101"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Dynamics 365 for Customer Engagement- és PowerApps-ajánlat létrehozása

Ez a témakör azt ismerteti, hogyan hozhat létre új Dynamics 365-t az ügyfelek engagement & PowerApps ajánlatához. A Dynamics 365 for Customer engagement (PowerApps, Sales, Service, Project Service és Field Service) összes alkalmazásának a minősítési folyamaton keresztül kell haladnia, és támogatni kell a próbaidőszakot. A minősítési folyamat a standard követelmények, a kompatibilitás és a megfelelő eljárások tekintetében ellenőrzi a megoldást. A próbaverzió lehetővé teszi, hogy a felhasználók egy élő Dynamics 365-környezetbe telepítse a megoldást.

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Dynamics 365 lehetőséget a Customer engagement & PowerApps**.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végzett módosítások csak az ajánlat ismételt közzététele után jelennek meg a kirakatokban. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Ez a **Létrehozás**gombra kattintva nem módosítható.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hogyan kívánja használni a potenciális ügyfeleket a jelen tőzsdei ajánlathoz?

Válassza ki az ajánlathoz használni kívánt beállítást.

#### <a name="get-it-now-free"></a>Letöltés (ingyenes)

Az ajánlatát ingyenesen listázhatja, ha érvényes URL-címet ad meg (a *http* vagy a *https*-től kezdve), ahol elérheti az alkalmazást.  Például: `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)

Az ajánlatot az ingyenes próbaverzióra mutató hivatkozással listázhatja, ha egy érvényes URL-címet biztosít (a vagy a verziótól kezdve `http` `https` ), ahol próbaverziót kaphat.  Például: `https://contoso.com/trial/my-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak Azure Active Directory (Azure AD) használatával szerezhetik be a felhasználói adatokat, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a jogkivonat használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolatfelvétel

Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [ügyfél-érdeklődők](#customer-leads).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="test-drive"></a>Tesztelési meghajtó

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt kipróbálhatja a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből. A test Drive-környezet konfigurálása a [tesztelési meghajtó technikai konfigurációja](#test-drive-technical-configuration) szakaszban, a témakör későbbi részében.

További információ: az [ajánlat tesztelése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>A tesztelési meghajtó típusa

Válasszon a következő lehetőségek közül:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – a Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve az üzembe helyezést és a telepítést is) egy üzleti központi vállalati erőforrás-tervezési rendszer (pénzügy, műveletek, ellátási lánc, CRM stb.) számára.  
- **[Dynamics 365 for Customer engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – a Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve az üzembe helyezést és a telepítést) az ügyfél-engagement rendszer (értékesítés, szolgáltatás, projekt szolgáltatás, mező szolgáltatás stb.) számára.  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – a Microsoft üzemelteti és karbantartja a test Drive szolgáltatást (beleértve a kiépítést és az üzembe helyezést) pénzügyi és üzemeltetési vállalati erőforrás-tervezési rendszerekhez (pénzügy, műveletek, gyártás, ellátási lánc stb.). 
- **[Logic app (logikai alkalmazás](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** ) – egy központi telepítési sablon, amely az összes összetett megoldás-architektúrát magában foglalja. Minden egyéni terméknek ilyen típusú tesztelési meghajtót kell használnia.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – egy egyéni beépített irányítópultra mutató beágyazott hivatkozás. Azok a termékek, amelyek interaktív Power BI vizualizációt kívánnak bizonyítani, ezt a típusú tesztelési meghajtót kell használniuk. Itt a beágyazott Power BI URL-címére kell feltöltenie.

#### <a name="additional-test-drive-resources"></a>További tesztelési meghajtó erőforrásai

- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketing – ajánlott eljárások](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tesztelési meghajtók – áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (ellenőrizze, hogy az előugró ablakok blokkolása ki van-e kapcsolva)

### <a name="customer-leads"></a>Ügyfél-érdeklődők

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren, az alkalmazás verziójában és az ajánlatát támogató jogi szerződésekben való csoportosításához használt kategóriákat és iparágakat.

### <a name="category"></a>Kategória

Válasszon legalább egyet, és legfeljebb három kategóriát. Ezeket az ajánlatokat a megfelelő Piactéri keresési területeken helyezheti el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában.

### <a name="industry"></a>Iparág

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Alkalmazható Dynamics 365-termékek

Válassza ki az összes olyan Dynamics 365-terméket, amelyre ez az ajánlat vonatkozik.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek ezt a verziót fogják látni az ajánlat részleteit tartalmazó oldalon. Ha csak a marketing/leíró változások miatt frissíti a verziószámot, jelölje be a **csak marketing módosítás** jelölőnégyzetet. Ez a beállítás lehetővé teszi, hogy az ajánlat megkerüljék a minősítési és kiépítési fázisokat.

### <a name="terms-and-conditions"></a>használati feltételei

Itt megadhatja a saját jogi feltételeit. Megadhatja azt is, hogy hol találhatók a feltételek és kikötések. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Ezen a lapon láthatók azok a nyelvek, amelyekben az ajánlat fel lesz sorolva. Jelenleg az egyetlen elérhető lehetőség az **angol (Egyesült Államok)** .

Itt adhatja meg az egyes nyelvekhez/piacokhoz tartozó piactér részleteit, például az ajánlat nevét, leírását és lemezképeit. Válassza ki a nyelv/piac nevét az információk megadásához.

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek és a használati feltételek) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

Íme egy példa arra, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatos célokat szolgálnak):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Termékek
3. Kategóriák
4. Támogatási címe (hivatkozás)
5. Használati feltételek címe (hivatkozás)
6. Ajánlat neve
7. Leírás
8. Képernyőképek/videók

### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat rövid leírását, amely legfeljebb 100 karakter hosszú lehet. Ez a leírás a piactér keresési eredményeiben használható.

### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Kulcsszavak keresése

Akár három keresési kulcsszót is megadhat, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmény érdekében ezeket a kulcsszavakat is használhatja a leírásában.

### <a name="products-your-app-works-with"></a>Az alkalmazás által használható termékek

Ha azt szeretné, hogy az alkalmazás az adott termékekkel működjön, adjon meg legfeljebb három terméknév-nevet.

### <a name="support-urls"></a>Támogatási URL-címek

Ez a szakasz olyan hivatkozásokat tartalmaz, amelyek segítségével az ügyfelek megismerhetik az ajánlatával kapcsolatos további információkat.

#### <a name="help-link"></a>Súgó hivatkozása

Adja meg azt a címeket, ahol az ügyfelek többet tudnak nyújtani az ajánlatról.

#### <a name="privacy-policy-url"></a>Adatvédelmi szabályzat URL-címe

Adja meg a szervezete adatvédelmi szabályzatának a címeit. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

### <a name="contacts"></a>Kapcsolattartók

Adja meg a **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ez az információ nem jelenik meg az ügyfelek számára, de a Microsoft számára elérhetővé válik, és a CSP-partnerek számára biztosítva lehet.

A **támogatási kapcsolattartó** szakaszban meg kell adnia azt a **támogatási URL-címet** is, ahol a CSP-partnerek megtalálják az ajánlat támogatását.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy (és legfeljebb három) kapcsolódó marketing-dokumentumot, például a tanulmányokat, a brosúrákat, a feladatlistákat vagy a bemutatókat PDF formátumban.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz tartozó emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie.

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg ajánlata emblémáját három képpontos méretben:
- **Kicsi** (kötelező; 48 x 48)
- **Nagyméretű** (kötelező; 216 x 216)
- **Wide** (opcionális; 255 x 115)

#### <a name="hero"></a>Hero

A hős képe nem kötelező. Ha megadja az egyiket, a 815 x 290 képpont értéket kell mérnie.

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legalább egy képernyőképre van szükség, és legfeljebb öt adható hozzá. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár négy videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

[Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

Ezen a lapon megadhatja, hogy hol és hogyan szeretné elérhetővé tenni az ajánlatot.

### <a name="markets"></a>Piacok

Adja meg azokat a piacokat, amelyekben ajánlatának elérhetőnek kell lennie. Ehhez válassza a **piacok szerkesztése**lehetőséget, amely megjeleníti a **piac kiválasztási** felugró ablakát.

Alapértelmezés szerint egyetlen piac sincs kiválasztva, de ki kell választania legalább egy piacot az ajánlat közzétételéhez. Az **összes kijelölése** lehetőség kiválasztásával elérhetővé teheti az ajánlatot minden lehetséges piacon, vagy kiválaszthatja a felvenni kívánt adott piacokat.

Az itt megadott beállítások csak az új beszerzésekre érvényesek; Ha valaki már rendelkezik az alkalmazással egy bizonyos piacon, és később eltávolítja ezt a piacot, akkor az adott piacon már meglévő ajánlata továbbra is használható, de az adott piacon még nem jelennek meg új ügyfelek az ajánlatában.

> [!IMPORTANT]
> Az Ön felelőssége, hogy megfeleljen a helyi jogi követelményeknek, még akkor is, ha ezek a követelmények nem szerepelnek itt vagy a partner Centerben. Akkor is, ha az összes piac lehetőséget választja, a helyi törvények és korlátozások vagy egyéb tényezők megakadályozhatják bizonyos ajánlatok egyes országokban és régiókban való listázását.

### <a name="preview-audience"></a>Előnézet célközönsége

Mielőtt közzéteszi ajánlatát a szélesebb körű Piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott **előzetes verzió célközönségét**. Itt adhatja meg az **elrejtési kulcsot** (a csak kisbetűket és/vagy számokat használó karakterláncokat). Az előzetes verzió célközönségének tagjai ezt az elrejtési kulcsot jogkivonatként használhatják a piactéren elérhető ajánlat előnézetének megtekintéséhez.

Ezután, amikor készen áll az ajánlat elérhetővé tételére és az előzetes verzió korlátozásának eltávolítására, el kell távolítania az **elrejteni kívánt kulcsot** , és újra közzé kell tennie.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Ez az oldal az ajánlathoz való kapcsolódáshoz használt technikai részleteket határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék.

### <a name="base-license-model"></a>Alap licencelési modell

Az alap licencelési modell határozza meg, hogy az ügyfelek Hogyan kapják meg az alkalmazást a CRM felügyeleti központban. Válassza ki az **erőforrást** a példány-alapú licenceléshez vagy a **felhasználóhoz** , ha a licencek egy bérlőhöz vannak rendelve.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>A S2S kimenő és a CRM Biztonságos tár elérését igényli

Jelölje be ezt a jelölőnégyzetet a CRM Biztonságos tár vagy a kiszolgálók közötti (S2S) kimenő hozzáférés konfigurálásának engedélyezéséhez. Ehhez a szolgáltatáshoz a minősítési fázisban a Dynamics 365 csapata speciális figyelmet igényel. A Microsoft felveszi Önnel a kapcsolatot a funkció támogatásához szükséges további lépések végrehajtásához.

### <a name="application-configuration-url"></a>Alkalmazás konfigurációjának URL-címe

Adja meg a konfigurációs weblap URL-címét, amely lehetővé teszi az ügyfél számára az alkalmazás konfigurálását.

### <a name="crm-package"></a>CRM-csomag

A **csomag helyének URL-** címe mezőben adja meg egy Azure Blob Storage-fiók URL-címét, amely tartalmazza a feltöltött CRM Package. zip fájlt. Egy írásvédett SAS-kulcsot is tartalmaz az URL-címben, így a Microsoft a csomagot az ellenőrzéshez is felveheti.

> [!IMPORTANT]
> A közzétételi blokk elkerüléséhez győződjön meg arról, hogy a blob Storage URL-címében szereplő lejárati dátum nem járt le. A dátum a Szabályzathoz való hozzáféréssel módosítható. Azt javasoljuk, hogy a **lejárati idő** legyen legalább egy hónappal később.

Jelölje be a csomagban található **több CRM-csomag**elemet, ha van ilyen. Ha igen, ügyeljen arra, hogy a. zip fájlban lévő összes csomagot tartalmazza.

A csomag létrehozásával és szerkezetének frissítésével kapcsolatos részletes információkért lásd a [3. lépés: AppSource-csomag létrehozása az alkalmazáshoz](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource)című témakört.

### <a name="crm-package-availability"></a>CRM-csomag elérhetősége

Ebben a szakaszban válassza a **+ régió hozzáadása** lehetőséget, hogy megadja azokat a földrajzi régiókat, amelyekben a CRM-csomag elérhető lesz az ügyfelek számára. A következő szuverén régiókban való üzembe helyezéshez különleges engedélyek és érvényesítés szükséges a minősítési folyamat során: [Németország](https://docs.microsoft.com/azure/germany/), [USA kormányzati felhője](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)és tipp.

Alapértelmezés szerint az egyes régiókban a fentiekben megadott **konfigurációs URL-cím** lesz használva. Ha szeretné, megadhat egy külön alkalmazás-konfigurációs URL-címet egy vagy több meghatározott régióhoz. 

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive-technical-configuration"></a>Tesztelési meghajtó technikai konfigurációja

Ezen a lapon megadható egy bemutató ("tesztelési meghajtó"), amely lehetővé teszi, hogy az ügyfelek a vásárlás előtt kipróbálják az ajánlatot. További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)című cikkben talál.

A tesztelési meghajtó engedélyezéséhez jelölje be a test Drive engedélyezése jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)című cikkben talál.

A következő típusú tesztelési meghajtók érhetők el, amelyek mindegyike saját technikai konfigurációs követelményekkel rendelkezik:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technikai konfiguráció nem szükséges)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó technikai konfigurációja

Egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. További információ [Azure Resource Manager tesztelési meghajtó](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező) – jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.
- **Példányok** – válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

    Gyakori – ez a típusú példány üzembe van helyezve **, és** a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A kompromisszum azt eredményezi, hogy ezek a példányok mindig az Azure-előfizetésen futnak, így a költségek nagyobb üzemidőt jelentenek. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

    **Hideg** – az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon** – töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt. További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)című cikkben található.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy hány óra elteltével marad aktív a tesztvezetés. A testdDrive ezen időszak lejárta után automatikusan leáll. Ez az időtartam csak egész órában állítható be (például "2" óra érvényes). "1,5" nem).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció a Dynamics 365 Test Drive-hoz

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét azáltal, hogy a szolgáltatás üzembe helyezését és üzembe helyezését az ilyen típusú tesztelési meghajtó használatával végzi el és tartja karban. Az ilyen típusú üzemeltetett tesztvezetés konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés üzleti központi, vevői szerepvállalási vagy műveleti célközönségre irányul.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező) – állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért biztosítania kell, hogy elegendő licenc álljon rendelkezésre a maximálisan engedélyezett készlet támogatásához. Az ajánlott érték 3-5.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy hány óra elteltével marad aktív a tesztvezetés. Ezt követően a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ez az időtartam csak egész órában állítható be (például "2" óra érvényes). "1,5" nem). Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező) – az az URL-cím, amelyben az ügyfél elkezdi a tesztelési meghajtót. Általában a Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja (például: `https://testdrive.crm.dynamics.com` ).

- **Példány webes API URL-címe** (kötelező) – a Dynamics 365-példány webes API URL-címének lekéréséhez jelentkezzen be a Microsoft 365-fiókjába, és navigáljon a **Beállítások**  >  **testreszabása**  >  **fejlesztői erőforrások**  >  **példány webes API (szolgáltatás gyökerének URL-címe)** mezőbe, és másolja az itt található URL-címet (például: `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Szerepkör neve** (kötelező) – adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét. A rendszer ezt a biztonsági szerepkört fogja hozzárendelni a felhasználóhoz a tesztelési meghajtón (például Test-Drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technikai konfiguráció a Logic app Test Drive-hoz

Minden egyéni terméknek ezt a típusú tesztelési meghajtó-telepítési sablont kell használnia, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyetlen kiválasztható legördülő lista) – jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztvezetés elérhetővé tehető. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. A legjobb módszer, ha a logikai alkalmazást helyileg helyezi üzembe az Azure-előfizetésében a portálon, és ellenőrizze, hogy megfelelően működik-e a kijelölés előtt.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező) – állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező) – adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, ahová a rendszer menti a logikai alkalmazás tesztelési meghajtóját.

- **Azure Logic App-alkalmazás neve** (kötelező) – Itt adhatja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót hozzárendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének** megszüntetése (kötelező) – Itt adhatja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót kiépíti az ügyfél befejeződése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI tesztelési meghajtókhoz nem szükséges technikai konfiguráció

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. További információ a[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) template-alkalmazások beállításáról.

### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Annak engedélyezéséhez, hogy a Microsoft a tesztelési meghajtót az Ön nevében telepítse, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést (Power BI tesztelési meghajtókhoz nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges) – adja meg az előfizetés azonosítóját, hogy hozzáférést biztosítson az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](https://docs.microsoft.com/azure/billing/billing-create-subscription) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztásával megjelenítheti az előfizetés-AZONOSÍTÓját (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező) – adja meg a Azure Active Directory (ad) [bérlői azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok**elemet, majd keresse **meg a listában** szereplő 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címe alapján is megkeresheti a következő helyen: [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges) – adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad** -alkalmazás azonosítója (kötelező) – adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-ügyfél titkos kulcsa** (kötelező) – adja meg az Azure ad-alkalmazás [ügyfél-titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. Ne navigáljon el az oldalról, mielőtt lemásolja az értéket, vagy nem fér hozzá az értékhez.)

### <a name="test-drive-marketplace-listings"></a>A Marketplace-beli piactér-listák tesztelése

A piactér **lapon található** **Marketplace listázási** lehetőség megjeleníti azokat a nyelveket, amelyeken a tesztelési meghajtó elérhető. Jelenleg az egyetlen elérhető hely az **angol (Egyesült Államok)** . Válassza ki a nyelv nevét a test Drive-élményt leíró információk megadásához.

- **Leírás** (kötelező) – írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célkitűzéseket, a felderített funkciókat, valamint minden olyan releváns információt, amely segít a felhasználónak eldönteni, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges) – megtudhatja, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező) – részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók** (nem kötelező) – videók feltöltése egy külső szolgáltatói helyre hivatkozás a hivatkozás és a miniatűr képe (533 x 324 képpont). Ez lehetővé teszi az ügyfelek számára, hogy megtekintsék az információkat, hogy jobban megértsék a tesztelési meghajtót, beleértve az ajánlat funkcióinak sikeres használatát, valamint az előnyeiket kiemelő forgatókönyvek megismerését.
  - **Név** (kötelező)
  - **Címe** (kötelező; Csak YouTube vagy Vimeo)
  - **Miniatűr** képe (a képfájlnak PNG formátumban kell lennie, és 533 x 324 képpont)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat adhat meg az ajánlatáról, hogy segítsen az ajánlat érvényesítésében. Ezek az információk nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="key-usage-scenario"></a>Kulcshasználat forgatókönyv

Töltsön fel egy PDF-fájlt, amely felsorolja az ajánlat legfontosabb felhasználási forgatókönyveit. Az ellenőrzési csapat minden forgatókönyvet ellenőrizheti, mielőtt jóváhagyja ajánlatát a piactéren.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután befejezte az ajánlat összes szükséges szakaszt, válassza a **felülvizsgálat és közzététel** elemet a portál jobb felső sarkában.

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - **Nincs elindítva** – a szakasz nincs megérintve, és el kell végezni.
    - **Hiányos** – a szakasznak olyan hibákkal kell rendelkeznie, amelyeket meg kell oldani, vagy további információkat kell megadnia. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
    - **Complete (Befejezés** ) – a szakasz elkészült, az összes szükséges információ meg van határozva, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- A **minősítési megjegyzések** szakaszban adja meg a minősítési csoport tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez szükséges kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat számára a nyilvános közzétételhez (vagy ha egy privát ajánlat, a privát közönség számára).

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
