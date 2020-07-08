---
title: A Dynamics 365 for Operations ajánlat létrehozása a kereskedelmi piactéren
description: Új Dynamics 365 for Operations ajánlat létrehozása az Azure Marketplace-en, a AppSource-on vagy a Cloud Solution Provider (CSP) programon keresztül a Microsoft partner Center kereskedelmi piactér portálján.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 0a2696da70a785fc1da6bfd2c7b0cabae1a89197
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980692"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations-ajánlat létrehozása

Ez a témakör bemutatja, hogyan hozhat létre új Dynamics 365 for Operations ajánlatot. [A Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) egy vállalati erőforrás-tervezési (ERP) szolgáltatás, amely támogatja a fejlett pénzügyi, üzemeltetési, gyártási és ellátási láncok kezelését. A Dynamics 365 for Operations összes ajánlatának a minősítési folyamaton keresztül kell haladnia.

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

>[!NOTE]
> Ha egy ajánlat közzé lett téve, a partner Centerben végzett ajánlat szerkesztése csak a rendszeren történik meg, és az újbóli közzététel után tárolja az előfizetéseket. Győződjön meg arról, hogy a módosítások elvégzése után elküldi az ajánlatot közzétételre.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Dynamics 365 for Operations**lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végzett módosítások csak az ajánlat ismételt közzététele után jelennek meg a kirakatokban. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1** értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hogyan kívánja használni a potenciális ügyfeleket a jelen tőzsdei ajánlathoz?

Válassza ki az ajánlathoz használni kívánt beállítást.

#### <a name="get-it-now-free"></a>Letöltés (ingyenes)

Az ajánlatát ingyenesen listázhatja, ha érvényes URL-címet ad meg (a *http* vagy a *https*-től kezdve), ahol elérheti az alkalmazást.  Például: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)

