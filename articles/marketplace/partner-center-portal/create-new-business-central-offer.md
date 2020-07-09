---
title: Dynamics 365 Business Central-ajánlat létrehozása – Microsoft kereskedelmi piactér
description: Ismerje meg az új Dynamics 365 Business Central-ajánlat létrehozásának lépéseit és szempontjait a partner Center kereskedelmi piactér portálján. Az ajánlatot az Azure Marketplace-en vagy a Cloud Solution Provider (CSP) programon keresztül is listázhatja vagy értékesítheti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 456764ae2b08b9852e56b2ab3f7cd9238b540b58
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121945"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Microsoft Dynamics 365 Business Central-ajánlat létrehozása

Ez a cikk azt ismerteti, hogyan hozható létre új Dynamics 365 Business Central-ajánlat. A [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) egy vállalati erőforrás-tervezési (ERP) rendszer, amely az üzleti folyamatok széles körét kezeli, többek között a pénzügy, a műveletek, az ellátási lánc, a CRM, a projektmenedzsment és az elektronikus kereskedelem terén. A Prémium csomag a klasszikus üzembe helyezési modellt és a gyártást is támogatja. A Dynamics 365 Business Central szolgáltatáshoz készült összes ajánlatnak a minősítési folyamaton keresztül kell haladnia.

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Dynamics 365 Business Central**lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-dynamics-365-business-central.png)

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

Az ajánlatot az ingyenes próbaverzióra mutató hivatkozással listázhatja, ha egy érvényes URL-címet biztosít (a vagy a verziótól kezdve `http` `https` ), ahol próbaverziót kaphat.  Például: `https://contoso.com/trial/my-app`. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

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

Itt adhatja meg az ajánlat részleteit, például a nevet, a leírást és a képeket.

> [!NOTE]
> Az ajánlat részletei csak egy nyelven adhatók meg. Nem kell angol nyelven lennie, amíg az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] nyelven érhető el." Azt is elfogadható, hogy a *Súgó hivatkozás URL-címe* , amely az ajánlati tartalomban használt tartalomtól eltérő nyelven kínál tartalmat.

Íme egy példa arra, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatos célokat szolgálnak):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Termékek
3. Kategóriák
4. Támogatási címe (hivatkozás)
5. Használati feltételek
6. Adatvédelmi irányelvek
7. Ajánlat neve
8. Összefoglalás
9. Leírás
10. Képernyőképek/videók

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

[Ajánlott eljárások a piactér ajánlati listáihoz](../gtm-offer-listing-best-practices.md)

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

## <a name="test-drive-technical-configuration"></a>Tesztverzió műszaki konfigurálása

Ezen a lapon megadható egy bemutató ("tesztelési meghajtó"), amely lehetővé teszi, hogy az ügyfelek a vásárlás előtt kipróbálják az ajánlatot. További információ: [Mi a test Drive](../what-is-test-drive.md).

A tesztelési meghajtó engedélyezéséhez jelölje be a **Test Drive engedélyezése** jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

A tesztelési meghajtó beállítása után a folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

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
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. A rendszer e-mailt küld Önnek, ha az ajánlat előzetes verziója elérhető az Ön számára az áttekintéshez és a jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvánosságra való közzétételéhez.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
