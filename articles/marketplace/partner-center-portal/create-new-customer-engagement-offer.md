---
title: Dynamics 365 létrehozása a Customer engagement & PowerApps ajánlathoz a Microsoft kereskedelmi piactéren
description: Új Dynamics 365 létrehozása a Customer engagement & PowerApps az Azure Marketplace-en, a AppSource-on vagy a partner centeren keresztül a Cloud Solution Provider (CSP) programon keresztül történő listázásához és értékesítéséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: bcb14c786ab652b1c51a3d3bf487e39fd0ebfdb6
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710685"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Dynamics 365 for Customer Engagement- és PowerApps-ajánlat létrehozása

Ez a témakör azt ismerteti, hogyan hozhat létre új Dynamics 365-t az ügyfelek engagement & PowerApps ajánlatához. A Dynamics 365 for Customer engagement (PowerApps, Sales, Service, Project Service és Field Service) összes alkalmazásának a minősítési folyamaton keresztül kell haladnia, és támogatni kell a próbaidőszakot. A minősítési folyamat a standard követelmények, a kompatibilitás és a megfelelő eljárások tekintetében ellenőrzi a megoldást. A próbaverzió lehetővé teszi, hogy a felhasználók egy élő Dynamics 365-környezetbe telepítse a megoldást.

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Dynamics 365 lehetőséget a Customer engagement & PowerApps**.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Az ajánlat közzétételét követően a csak az ajánlat újbóli közzététele után az online áruházakban megjelenő szerkesztési lehetőségek jelennek meg a partner Centerben. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- A közzétevő AZONOSÍTÓval összevont ajánlat AZONOSÍTÓjának 40 karakternél rövidebbnek kell lennie.
- Csak kisbetűket és számokat használjon. Kötőjeleket és aláhúzást tartalmazhat, de nem tartalmaz szóközt. Ha például a közzétevő azonosítója testpublisherid, és a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
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

Ajánlatát ingyenesen kilistázhatja az ügyfeleknek.

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (Listázás)

Az ingyenes próbaverzióra mutató hivatkozást tartalmazó ajánlat listázása az ügyfeleknek. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak Azure Active Directory (Azure AD) használatával szerezhetik be a felhasználói adatokat, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a jogkivonat használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolatfelvétel

Az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával Gyűjtse össze az ügyfelek kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [ügyfél-érdeklődők](#customer-leads).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

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
7. Description
8. Képernyőképek/videók

### <a name="name"></a>Név

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat rövid leírását, amely legfeljebb 100 karakter hosszú lehet. Ez a leírás a piactér keresési eredményeiben használható.

### <a name="description"></a>Description

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

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** embléma létrehozásához. Ezt később is lecserélheti egy másik rendszerképpel.

- **Nagyméretű** (216 x 216 és 350 x 350 px, kötelező)
- **Kicsi** (48 x 48 px, opcionális)

Ezeket az emblémákat a lista különböző helyein használják:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legalább egy képernyőképre van szükség, és legfeljebb öt adható hozzá. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár négy videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

[Ajánlott eljárások a piactér ajánlati listáihoz](../gtm-offer-listing-best-practices.md)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

Ezen a lapon megadhatja, hogy hol és hogyan szeretné elérhetővé tenni az ajánlatot.

### <a name="markets"></a>Piacok

Adja meg azokat a piacokat, amelyekben ajánlatának elérhetőnek kell lennie. Ehhez válassza a **piacok szerkesztése**lehetőséget, amely megjeleníti a **piac kiválasztási** felugró ablakát.

Alapértelmezés szerint egyetlen piac sincs kiválasztva, de ki kell választania legalább egy piacot az ajánlat közzétételéhez. Az  **összes kijelölése** lehetőség kiválasztásával elérhetővé teheti az ajánlatot minden lehetséges piacon, vagy kiválaszthatja a felvenni kívánt adott piacokat.

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

Ebben a szakaszban válassza a **+ régió hozzáadása** lehetőséget, hogy megadja azokat a földrajzi régiókat, amelyekben a CRM-csomag elérhető lesz az ügyfelek számára. A következő szuverén régiókban való üzembe helyezéshez különleges engedélyek és érvényesítés szükséges a minősítési folyamat során: [Németország](../../germany/index.yml), [USA kormányzati felhője](../../azure-government/documentation-government-welcome.md)és tipp.

Alapértelmezés szerint az egyes régiókban a fentiekben megadott **konfigurációs URL-cím** lesz használva. Ha szeretné, megadhat egy külön alkalmazás-konfigurációs URL-címet egy vagy több meghatározott régióhoz. 

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive-technical-configuration"></a>Tesztverzió műszaki konfigurálása

Ezen a lapon megadható egy bemutató ("tesztelési meghajtó"), amely lehetővé teszi, hogy az ügyfelek a vásárlás előtt kipróbálják az ajánlatot. További információ: [Mi a test Drive](../what-is-test-drive.md).

A tesztelési meghajtó engedélyezéséhez jelölje be a **Test Drive engedélyezése** jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

A tesztelési meghajtó beállítása után a folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

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
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat számára a nyilvános közzétételhez.

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