Az ajánlatot az ingyenes próbaverzióra mutató hivatkozással listázhatja, ha egy érvényes URL-címet biztosít (a vagy a verziótól kezdve `http` `https` ), ahol próbaverziót kaphat. Például: `https://contoso.com/trial/my-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak Azure Active Directory (Azure AD) használatával szerezhetik be a felhasználói adatokat, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a jogkivonat használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolatfelvétel

Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [ügyfél-érdeklődők](#customer-leads).

### <a name="test-drive"></a>Tesztelési meghajtó

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt kipróbálhatja a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. További információ: [Mi a test Drive](../what-is-test-drive.md).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

### <a name="customer-leads"></a>Ügyfél-érdeklődők

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren, az alkalmazás verziójában és az ajánlatát támogató jogi szerződésekben való csoportosításához használt kategóriákat és iparágakat.

### <a name="category"></a>Kategória

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a piactér megfelelő keresési területein helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható**lehetőséget.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Iparág

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek ezt a verziót fogják látni az ajánlat részleteit tartalmazó oldalon.

### <a name="terms-and-conditions"></a>használati feltételei

A **feltételek és** kikötések mezőben adja meg a saját jogi feltételeit. Megadhatja azt az URL-címet is, ahol a feltételek és kikötések megtalálhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Ezen a lapon láthatók azok a nyelvek, amelyekben az ajánlat fel lesz sorolva. Jelenleg az egyetlen elérhető lehetőség az **angol (Egyesült Államok)** .

Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, képeit stb.) az egyes nyelvekhez/piacokhoz. Válassza ki a nyelv/piac nevét az információ megadásához.

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek, a használati feltételek stb.) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] verzióban érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Termékek
3. Kategóriák
4. Ágazatok
5. Támogatási címe (hivatkozás)
6. Használati feltételek
7. Adatvédelmi irányelvek
8. Ajánlat neve
9. Képernyőképek/videók
10. Description

### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat rövid leírását, amely legfeljebb 100 karakter hosszú lehet. Ez a leírás a piactér keresési eredményeiben használható.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

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

### <a name="contacts"></a>Kapcsolattartók

Ebben a szakaszban adja meg a **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ezek az információk nem jelennek meg az ügyfelek számára, de a Microsoft számára elérhetővé válnak, és a CSP-partnerek számára is megadhatók.

A **támogatási kapcsolattartó** szakaszban adja meg a **támogatási URL-címet** , ahol a CSP-partnerek az ajánlathoz támogatást kaphatnak.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy (és legfeljebb három) kapcsolódó marketing-dokumentumot, például a tanulmányokat, a brosúrákat, a feladatlistákat és a bemutatókat. A dokumentumoknak PDF formátumúnak kell lenniük.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Ebben a szakaszban megadhatja azokat az emblémákat és képeket, amelyeket az ajánlat vásárlónak való megjelenítésekor használni fog. Minden képnek. png formátumúnak kell lennie.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlat emblémáját két képpontos méretben:

- **Kicsi** (48 x 48)
- **Nagyméretű** (216 x 216)


#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legalább egy képernyőképre van szükség, és legfeljebb öt adható hozzá. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár négy videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

[Ajánlott eljárások a piactér ajánlati listáihoz](../gtm-offer-listing-best-practices.md)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

Ezen az oldalon megtudhatja, hol és hogyan teheti elérhetővé az ajánlatát.

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

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Ez az oldal az ajánlathoz való kapcsolódáshoz használt technikai részleteket határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék.

### <a name="solution-identifier"></a>Megoldás azonosítója

Adja meg a megoldás azonosítóját (GUID).

A megoldás azonosítójának megkeresése:

1. A Microsoft Dynamics Lifecycle Services (LCS) szolgáltatásban válassza a **megoldás-kezelés**lehetőséget.
2. Válassza ki a megoldást, majd keresse meg a **megoldás azonosítóját** a **csomag áttekintésében**. Ha az azonosító üres, válassza a **Szerkesztés** lehetőséget, és tegye közzé újra a csomagot, majd próbálkozzon újra.

### <a name="release-version"></a>Kiadás verziója

Válassza ki a Dynamics 365-verziót a Pénzügy és a megoldás által használható műveletek számára.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive-technical-configuration"></a>Tesztverzió műszaki konfigurálása

Ezen a lapon megadható egy bemutató ("tesztelési meghajtó"), amely lehetővé teszi, hogy az ügyfelek a vásárlás előtt kipróbálják az ajánlatot. További információ: [Mi a test Drive](../what-is-test-drive.md).

A tesztelési meghajtó engedélyezéséhez jelölje be a **Test Drive engedélyezése** jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

A tesztelési meghajtó beállítása után a folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat adhat meg az ajánlatáról, hogy segítsen az ajánlat érvényesítésében. Ezek az információk nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="validation-assets"></a>Érvényesítési eszközök

Töltse fel a [testreszabási elemzési jelentést (Car)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) ebben a szakaszban. Ez a jelentés a testreszabási és bővítményi modellek elemzésével jön létre, az ajánlott eljárási szabályok előre meghatározott készlete alapján.

Ennek a fájlnak. xls vagy. xlsx formátumúnak kell lennie. Ha több jelentés is van, feltöltheti az összes jelentést tartalmazó. zip-fájlt.

### <a name="does-solution-include-localizations"></a>A megoldás tartalmaz-e honosítást?

Válassza az **Igen** lehetőséget, ha a megoldás lehetővé teszi a helyi szabványok és házirendek használatát (például ha a különböző, különböző országokban/régiókban megkövetelt bérlista-szabályokat tartalmazza). Egyéb esetben a **Nem** választógombot jelölje be.

### <a name="does-solution-enable-translations"></a>Engedélyezi a megoldás a fordításokat?

Válasz **Igen** , ha a megoldás szövege más nyelvekre is fordítható. Egyéb esetben a **Nem** választógombot jelölje be.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután elvégezte az ajánlat összes szükséges részét, válassza a **felülvizsgálat és közzététel** elemet a portál jobb felső sarkában.

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - **Nincs elindítva** – a szakasz nincs megérintve, és el kell végezni.
    - **Hiányos** – a szakasznak olyan hibákkal kell rendelkeznie, amelyeket meg kell oldani, vagy további információkat kell megadnia. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
    - **Complete (Befejezés** ) – a szakasz elkészült, az összes szükséges adattal megadták, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- A **minősítési megjegyzések** szakaszban adja meg a minősítési csoport tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez szükséges kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvánosságra való közzétételéhez.

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
