---
title: Dynamics 365 Business Central-ajánlat létrehozása – Microsoft kereskedelmi piactér
description: Ismerje meg az új Dynamics 365 Business Central-ajánlat létrehozásának lépéseit és szempontjait a partner Center kereskedelmi piactér portálján. Az ajánlatot az Azure Marketplace-en vagy a Cloud Solution Provider (CSP) programon keresztül is listázhatja vagy értékesítheti.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 04de89624dd0e6857e96327bb408cf8700a1f6a2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848881"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Microsoft Dynamics 365 Business Central-ajánlat létrehozása

Ez a cikk azt ismerteti, hogyan hozható létre új Dynamics 365 Business Central-ajánlat. A [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) egy vállalati erőforrás-tervezési (ERP) rendszer, amely az üzleti folyamatok széles körét kezeli, többek között a pénzügy, a műveletek, az ellátási lánc, a CRM, a projektmenedzsment és az elektronikus kereskedelem terén. A Prémium csomag a klasszikus üzembe helyezési modellt és a gyártást is támogatja. A Dynamics 365 Business Central szolgáltatáshoz készült összes ajánlatnak a minősítési folyamaton keresztül kell haladnia.

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Dynamics 365 Business Central**lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-dynamics-365-bc.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végzett módosítások csak az ajánlat ismételt közzététele után jelennek meg a kirakatokban. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Az ajánlat aliasa nem módosítható a **Létrehozás**gombra kattintva.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hogyan kívánja használni a potenciális ügyfeleket a jelen tőzsdei ajánlathoz?

Válassza ki az ajánlathoz használni kívánt beállítást.

#### <a name="get-it-now-free"></a>Letöltés (ingyenes)

