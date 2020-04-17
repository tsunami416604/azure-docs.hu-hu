---
title: Gyakori kérdések (GYIK) - LUIS
description: Ez a cikk a nyelvi ismeretekkel (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 59d9b1f21e1936b7d03293ec3d338677380a7c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530232"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Nyelvismertetés – gyakori kérdések

Ez a cikk a nyelvi ismeretekkel (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="whats-new"></a>Újdonságok

[További információ](whats-new.md) a nyelvi ismeretek (LUIS) újdonságairól.

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tartalomkészítés

### <a name="what-are-the-luis-best-practices"></a>Melyek a LUIS ajánlott eljárások?
Kezdje a [szerzői ciklussal](luis-concept-app-iteration.md), majd olvassa el az [ajánlott eljárásokat](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Mi a legjobb módja az alkalmazás létrehozásának megkezdéséhez a LUIS-ban?

Az alkalmazás létrehozásának legjobb módja egy [növekményes folyamat.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Mi a helyes gyakorlat az alkalmazásom szándékainak modellezésére? Hozzak létre konkrétabb vagy általánosabb szándékokat?

Válasszon olyan leképezéseket, amelyek nem olyan általánosak, hogy átfedésben legyenek, de nem annyira specifikusak, hogy a LUIS számára megnehezíti a hasonló szándékok megkülönböztetését. Diszkriminatív konkrét leképezések létrehozása az egyik ajánlott eljárás a LUIS modellezés.

### <a name="is-it-important-to-train-the-none-intent"></a>Fontos, hogy a vonat a Nincs szándék?

Igen, jó betanítása a **Nincs** szándék több utterances, ahogy további címkéket ad hozzá más szándékok. A jó arány 1 vagy 2 címke hozzáadva **a Nincs** értékhez minden 10, a szándékhoz hozzáadott címke után. Ez az arány növeli a LUIS megkülönböztető erejét.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hogyan javíthatom ki a helyesírási hibákat a kimondott szövegben?

Tekintse meg a [Bing Helyesírás-ellenőrző API V7](luis-tutorial-bing-spellcheck.md) oktatóanyagát. A LUIS kényszeríti a Bing Helyesírás-ellenőrző API V7 által előírt korlátozásokat.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hogyan szerkeszthetem a LUIS-alkalmazást programozott módon?
A LUIS-alkalmazás programozott szerkesztéséhez használja a [Szerzői API-t.](https://go.microsoft.com/fwlink/?linkid=2092087) Lásd: [Luis szerzői API hívása](./get-started-get-model-rest-apis.md) és [a Luis-alkalmazás létrehozása programozott módon a Node.js használatával](./luis-tutorial-node-import-utterances-csv.md) című témakörben példákat talál a Szerzői API hívására. A szerzői API-t kell használni [a szerzői kulcs](luis-concept-keys.md#azure-resources-for-luis) helyett egy végpontkulcsot. A programozott szerzői műveletek havonta legfeljebb 1 000 000 hívást és másodpercenként öt tranzakciót tesznek lehetővé. A LUIS-szal használt kulcsokról a [Kulcsok kezelése című](./luis-concept-keys.md)témakörben talál további információt.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Hol található a Minta szolgáltatás, amely reguláris kifejezés egyezést biztosított?
Az előző **Minta funkció** jelenleg elavult, helyébe **[minták](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hogyan használhatok entitást a megfelelő adatok kihúzására?
Lásd [az entitásokat](luis-concept-entity-types.md) és [az adatok kinyerését.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Egy példa utterance (kifejezés) változatok tartalmaznak írásjeleket?
Használja az alábbi megoldások egyikét:
* [Írásjelek](luis-reference-application-settings.md#punctuation-normalization) figyelmen kívül hagyása
* Adja hozzá a különböző változatokat példaként a szándékhoz.
* Adja hozzá a példa utterance (kifejezés) mintáját a [szintaxissal az](luis-concept-patterns.md#pattern-syntax) írásjelek figyelmen kívül hagyásához.

### <a name="does-luis-currently-support-cortana"></a>A LUIS jelenleg támogatja Cortanát?

A Cortana előre elkészített alkalmazásai 2017-ben elavultak. Ezek már nem támogatottak.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hogyan ruházhatok át egy LUIS-alkalmazás tulajdonjogát?
Ha egy LUIS-alkalmazást át szeretne vinni egy másik Azure-előfizetésbe, exportálja a LUIS alkalmazást, és importálja egy új fiókkal. Frissítse a LUIS-alkalmazásazonosítót az azt minősítő ügyfélalkalmazásban. Az új alkalmazás az eredeti alkalmazástól kissé eltérő LUIS-pontszámokat adhat vissza.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Egy előre összeállított entitás van címkézve egy példa utterance (kifejezés) az egyéni entitás helyett. Hogyan tudom ezt helyrehozni?

A LUIS-portálon címkézheti a szöveget a pontos entitás, amely et ki szeretne vonni. Ha a LUIS-portál nem jeleníti meg a megfelelő entitás-előrejelzést, előfordulhat, hogy további kimondott szöveget kell hozzáadnia, és fel kell címkéznie az entitást a szövegen belül, vagy hozzá kell adnia egy leírót (például egy jellemzőt).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Megpróbáltam importálni egy alkalmazást vagy verziófájlt, de hibaüzenetet kaptam, mi történt?

További információ a [verzióimportálási hibákról](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Együttműködés és közreműködés

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Hogyan adhatok hozzáférést a közreműködőknek a LUIS-hoz az Azure Active Directory (Azure AD) vagy a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével?

Tekintse meg [az Azure Active Directory-erőforrások](luis-how-to-collaborate.md#azure-active-directory-resources) és [az Azure Active Directory bérlői felhasználó,](luis-how-to-collaborate.md#azure-active-directory-tenant-user) hogyan adhat hozzáférést a közreműködők.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Végpont

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>HTTP 403-as hibaállapot-kódot kaptam. Hogyan lehet kijavítani a hibát?

403-as és 429-es hibaállapot-kódokat kap, ha túllépi a másodpercenkénti tranzakciókat vagy a tarifacsomag havonta lebonyolított tranzakcióit. Növelje a tarifacsomagot, vagy használja a Nyelvtudás [tárolókat.](luis-container-howto.md)

Ha az összes ingyenes 1000 végpontlekérdezést használja, vagy túllépi a tarifacsomag havi tranzakciókvótáját, http 403-as hibaállapot-kódot kap.

A hiba megoldásához vagy magasabb szintre kell [módosítania a tarifacsomagot,](luis-how-to-azure-subscription.md#change-pricing-tier) vagy létre kell [hoznia egy új erőforrást,](get-started-portal-deploy-app.md#create-the-endpoint-resource) és [hozzá kell rendelnie az alkalmazáshoz.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)

A hiba megoldásai a következők:

* Az [Azure Portalon,](https://portal.azure.com)a nyelvtudás erőforrás, az **erőforrás-kezelés -> tarifacsomag,** módosítsa a tarifacsomagot egy magasabb TPS-szint. Nem kell semmit tennie a Nyelvmegértés portálon, ha az erőforrás már hozzá van rendelve a Nyelvmegértés alkalmazáshoz.
*  Ha a használat meghaladja a legmagasabb tarifacsomagot, adjon hozzá további nyelvi megértési erőforrásokat egy terheléselosztóval előttük. A [Language Understanding tároló](luis-container-howto.md) Kubernetes vagy Docker Compose segíthet ebben.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>HTTP 429-es hibaállapot-kódot kaptam. Hogyan lehet kijavítani a hibát?

403-as és 429-es hibaállapot-kódokat kap, ha túllépi a másodpercenkénti tranzakciókat vagy a tarifacsomag havonta lebonyolított tranzakcióit. Növelje a tarifacsomagot, vagy használja a Nyelvtudás [tárolókat.](luis-container-howto.md)

Ez az állapotkód akkor kerül visszaadásra, ha a másodpercenkénti tranzakciók meghaladják a tarifacsomagot.

A megoldások a következők:

* Növelheti [a tarifacsomagot,](luis-how-to-azure-subscription.md#change-pricing-tier)ha nem a legmagasabb szinten van.
* Ha a használat meghaladja a legmagasabb tarifacsomagot, adjon hozzá további nyelvi megértési erőforrásokat egy terheléselosztóval előttük. A [Language Understanding tároló](luis-container-howto.md) Kubernetes vagy Docker Compose segíthet ebben.
* Az ügyfélalkalmazás-kérelmek et egy [újrapróbálkozási szabályzattal](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) biztosíthatja, amelyet saját maga valósít meg, amikor megkapja ezt az állapotkódot.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>A végponti lekérdezés nem várt eredményt adott vissza. Mit tegyek?

A nem várt lekérdezés-előrejelzési eredmények a közzétett modell állapotán alapulnak. A modell javításához szükség lehet a modell módosítására, a betanításra és a közzététel újbóli közzétételére.

A modell javítása az [aktív tanulással](luis-how-to-review-endpoint-utterances.md)kezdődik.

Eltávolíthatja a nem determinisztikus betanítást az [alkalmazás verzióbeállítási API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) frissítésével az összes betanítási adat használatához.

Tekintse át az egyéb tippekért az [ajánlott eljárásokat.](luis-concept-best-practices.md)

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Miért a LUIS szóközöket ad a lekérdezéshez a szavak körül vagy közepén?
A LUIS [tokenizes az](luis-glossary.md#token) utterance (kifejezés) a [kultúra](luis-language-support.md#tokenization)alapján. Mind az eredeti érték, mind a tokenizált érték elérhető az [adatok kinyeréséhez.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hogyan hozhatok létre és rendelhetek hozzá LUIS-végpontkulcsot?
[Hozza létre a végpontkulcsot az](luis-how-to-azure-subscription.md) Azure-ban a [szolgáltatási](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) szinthez. [Rendelje hozzá a kulcsot](luis-how-to-azure-subscription.md) az **[Azure Resources](luis-how-to-azure-subscription.md)** lapon. Ehhez a művelethez nincs megfelelő API. Ezután módosítania kell a HTTP-kérelmet a végpontra [az új végpontkulcs használatához.](luis-concept-keys.md)

### <a name="how-do-i-interpret-luis-scores"></a>Hogyan értelmezhetem a LUIS-pontszámokat?
A rendszernek a legmagasabb pontozási szándékot kell használnia, függetlenül annak értékétől. Például 0,5 alatti pontszám (kevesebb mint 50%) nem feltétlenül jelenti azt, hogy a LUIS alacsony megbízhatóságú. További betanítási adatok megadása segíthet a legvalószínűbb szándék [pontszámának](luis-concept-prediction-score.md) növelésében.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Miért nem látom a végpontlekéréseimet az alkalmazás irányítópultján?
Az alkalmazás irányítópultján található végpontlekérések rendszeresen frissülnek, de az Azure Portalluis végpontkulcsához társított metrikák gyakrabban frissülnek.

Ha nem látja a frissített végpontlekéréseket az irányítópulton, jelentkezzen be az Azure Portalra, és keresse meg a LUIS-végpontkulcshoz társított erőforrást, és nyissa meg a **Metrikák at** az **Összes hívás** metrika kiválasztásához. Ha a végpontkulcsot egynél több LUIS-alkalmazáshoz használja, az Azure Portalon lévő metrika az azt használó összes LUIS-alkalmazás hívásainak összesített számát jeleníti meg.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Van egy PowerShell-parancs a végpontkvótához?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Végpontkvóta megtekintéséhez powershell-paranccsal is megtekintheti a végpontkvótát:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Saját LUIS app működött tegnap, de ma kapok 403 hibákat. Nem változtattam meg az alkalmazást. Hogyan lehet kijavítani a hibát?
Kövesse az [alábbi utasításokat](#how-do-i-create-and-assign-a-luis-endpoint-key) a LUIS-végpontkulcs létrehozásához és az alkalmazáshoz való hozzárendeléséhez. Ezután módosítania kell az ügyfélalkalmazás HTTP-kérelmét a végpontra [az új végpontkulcs használatához.](luis-concept-keys.md) Ha új erőforrást hozott létre egy másik régióban, módosítsa a HTTP-ügyfélkérelem régióját is.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hogyan biztonságos a LUIS-végpont?
Lásd: [A végpont védelme](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>A LUIS határain belüli munka

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Mi a leképezések és entitások maximális száma, amelyet egy LUIS-alkalmazás támogathat?
Lásd a [határok](luis-boundaries.md) hivatkozás.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Szeretnék egy LUIS-alkalmazást a leképezések maximális számánál többsel. Mit tegyek?

Lásd: [Gyakorlati tanácsok a leképezések.](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Szeretnék egy alkalmazást a LUIS több, mint az entitások maximális száma. Mit tegyek?

Lásd: [Gyakorlati tanácsok entitásokhoz](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Milyen korlátok vonatkoznak a kifejezéslisták számára és méretére vonatkozóan?
A [kifejezéslista](./luis-concept-feature.md)maximális hosszát lásd a [határok](luis-boundaries.md) hivatkozásában.

### <a name="what-are-the-limits-on-example-utterances"></a>Milyen korlátok vonatkoznak a példakimondott szövegre vonatkozóan?
Lásd a [határok](luis-boundaries.md) hivatkozás.

## <a name="testing-and-training"></a>Tesztelés és képzés

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Az alkalmazás egyes modelljeinek kötegelt tesztelési ablaktáblájában néhány hiba jelenik meg. Hogyan oldhatom meg ezt a problémát?

A hibák azt jelzik, hogy van némi eltérés a címkék és a modellek előrejelzései között. A probléma megoldásához tegye az alábbi feladatok egyikét vagy mindkettőt:
* A LUIS-nak a szándékok közötti megkülönböztetés javítása érdekében adjon hozzá további címkéket.
* Annak érdekében, hogy a LUIS gyorsabban tanulhasson, adjon hozzá kifejezéslista-funkciókat, amelyek tartományspecifikus szókincset vezetnek be.

Tekintse meg a [Kötegelt tesztelés](luis-tutorial-batch-testing.md) oktatóanyagot.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Amikor egy alkalmazást exportál, majd újra importál egy új alkalmazásba (egy új alkalmazásazonosítóval), a LUIS előrejelzési pontszámok eltérőek. Miért történik ez?

Lásd: [Előrejelzési különbségek ugyanazon alkalmazás példányai között.](luis-concept-prediction-score.md#review-intents-with-similar-scores)

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Néhány kimondott szöveg rossz szándékkal történik, miután módosítottam az alkalmazást. A kérdés úgy tűnik, hogy eltűnik véletlenszerűen. Hogyan lehet kijavítani a hibát?

Lásd: [Vonat az összes adattal.](luis-how-to-train.md#train-with-all-data)

## <a name="app-publishing"></a>Alkalmazás közzététele

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Mi a bérlői azonosító a "Kulcs hozzáadása az alkalmazáshoz" ablakban?
Az Azure-ban a bérlő képviseli a szolgáltatáshoz társított ügyfelet vagy szervezetet. Keresse meg a bérlői azonosítót az Azure Portalon a **Címtárazonosító** mezőben az **Azure Active** > **Directory-kezelés** > **tulajdonságai kiválasztásával.**

![Bérlőazonosító az Azure Portalon](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Miért van több végpontkulcs hozzárendelve az alkalmazásomhoz, mint én?
Minden LUIS-alkalmazás rendelkezik a szerzői/kezdő kulcs a végpontlistában a kényelem érdekében. Ez a kulcs csak néhány végpontlekérést tesz lehetővé, így kipróbálhatja a LUIS-t.

Ha az alkalmazás létezett, mielőtt LUIS általánosan elérhető (GA), LUIS végpontkulcsok az előfizetésben automatikusan hozzá van rendelve. Ez azért történt, hogy megkönnyítse a GA-áttelepítést. Az Azure Portalon lévő új LUIS-végpontkulcsok _nem_ lesznek automatikusan hozzárendelve a LUIS-hoz.

## <a name="key-management"></a>Kulcskezelés

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Honnan tudhatom, hogy milyen kulcsra van szükségem, hol kapom meg, és mit csinálok vele?

[A SZERZŐI és lekérdezési előrejelzési végpontkulcsok a LUIS-ban](luis-concept-keys.md) a szerzői kulcs és az előrejelzési futásidejű kulcs közötti különbségek megismerése.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Van egy hiba, hogy ki a kvótát. Hogyan lehet kijavítani a hibát?

További információért tekintse meg a [403-as](#i-received-an-http-403-error-status-code-how-do-i-fix-it) és [429-es](#i-received-an-http-429-error-status-code-how-do-i-fix-it) HTTP-állapotkód javítását.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>További végpontlekérdezéseket kell kezelnem. Mit kell tennem ehhez?

További információért tekintse meg a [403-as](#i-received-an-http-403-error-status-code-how-do-i-fix-it) és [429-es](#i-received-an-http-429-error-status-code-how-do-i-fix-it) HTTP-állapotkód javítását.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Létrehoztam egy szerzői kulcsot, de az nem jelenik meg a LUIS-portálon. Mi történt?

A szerzői kulcsok a LUIS-portálon érhetők el [a szerzői kulcsélménybe való áttelepítés](luis-migration-authoring.md)után.

## <a name="app-management"></a>Alkalmazáskezelés

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hogyan tölthetek le a felhasználói utterances naplóját?
Alapértelmezés szerint a LUIS-alkalmazás naplózza a felhasználók utterances. A felhasználók által a LUIS-alkalmazásnak küldött kimondott szövegnapló letöltéséhez nyissa meg a **Saját alkalmazások**lehetőséget, és válassza ki az alkalmazást. A környezetfüggő eszköztáron válassza a **Végpontnaplók exportálása**lehetőséget. A napló vesszővel tagolt értékfájlként (CSV) van formázva.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hogyan tudom letiltani a kimondott szöveg naplózását?
Kikapcsolhatja a felhasználói utterances naplózását `log=false` az Endpoint URL-címének beállításával, amelyet az ügyfélalkalmazás a LUIS lekérdezésére használ. A naplózás kikapcsolása azonban letiltja a LUIS-alkalmazás azon képességét, hogy kimondott szövegeket javasoljon, vagy javítsa az [aktív tanuláson](luis-concept-review-endpoint-utterances.md#what-is-active-learning)alapuló teljesítményt. Ha adatvédelmi problémák miatt állítja be, `log=false` nem töltheti le a luis-ból származó felhasználói kimondott szövegrekordját, és nem használhatja ezeket a kimondott szövegeket az alkalmazás fejlesztéséhez.

A naplózás az utterances egyetlen tárolója.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Miért nem szeretném naplózni az összes végpontkimondott szöveget?
Ha a napló előrejelzéselemzés, ne rögzítse a teszt utterances a naplóban.

## <a name="data-management"></a>Adatkezelés

### <a name="can-i-delete-data-from-luis"></a>Törölhetek adatokat a LUIS-ból?

* A LUIS betanításasorán használt példautterances bármikor törölheti. Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazásból, törlődik a LUIS webszolgáltatásból, és nem érhető el az exportáláshoz.
* Törölheti a kimondott szövegeket a felhasználói utterances listájából, amely a LUIS javasolja a **végpont kimondott szövegáttekintése** lapon. Utterance s utterances erről a listából megakadályozza, hogy a javasolt, de nem törli őket a naplókból.
* Ha töröl egy fiókot, az összes alkalmazás törlődik, valamint a példa utterances és naplók. Az adatok at a kiszolgálókon 60 napig őrzi kitasztják, mielőtt véglegesen törölné őket.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hogyan kezeli a Microsoft a LUIS-nak küldött adatokat?

Az [Adatvédelmi központ](https://www.microsoft.com/trustcenter) ismerteti kötelezettségvállalásainkat, valamint az Azure Servicesadat-kezelési és -hozzáférési lehetőségeit.

## <a name="language-and-translation-support"></a>Nyelvi és fordítási támogatás

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Van egy alkalmazásom az egyik nyelven, és egy párhuzamos alkalmazást szeretnék létrehozni egy másik nyelven. Mi a legegyszerűbb módja ennek?
1. Exportálja az alkalmazást.
2. Fordítsa le a címkézett utterances a JSON-fájlban az exportált alkalmazás a célnyelvre.
3. Előfordulhat, hogy módosítania kell a szándékok és az entitások nevét, vagy úgy kell hagynia őket, ahogy vannak.
4. Végül importálja az alkalmazást, hogy a célnyelven egy LUIS-alkalmazás legyen.

## <a name="app-notification"></a>Alkalmazásértesítés

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Miért kaptam egy e-mailt arról, hogy már majdnem kikerültem a kvótából?
A szerzői/kezdőkulcs csak 1000 végpontlekérdezést engedélyezett havonta. Hozzon létre egy LUIS-végpontkulcsot (ingyenes vagy fizetős), és használja ezt a kulcsot végpont-lekérdezések készítésekor. Ha végpontlekérdezéseket hajt segyletet egy robotvagy egy másik ügyfélalkalmazás, módosítania kell a LUIS végpont kulcs van.

## <a name="bots"></a>Robotok

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>A LUIS-robot nem működik. Mit tegyek?

Az első kérdés az, hogy elkülöníteni, ha a probléma a LUIS vagy a LUIS köztes szoftveren kívül történik.

#### <a name="resolve-issue-in-luis"></a>Probléma megoldása a LUIS-ban
Adja át ugyanazt az utterance (pont) a LUIS a [LUIS végpont.](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) Ha hibaüzenetet kap, oldja meg a problémát a LUIS-ban, amíg a hibát már nem adja vissza. Gyakori hibák a következők:

* `Out of call volume quota. Quota will be replenished in <time>.`- Ez a probléma azt jelzi, hogy vagy át kell váltania egy szerzői kulcsról [egy végpontkulcsra,](luis-how-to-azure-subscription.md) vagy módosítania kell a [szolgáltatási szinteket.](luis-how-to-azure-subscription.md#change-pricing-tier)

#### <a name="resolve-issue-in-azure-bot-service"></a>Probléma megoldása az Azure Bot Service szolgáltatásban

Ha az Azure Bot Service-t használja, és a probléma az, hogy a **Web Chat-ben való tesztelés** visszaküldi `Sorry, my bot code is having an issue`a naplóit:

1. Az Azure Portalon a robot, a **Robot felügyeleti** szakaszban válassza **a Build**lehetőséget.
1. Nyissa meg az online kódszerkesztőt.
1. A felső, kék navigációs sávon válassza ki a robot nevét (a második elem jobbra).
1. Az eredményül kapott legördülő listában válassza a **Kudu konzol megnyitása**lehetőséget.
1. Válassza **a LogFiles (Naplófájlok)** lehetőséget, majd az **Alkalmazás**lehetőséget. Tekintse át az összes naplófájlt. Ha a hiba nem jelenik meg az alkalmazásmappában, tekintse át az összes naplófájlt a **LogFiles területen.**
1. Ne felejtse el újraépíteni a projektet, ha olyan lefordított nyelvet használ, mint a C#.

> [!Tip]
> A konzol csomagokat is telepíthet.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Oldja meg a problémát, miközben a Bot Framework helyi gépen hibakeresést folytat.

Ha többet szeretne megtudni a robot helyi hibakereséséről, olvassa el [a Robot hibakeresése](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>A LUIS integrálása

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Hol jön létre a LUIS-alkalmazás az Azure webalkalmazás-robot előfizetési folyamat a folyamat során?
Ha kiválaszt egy LUIS-sablont, és a sablon ablaktáblában a Kijelölés gombot **választja,** a bal oldali ablaktábla a sablontípust is tartalmazza, és megkérdezi, hogy melyik régióban hozza létre a LUIS-sablont. A webalkalmazás-robot folyamat azonban nem hoz létre LUIS-előfizetést.

![LUIS-sablon webalkalmazás-robot régiója](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Milyen LUIS-régiók támogatják a Bot Framework beszédalapozását?
[A beszédfelismerés-alapozás](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) csak a központi (USA- beli) példányban lévő LUIS-alkalmazások esetében támogatott.

## <a name="api-programming-strategies"></a>API programozási stratégiák

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hogyan kaphatom meg programozott módon egy erőforrás LUIS-régióját?

A LUIS-minta segítségével a C# vagy a Node.Js használatával programozott módon [keresheti meg](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) a régiót.

## <a name="luis-service"></a>LUIS szolgáltatás

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>A Language Understanding (LUIS) elérhető a helyszínen vagy a magánfelhőben?

Igen, használhatja a [LUIS-tárolót](luis-container-howto.md) ezekhez a forgatókönyvekhez, ha rendelkezik a szükséges kapcsolatmérő-használathoz.

## <a name="migrating-to-the-next-version"></a>Áttelepítés a következő verzióra

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hogyan telepíthetem át a V3 API előnézetét?

Lásd: [API-v2–v3 áttelepítési útmutató a LUIS-alkalmazásokhoz](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>A 2019-es konferencia közleményei

A Build 2019 konferencián a következő funkciók jelentek meg:

* [A V3 API áttelepítési útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeállított tartományok](luis-reference-prebuilt-domains.md)
* [Dinamikus listaentitások](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videók:

* [Az Azure Conversational AI használata a vállalkozás méretezéséhez a következő generációszámára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>További lépések

A LUIS-ról az alábbi forrásokban olvashat bővebben:
* [Luis címkével ellátott túlcsordulási kérdések](https://stackoverflow.com/questions/tagged/luis)
* [MSDN nyelvi ismeretek intelligens szolgáltatások (LUIS) fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