Az ajánlatát ingyenesen listázhatja, ha érvényes URL-címet ad meg (a *http* vagy a *https*-től kezdve), ahol elérheti az alkalmazást.  Például: `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)

Az ajánlat az ingyenes próbaverzióra mutató hivatkozással listázhatja az ügyfeleket, ha egy érvényes URL-címet (a *http* -t vagy a *https*-t) biztosít, ahol próbaverziót kaphat.  Például: `https://contoso.com/trial/my-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak Azure Active Directory (Azure AD) használatával szerezhetik be a felhasználói adatokat, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a jogkivonat használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolatfelvétel

Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [ügyfél-érdeklődők](#customer-leads).

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

- [A test Drive technikai ajánlott eljárásai](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A test Drive marketing ajánlott eljárásai](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tesztelési meghajtók – áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (ellenőrizze, hogy az előugró ablakok blokkolása ki van-e kapcsolva)

### <a name="customer-leads"></a>Ügyfél-érdeklődők

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren, az alkalmazás verziójában és az ajánlatát támogató jogi szerződésekben való csoportosításához használt kategóriákat és iparágakat.

### <a name="category"></a>Kategória

Válasszon ki legalább egyet, és legfeljebb három kategóriát, amelyek az ajánlatnak a piactér megfelelő keresési területeire való elhelyezésére szolgálnak. Ügyeljen arra, hogy az ajánlat leírásában ne adja meg, hogy az ajánlat hogyan támogatja ezeket a kategóriákat. 

### <a name="industry"></a>Iparág

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek ezt a verziót fogják látni az ajánlat részleteit tartalmazó oldalon.

### <a name="terms-and-conditions"></a>használati feltételei

A **feltételek és** kikötések mezőben adja meg a saját jogi feltételeit. Megadhatja azt az URL-címet is, ahol a feltételek és kikötések megtalálhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Itt adhatja meg az ajánlat részleteit, például a nevet, a leírást és a képeket.

> [!NOTE]
> Az ajánlat részletei csak egy nyelven adhatók meg. Nem kell angol nyelven lennie, amíg az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] nyelven érhető el." Azt is elfogadható, hogy a *Súgó hivatkozás URL-címe* , amely az ajánlati tartalomban használt tartalomtól eltérő nyelven kínál tartalmat.

### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat rövid leírását, amely legfeljebb 100 karakter hosszú lehet. Ez a leírás a piactér keresési eredményeiben használható.

### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Kulcsszavak keresése

Akár három keresési kulcsszót is megadhat, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmények érdekében próbálja meg használni ezeket a kulcsszavakat a leírásában is.

### <a name="products-your-app-works-with"></a>Az alkalmazás által használható termékek

Ha azt szeretné, hogy az alkalmazás az adott termékekkel működjön, adjon meg legfeljebb három terméknév-nevet.

### <a name="helpprivacy-urls"></a>Súgó/adatvédelmi URL-címek

Ez a szakasz olyan hivatkozásokat tartalmaz, amelyek segítségével az ügyfelek megismerhetik az ajánlatával kapcsolatos további információkat.

#### <a name="help-link"></a>Súgó hivatkozása

Adja meg azt az URL-címet, ahol az ügyfelek többet tudnak nyújtani az ajánlatról. A **Súgó hivatkozása** nem egyezhet meg a **támogatási URL-címmel** (lásd alább).

#### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának URL-címét. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

### <a name="contact-information"></a>Kapcsolattartási adatok

Ebben a szakaszban meg kell adnia egy **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ez az információ nem jelenik meg az ügyfelek számára, de a Microsoft számára elérhetővé válik, és a CSP-partnerek számára is biztosítható.

A **támogatási kapcsolattartó** szakaszban meg kell adnia azt a **támogatási URL-címet** is, ahol a CSP-partnerek megtalálják az ajánlat támogatását. A támogatási URL-cím nem egyezhet meg a **Súgó hivatkozásával**.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy (és legfeljebb három) kapcsolódó marketing-dokumentumot, például a tanulmányokat, a brosúrákat, a feladatlistákat és a bemutatókat. A dokumentumoknak. pdf formátumúnak kell lenniük.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz tartozó emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. Töltse fel az ajánlat emblémáját két méretben:

* **Kicsi** (48 x 48 képpont)
* **Nagyméretű** (216 x 216 képpont)

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a `https://upload.xboxlive.com` partner központ által használt szolgáltatást.

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legalább három képernyőkép szükséges, és legfeljebb öt adható hozzá. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár öt videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

[Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

Ezen a lapon megadhatja, hol és hogyan teheti elérhetővé az ajánlatát.

### <a name="markets"></a>Piacok

Ebben a szakaszban megadhatja azokat a piacokat, amelyekben ajánlatának elérhetőnek kell lennie. Ehhez válassza a **piacok szerkesztése**lehetőséget, amely megjeleníti a **piac kiválasztási** felugró ablakát.

Válasszon ki legalább egy piacot az ajánlat közzétételéhez. Az **összes kijelölése** lehetőség kiválasztásával elérhetővé teheti az ajánlatot minden lehetséges piacon, vagy kiválaszthatja a felvenni kívánt adott piacokat.

Az itt megadott beállítások csak az új beszerzésekre érvényesek; Ha valaki már rendelkezik az alkalmazással egy bizonyos piacon, és később eltávolítja ezt a piacot, akkor az adott piacon már megjelenő ügyfelek továbbra is használhatják azt, de az adott piacon nem jelennek meg új ügyfelek az ajánlatának megszerzéséhez.

> [!IMPORTANT]
> Az Ön felelőssége, hogy megfeleljen a helyi jogi követelményeknek, még akkor is, ha ezek a követelmények nem szerepelnek itt vagy a partner Centerben.

Ne feledje, hogy még akkor is, ha az összes piac, a helyi törvények, a korlátozások vagy más tényezők kiválasztásával bizonyos ajánlatokat egyes országokban és régiókban is meg lehet akadályozni.

### <a name="preview-audience"></a>Előnézet célközönsége

Mielőtt közzéteszi ajánlatát a szélesebb körű Piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott **előzetes verzió célközönségét**. Itt adhatja meg az **elrejtési kulcsot** (a csak kisbetűket és/vagy számokat használó karakterláncokat). Az előzetes verzió célközönségének tagjai ezt az elrejtési kulcsot jogkivonatként használhatják a piactéren elérhető ajánlat előnézetének megtekintéséhez.

Ezután, amikor készen áll az ajánlat elérhetővé tételére és az előzetes verzió korlátozásának eltávolítására, el kell távolítania az **elrejteni kívánt kulcsot** , és újra közzé kell tennie.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Ez az oldal az ajánlathoz való kapcsolódáshoz használt technikai részleteket határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék.

### <a name="package-type"></a>Csomag típusa

Válassza ki az ajánlathoz tartozó beállítást:

* **Bekapcsolás** – a kiegészítő alkalmazás a Dynamics 365 Business Central által nyújtott élményt és a meglévő funkciókat bővíti. Részletekért lásd: [kiegészítő alkalmazások](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Kapcsolódás** – a kapcsolódási alkalmazás abban a forgatókönyvben használható, ahol a Dynamics 365 Business Central és egy harmadik féltől származó megoldás vagy szolgáltatás között pont-pont típusú kapcsolatot kell létrehozni. Részletekért lásd az [alkalmazások összekapcsolását](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)ismertető témakört.

### <a name="file-upload"></a>Fájlfeltöltés

Ha a fenti **Hozzáadás** lehetőséget választotta, itt töltheti fel az ajánlat csomagfájl, valamint a csomagok fájljait minden olyan bővítményhez, amelyen függőségek vannak.

#### <a name="extensions-package-file"></a>Kiterjesztések csomagfájl

Töltse fel az ajánlathoz tartozó fájlkiterjesztés-fájlt (. app).

#### <a name="library-package-file"></a>Függvénytár-csomagfájl

Kötelező, ha az ajánlatát egy olyan bővítménnyel együtt kell telepíteni, amely nem lesz közzétéve a piactéren. Ha igen, töltse fel az. app fájlt.

#### <a name="dependency-package-file"></a>Függőségi csomag fájlja

Kötelező, ha az ajánlatot a piactéren már közzétett másik bővítménnyel együtt kell telepíteni. Ha igen, töltse fel `.app` itt a vagy a `.zip` fájlt.

### <a name="url-to-app-installation"></a>Az alkalmazás telepítésének URL-címe

Ha a fenti **kapcsolat** lehetőséget választotta, itt adja meg az alkalmazás telepítési címeit. A telepítést nem igénylő csatlakoztatott szolgáltatások esetében adja meg a szolgáltatás kezdőlapjának vagy regisztrációs oldalának a címeit.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive-technical-configuration"></a>Tesztelési meghajtó technikai konfigurációja

Ezen a lapon megadható egy bemutató ("tesztelési meghajtó"), amely lehetővé teszi, hogy az ügyfelek a vásárlás előtt kipróbálják az ajánlatot. További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)című cikkben talál.

A tesztelési meghajtó engedélyezéséhez jelölje be a **Test Drive engedélyezése** jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

A következő típusú tesztelési meghajtók érhetők el, amelyek mindegyike saját technikai konfigurációs követelményekkel rendelkezik.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technikai konfiguráció nem szükséges)

További tesztelési meghajtó erőforrásai:

- [Marketingre vonatkozó ajánlott eljárások](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó technikai konfigurációja

Egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. További információ [Azure Resource Manager tesztelési meghajtó](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező) – jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.

- **Példányok** – válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

    Gyakori – ez a típusú példány üzembe van helyezve **, és** a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A kompromisszum azt eredményezi, hogy ezek a példányok mindig az Azure-előfizetésen futnak, így a költségek nagyobb üzemidőt jelentenek. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

    **Hideg** – az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon** – töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt.  További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)című cikkben található.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy mennyi idő alatt marad a tesztvezetés aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után. Csak egész számokat használjon (például "2" óra érvényes, "1,5").

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció a Dynamics 365 Test Drive-hoz

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét azáltal, hogy a szolgáltatás üzembe helyezését és üzembe helyezését az ilyen típusú tesztelési meghajtó használatával végzi el és tartja karban. Az ilyen típusú üzemeltetett tesztvezetés konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés üzleti központi, vevői szerepvállalási vagy műveleti célközönségre irányul.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező) – állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért biztosítania kell, hogy elegendő licenc álljon rendelkezésre a maximálisan engedélyezett készlet támogatásához. A 3-5 javasolt értéke.

- **Tesztelési meghajtó időtartama** (kötelező) – adja meg, hogy mennyi ideig marad aktív a tesztvezetés az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra, "1,5" nem érvényes).  Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező) – az az URL-cím, amelyben az ügyfél elkezdi a tesztelési meghajtót. Általában a Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja (például: `https://testdrive.crm.dynamics.com` ).

- **Példány webes API URL-címe** (kötelező) – a Dynamics 365-példány webes API URL-címének beolvasása a Microsoft 365 fiókba való bejelentkezéssel és a **Beállítások** \& gt; **Testreszabás** \& gt **Fejlesztői erőforrások** \& gt A **példány webes API-ját (a szolgáltatás gyökerének URL-címe)** másolja az itt található URL-címet (például: `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Szerepkör neve** (kötelező) – adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét, amely a felhasználóhoz lesz hozzárendelve a tesztelési meghajtón (például Test-Drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technikai konfiguráció a Logic app Test Drive-hoz

Minden egyéni terméknek ezt a típusú tesztelési meghajtó-telepítési sablont kell használnia, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyetlen kiválasztható legördülő lista) – jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztvezetés elérhetővé tehető. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. A legjobb módszer a logikai alkalmazás helyi üzembe helyezése az Azure-előfizetésben a portálon, és annak ellenőrzése, hogy megfelelően működik-e a kijelölés előtt.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező) – állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező) adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, ahová a rendszer menti a logikai alkalmazás tesztelési meghajtóját.

- **Azure Logic App-alkalmazás neve** (kötelező) – Itt adhatja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót hozzárendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének** megszüntetése (kötelező) – Itt adhatja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót kiépíti az ügyfél befejeződése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI tesztelési meghajtókhoz nem szükséges technikai konfiguráció

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. További információ a [Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) template-alkalmazások beállításáról.

### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Ha a tesztelési meghajtót az Ön nevében szeretné üzembe helyezni, hozzon létre és adjon meg egy különálló és egyedi Azure-előfizetést. (Power BI tesztelési meghajtók esetében nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges) – adja meg az előfizetés azonosítóját, hogy hozzáférést biztosítson az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](https://docs.microsoft.com/azure/billing/billing-create-subscription) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztásával megjelenítheti az előfizetés-AZONOSÍTÓját (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező) – adja meg a Azure Active Directory (ad) [bérlői azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a * * tulajdonságok elemet, majd keresse **meg a listában** szereplő 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév címe alapján is megkeresheti a következő helyen: [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges) – adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad** -alkalmazás azonosítója (kötelező) – adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-ügyfél titkos kulcsa** (kötelező) – adja meg az Azure ad-alkalmazás [ügyfél-titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. (Ne navigáljon el az oldalról, mielőtt ezt megtenné, különben nem lesz hozzáférése az értékhez.)

### <a name="test-drive-marketplace-listings"></a>A Marketplace-beli piactér-listák tesztelése

A **Test Drive** (Piactér) lapon található **Marketplace listázási** lehetőséggel megadhatja a test Drive-élmény részleteit.

> [!NOTE]
> A tesztelési meghajtóra vonatkozó adatokat csak egy nyelven lehet megadni. Nem kell angol nyelven lennie, amíg az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] nyelven érhető el." Az is elfogadható, ha a *Súgó hivatkozás URL-címét* olyan nyelven ajánljuk, amely nem a tesztelési meghajtón használt tartalom.

- **Leírás** (kötelező) – írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célkitűzéseket, a felderített funkciókat, valamint minden olyan releváns információt, amely segít a felhasználónak eldönteni, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges) – megtudhatja, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező) – részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók** (nem kötelező) – a videókat feltöltheti a YouTube-ra vagy a Vimeo-ra, és itt hivatkozhat egy hivatkozással és egy miniatűr képpel (533 x 324 képpont), így az ügyfelek megtekinthetik az információk áttekintését, így könnyebben megismerhetik a tesztelési meghajtót, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyvek megismerését.
  - **Név** (kötelező)
  - **URL-cím (csak YouTube vagy Vimeo)** (kötelező)
  - **Miniatűr** képe (a fájlnak PNG formátumban kell lennie, és 533 x 324 px)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat adhat meg az ajánlatáról, hogy segítsen az ajánlat érvényesítésében. Ezek az információk nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="target-release"></a>Cél kiadása

Jelezze, hogy a Microsoft Dynamics üzleti központjának melyik kiadását célozza meg a megoldás céljai: **current**, **Next Major**vagy **Next Minor**. Ez az információ lehetővé teszi a megoldás megfelelő tesztelését.

### <a name="supported-editions"></a>Támogatott kiadások

Ha az ajánlata a Microsoft Dynamics 365 Business Central Premium kiadását igényli, válassza a **prémium** szintű lehetőséget. Ellenkező esetben válassza a mind az **Essentials** , mind a **prémium**lehetőséget.

### <a name="key-usage-scenario"></a>Kulcshasználat forgatókönyv

Fel kell töltenie egy PDF-fájlt, amely felsorolja az ajánlat legfontosabb használati forgatókönyveit a dokumentumban (. PDF formátumban). Az itt felsorolt összes forgatókönyvet ellenőrizni kell az ellenőrzési csapatnál, mielőtt jóváhagyjuk ajánlatát a piactéren.

### <a name="app-tests-automation"></a>Alkalmazás-tesztek automatizálása

Ha az ajánlat egy kiegészítő alkalmazás, fel kell töltenie egy alkalmazás- **teszt Automation** -fájlt (. app). Ez a fájl nem alkalmazható az alkalmazások összekapcsolására.

### <a name="test-accounts"></a>Fiókok tesztelése

Ha tesztelési fiókra van szükség ahhoz, hogy a minősítési csapat megfelelően áttekintse az ajánlatát, töltsön fel egy. pdf,. doc vagy. docx fájlt a **tesztelési fiókokkal** kapcsolatos információkkal.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután befejezte az ajánlat összes szükséges szakaszt, válassza a **Közzététel** lehetőséget a portál jobb felső sarkában. A rendszer átirányítja a **felülvizsgálat és közzététel** lapra. 

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - *Nincs elindítva* – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
    - *Hiányos* – azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információkat kell megadni. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
    - *Complete (Befejezés* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- A **minősítési megjegyzések** szakaszban adja meg a minősítési csoport tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez szükséges kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. A rendszer e-mailt küld Önnek, ha az ajánlat előzetes verziója elérhető az Ön számára az áttekintéshez és a jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvános közzétételére (vagy ha egy privát ajánlatra a privát közönség számára).

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
