---
title: Szerzői és futtatókörnyezeti kulcsok használata – LUIS
description: Ha először használja a Language Understanding (LUIS), nem kell létrehoznia authoring-kulcsot. Ha közzé szeretné tenni az alkalmazást, majd használja a futásidejű végpontját, létre kell hoznia és hozzá kell rendelnie a futásidejű kulcsot az alkalmazáshoz.
services: cognitive-services
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 5f6d62a63ea5ae0d3e4ca5913d6e7834ba07692a
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560433"
---
# <a name="create-luis-resources"></a>LUIS-erőforrások létrehozása

A szerzői és lekérdezési előrejelzési futtatókörnyezet erőforrásai hitelesítést biztosítanak a LUIS-alkalmazás és az előrejelzési végpont számára.

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-erőforrások

A LUIS három típusú Azure-erőforrást és egy nem Azure-erőforrást is lehetővé tesz:

|Kulcs|Cél|Kognitív szolgáltatás`kind`|Kognitív szolgáltatás`type`|
|--|--|--|--|
|Szerzői kulcs|A szerzői műveletek, a képzés, a közzététel és a tesztelés segítségével hozzáférhetnek az alkalmazáshoz, és kezelhetik azokat. Hozzon létre egy LUIS authoring-kulcsot, ha a LUIS-alkalmazásokat programozott módon szeretné létrehozni.<br><br>A kulcs célja, `LUIS.Authoring` hogy lehetővé tegye a következőket:<br>* programozott módon felügyelheti Language Understanding alkalmazásokat és modelleket, beleértve a képzést és a közzétételt<br> * a szerzői erőforrásra vonatkozó engedélyek vezérlése [a közreműködő szerepkörhöz](#contributions-from-other-authors)rendelt személyek hozzárendelésével.|`LUIS.Authoring`|`Cognitive Services`|
|Lekérdezés-előrejelzési kulcs| Lekérdezés-előrejelzési végponti kérelmek. Hozzon létre egy LUIS-előrejelzési kulcsot, mielőtt az ügyfélalkalmazás a kezdő erőforrás által biztosított 1 000-kérelmeknél újabb előrejelzéseket kér. |`LUIS`|`Cognitive Services`|
|[Kognitív szolgáltatás – több szolgáltatásból álló erőforrás kulcsa](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|A lekérdezés-előrejelzési végpontok megosztva a LUIS és más támogatott Cognitive Servicesokkal.|`CognitiveServices`|`Cognitive Services`|
|Kezdő|Ingyenes szerzői műveletek (szerepköralapú hozzáférés-vezérlés nélkül) a LUIS-portálon vagy API-kon keresztül (beleértve az SDK-kat), az ingyenes 1 000 előrejelzési végponti kérések havonta egy böngészőben, API-n vagy SDK-n keresztül|-|Nem Azure-erőforrás|

Ha az Azure-Erőforrás-létrehozási folyamat elkészült, [rendelje hozzá a kulcsot](#assign-a-resource-to-an-app) az alkalmazáshoz a Luis portálon.

Fontos, hogy LUIS-alkalmazásokat hozzon létre azokon a [régiókban](luis-reference-regions.md#publishing-regions) , amelyeken közzé és lekérdezéseket szeretne közzétenni.

## <a name="resource-ownership"></a>Erőforrás-tulajdonos

Az Azure-erőforrás, például a LUIS, az erőforrást tartalmazó előfizetés tulajdonosa.

Egy erőforrás tulajdonjogának átadásához a szervezeti egység a következőkre képes:
* Az előfizetés [tulajdonjogának](../../cost-management-billing/manage/billing-subscription-transfer.md) átruházása
* Exportálja a LUIS alkalmazást fájlként, majd importálja az alkalmazást egy másik előfizetésre. Az Exportálás a LUIS portál **saját alkalmazások** lapján érhető el.


## <a name="resource-limits"></a>Erőforráskorlátok

### <a name="authoring-key-creation-limits"></a>Szerzői kulcsok létrehozási korlátai

Régiónként legfeljebb 10 szerzői kulcsot hozhat létre egy előfizetéshez.

Lásd a [legfontosabb korlátokat](luis-limits.md#key-limits) és az [Azure-régiókat](luis-reference-regions.md).

A közzétételi régiók eltérnek a szerzői régióktól. Győződjön meg arról, hogy létrehoz egy alkalmazást a szerzői régióban, amely ahhoz a közzétételi régióhoz tartozik, amelyet az ügyfélalkalmazás el szeretne helyezni.

### <a name="key-usage-limit-errors"></a>Kulcshasználat korlátozási hibák

A használati korlátok a díjszabási szinten alapulnak.

Ha túllépi a tranzakció-másodpercenkénti (TPS-) kvótát, HTTP 429-es hibaüzenetet kap. Ha túllépi a havi tranzakciós (TPS) kvótát, HTTP 403-es hibaüzenetet kap.


### <a name="reset-authoring-key"></a>Szerzői kulcs alaphelyzetbe állítása

Az [áttelepített erőforrás-alkalmazások létrehozásához](luis-migration-authoring.md) : Ha a szerzői kulcs biztonsága sérül, állítsa alaphelyzetbe a kulcsot a Azure Portal az adott authoring Resource **kulcsok** lapján.

Még nem telepített alkalmazások esetén: a kulcs alaphelyzetbe áll a LUIS-portál összes alkalmazásán. Ha a szerzői API-kon keresztül készíti el az alkalmazásokat, a OCP-APIM-Subscription-Key értékét az új kulcsra kell módosítania.

### <a name="regenerate-azure-key"></a>Azure-kulcs újrakészítése

Az Azure-kulcsok újragenerálása a Azure Portal a **kulcsok** lapon.


## <a name="app-ownership-access-and-security"></a>Alkalmazások tulajdonlása, hozzáférése és biztonsága

Az alkalmazást az Azure-erőforrásai határozzák meg, amelyet a tulajdonos előfizetése határoz meg.

Elvégezheti a LUIS-alkalmazás áthelyezését. Használja az alábbi dokumentációs erőforrásokat a Azure Portal vagy az Azure CLI-ben:

* [Alkalmazás áthelyezése a LUIS authoring-erőforrások között](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Erőforrás áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás áthelyezése ugyanazon az előfizetésen belül vagy előfizetések között](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Más szerzők hozzájárulásai

Az [áttelepített erőforrás-alkalmazások létrehozásához](luis-migration-authoring.md) : a _közreműködők_ kezelése a Azure Portal a szerzői erőforráshoz a **hozzáférés-vezérlés (iam)** lapon. Megtudhatja, [hogyan adhat hozzá felhasználót](luis-how-to-collaborate.md)a közreműködő e-mail-címével és a _közreműködő_ szerepkörrel.

Még nem telepített alkalmazások esetén: az összes _közreműködőt_ a Luis portálon kezelheti a **Manage-> közreműködők** lapról.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Lekérdezés-előrejelzési hozzáférés magán-és nyilvános alkalmazásokhoz

**Privát** alkalmazások esetén a lekérdezési előrejelzési futtatókörnyezet hozzáférése a tulajdonosokhoz és a közreműködők számára érhető el. **Nyilvános** alkalmazások esetén a futtatókörnyezet-hozzáférés mindenki számára elérhető, aki saját Azure [kognitív szolgáltatással](../cognitive-services-apis-create-account.md) vagy [Luis](#create-resources-in-the-azure-portal) futásidejű erőforrással rendelkezik, és rendelkezik a nyilvános alkalmazás azonosítójával.

Jelenleg nem létezik katalógus a nyilvános alkalmazásokról.

### <a name="authoring-permissions-and-access"></a>Szerzői engedélyek és hozzáférés
Az alkalmazáshoz való hozzáférést a [Luis](luis-reference-regions.md#luis-website) portálról vagy a [szerzői API-kat](https://go.microsoft.com/fwlink/?linkid=2092087) az Azure authoring Resource vezérli.

A tulajdonos és az összes közreműködő hozzáfér az alkalmazás létrehozásához.

|A szerzői hozzáférés magában foglalja a|Jegyzetek|
|--|--|
|Végponti kulcsok hozzáadása vagy eltávolítása||
|Verzió exportálása||
|Végponti naplók exportálása||
|Verzió importálása||
|Alkalmazás nyilvánosvé tétele|Ha egy alkalmazás nyilvános, a szerzői vagy végponti kulccsal bárki lekérdezheti az alkalmazást.|
|Modell módosítása|
|Közzététel|
|Az [aktív tanulási](luis-how-to-review-endpoint-utterances.md) végpont hosszúságú kimondott szöveg áttekintése|
|Betanítás|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>A végpont futásidejű hozzáférésének előrejelzése

Az előrejelzési végpont lekérdezéséhez a **kezelés** szakasz **alkalmazás adatai** lapján található beállítás vezérli.

|[Privát végpont](#runtime-security-for-private-apps)|[Nyilvános végpont](#runtime-security-for-public-apps)|
|:--|:--|
|Elérhető a tulajdonos és a közreműködők számára|Elérhető a tulajdonos, a közreműködők és bárki másnak, aki ismeri az alkalmazás AZONOSÍTÓját|

Megadhatja, hogy ki látja el a LUIS Runtime-kulcsot úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha a LUIS-t egy robotból használja, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül hívja meg a LUIS-végpontot, hozzon létre egy kiszolgálóoldali API-t (például egy Azure- [függvényt](https://azure.microsoft.com/services/functions/)) vezérelt hozzáféréssel (például [HRE](https://azure.microsoft.com/services/active-directory/)). A kiszolgálóoldali API meghívása és hitelesítése, valamint az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Habár ez a stratégia nem gátolja meg az emberen belüli támadásokat, az eltorzítja a felhasználók kulcs-és végpont-URL-címét, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának hozzáadását (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Futásidejű biztonság privát alkalmazásokhoz

A privát alkalmazások futtatókörnyezete csak a következőkhöz érhető el:

|Kulcs és felhasználó|Magyarázat|
|--|--|
|Tulajdonos szerzői kulcsa| Akár 1000 végpontot elér|
|Közreműködő/közreműködő szerzői kulcsok| Akár 1000 végpontot elér|
|Egy szerző vagy közreműködő vagy közreműködő által a LUIShoz rendelt kulcs|Kulcshasználat szintje alapján|

### <a name="runtime-security-for-public-apps"></a>Futtatókörnyezeti biztonság nyilvános alkalmazásokhoz

Az alkalmazás nyilvánosként való konfigurálása után _bármely_ érvényes Luis authoring Key vagy Luis Endpoint kulcs lekérdezheti az alkalmazást, feltéve, hogy a kulcs nem használta a teljes végponti kvótát.

Olyan felhasználó, aki nem tulajdonos vagy közreműködő, csak akkor férhet hozzá egy nyilvános alkalmazás futtatókörnyezetéhez, ha az alkalmazás azonosítója van megadva. A LUIS nem rendelkezik nyilvános _piaccal_ vagy más módszerrel a nyilvános alkalmazások kereséséhez.

A nyilvános alkalmazások minden régióban közzé vannak téve, hogy a régió-alapú LUIS-erőforrás kulcsával rendelkező felhasználók hozzáférhessenek az alkalmazáshoz, amely bármelyik régióban hozzá van rendelve az erőforrás-kulcshoz.


### <a name="securing-the-query-prediction-endpoint"></a>A lekérdezés-előrejelzési végpont biztonságossá tétele

Megadhatja, hogy ki láthatja a LUIS előrejelzési futtatókörnyezet végpontjának kulcsát úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha a LUIS-t egy robotból használja, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül hívja meg a LUIS-végpontot, hozzon létre egy kiszolgálóoldali API-t (például egy Azure- [függvényt](https://azure.microsoft.com/services/functions/)) vezérelt hozzáféréssel (például [HRE](https://azure.microsoft.com/services/active-directory/)). A kiszolgálóoldali API meghívásakor, valamint a hitelesítés és az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Habár ez a stratégia nem gátolja meg az emberen belüli támadásokat, az eltorzítja a végpontot a felhasználóktól, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának (például [Application Insights](https://azure.microsoft.com/services/application-insights/)) hozzáadását.

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Jelentkezzen be a LUIS Portalra, és kezdje el a szerzői műveletek

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai) , és fogadja el a használati feltételeket.
1. Indítsa el a LUIS alkalmazást úgy, hogy kiválasztja a használni kívánt LUIS authoring-kulcs típusát: ingyenes próbaverziós kulcs vagy új Azure LUIS authoring Key.

    ![Language Understanding szerzői erőforrás típusának kiválasztása](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Ha elkészült az erőforrás-kiválasztási folyamattal, [hozzon létre egy új alkalmazást](luis-how-to-start-new-app.md#create-new-app-in-luis).


## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure Portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Erőforrások létrehozása az Azure CLI-ben

Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) használatával egyenként hozhatja létre az erőforrásokat.

Erőforrás `kind` :

* Authoring`LUIS.Authoring`
* Jóslás`LUIS`

1. Jelentkezzen be az Azure CLI-be:

    ```azurecli
    az login
    ```

    Ekkor megnyílik egy böngésző, amely lehetővé teszi a megfelelő fiók kiválasztását és a hitelesítés megadását.

1. Hozzon létre egy nevű **Luis authoring-erőforrást**, amely a `LUIS.Authoring` (z `my-luis-authoring-resource` _existing_ `my-resource-group` ) régióhoz tartozó meglévő erőforráscsoporthoz van elnevezve `westus` .

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Hozzon **LUIS prediction endpoint resource**létre egy `LUIS` , a `my-luis-prediction-resource` régióhoz tartozó _meglévő_ erőforráscsoport nevű Luis előrejelzési végpont-erőforrást `my-resource-group` `westus` . Ha az ingyenes szintjénél nagyobb átviteli sebességet szeretne használni, váltson a következőre: `F0` `S0` . További információ a [díjszabási szintekről és az átviteli sebességről](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > A LUIS portál **nem** használja ezeket a kulcsokat, amíg hozzá nem rendelik a Luis portálon a **> Azure-erőforrások kezeléséhez**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Erőforrás kiosztása a LUIS-portálon

Létrehozhat egy authoring-erőforrást egyetlen alkalmazáshoz vagy a LUIS összes alkalmazásához. Az alábbi eljárás az összes alkalmazást egyetlen szerzői erőforráshoz rendeli.

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai).
1. A felső navigációs sávon kattintson a jobb szélre, válassza ki a felhasználói fiókját, majd válassza a **Beállítások**lehetőséget.
1. A **felhasználói beállítások** lapon válassza a **szerzői erőforrás hozzáadása** lehetőséget, majd válasszon ki egy meglévő authoring-erőforrást. Kattintson a **Mentés** gombra.

## <a name="assign-a-resource-to-an-app"></a>Erőforrás kiosztása egy alkalmazáshoz

A következő eljárással rendelhet hozzá egy alkalmazást.

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), majd válasszon ki egy alkalmazást a **saját alkalmazások** listából.
1. Navigáljon a **Manage-> Azure-erőforrások** lapra.

    ![A LUIS-portálon kattintson a Manage-> Azure-erőforrások elemre, és rendeljen hozzá egy erőforrást az alkalmazáshoz.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Válassza az előrejelzés vagy a szerzői erőforrás létrehozása fület, majd jelölje be az **előrejelzési erőforrás hozzáadása** vagy a **szerzői erőforrás hozzáadása** gomb.
1. A megfelelő erőforrás megkereséséhez válassza ki a mezőket az űrlapon, majd válassza a **Mentés**lehetőséget.

### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Lekérdezés-előrejelzési futtatókörnyezet erőforrásának kiosztása a LUIS-portál használata nélkül

Az automatizálási célokra, például a CI/CD-folyamatok esetében érdemes automatizálni egy LUIS Runtime-erőforrás hozzárendelését egy LUIS-alkalmazásba. Ehhez a következő lépéseket kell elvégeznie:

1. Azure Resource Manager jogkivonat beszerzése a [webhelyről](https://resources.azure.com/api/token?plaintext=true). Ez a jogkivonat a lejárat után azonnal használatba kerül. A kérelem Azure Resource Manager tokent ad vissza.

    ![Azure Resource Manager jogkivonat igénylése és Azure Resource Manager token fogadása](./media/luis-manage-keys/get-arm-token.png)

1. A token használatával kérheti a LUIS Runtime erőforrásait az előfizetések között, a [Get Luis Azure accounts API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)-ból, amelyhez a felhasználói fiókja hozzáfér.

    A POST API-nak a következő beállításokat kell megadnia:

    |Fejléc|Érték|
    |--|--|
    |`Authorization`|A értéke: `Authorization` `Bearer {token}` . Figyelje meg, hogy a jogkivonat értékének előtt szerepelnie kell a szónak `Bearer` és a szóköznek.|
    |`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|

    Ez az API a LUIS-előfizetések JSON-objektumainak egy tömbjét adja vissza, beleértve az előfizetés-azonosítót, az erőforráscsoportot és az erőforrás nevét, amelyet a rendszer a fiók neveként adott vissza. Keresse meg a tömb egy olyan elemét, amely a LUIS-alkalmazáshoz rendelendő LUIS-erőforrás.

1. Rendelje hozzá a tokent a LUIS-erőforráshoz a [Luis Azure-fiókok egy Application API-hoz](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) való hozzárendelésével.

    A POST API-nak a következő beállításokat kell megadnia:

    |Típus|Beállítás|Érték|
    |--|--|--|
    |Fejléc|`Authorization`|A értéke: `Authorization` `Bearer {token}` . Figyelje meg, hogy a jogkivonat értékének előtt szerepelnie kell a szónak `Bearer` és a szóköznek.|
    |Fejléc|`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|
    |Fejléc|`Content-type`|`application/json`|
    |QueryString|`appid`|A LUIS-app azonosítója.
    |Törzs||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-USWest-S0-2"}|

    Ha ez az API sikeres, egy 201 által létrehozott állapotot ad vissza.

## <a name="unassign-resource"></a>Erőforrás hozzárendelésének megszüntetése

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), majd válasszon ki egy alkalmazást a **saját alkalmazások** listából.
1. Navigáljon a **Manage-> Azure-erőforrások** lapra.
1. Válassza az előrejelzés vagy a szerzői erőforrás létrehozása fület, majd válassza az erőforrás **hozzárendelésének megszüntetése** gombot.

Ha törli az erőforrás hozzárendelését, az nem törlődik az Azure-ból. Csak a LUIS-ról van csatolva.


## <a name="delete-account"></a>Fiók törlése

Az adatok [tárolásával és eltávolításával](luis-concept-data-storage.md#accounts) kapcsolatos információkért tekintse meg a fiók törlése után megjelenő adatokat.

## <a name="change-pricing-tier"></a>A tarifacsomag módosítása

1.  Az [Azure](https://portal.azure.com)-ban keresse meg a Luis-előfizetését. Válassza ki a LUIS-előfizetést.
    ![A LUIS-előfizetés keresése](./media/luis-usage-tiers/find.png)
1.  A rendelkezésre álló díjszabási szintek megtekintéséhez válassza az **árképzési szintet** .
    ![Árképzési szintek megtekintése](./media/luis-usage-tiers/subscription.png)
1.  Válassza ki a díjszabási szintet, és válassza a **kiválasztás** lehetőséget a módosítás mentéséhez.
    ![A LUIS fizetési szintek módosítása](./media/luis-usage-tiers/plans.png)
1.  A díjszabás megváltozása után egy előugró ablak ellenőrzi az új díjszabási szintet.
    ![A LUIS fizetési szintek ellenőrzése](./media/luis-usage-tiers/updated.png)
1. Ne felejtse el [hozzárendelni ezt a végponti kulcsot](#assign-a-resource-to-an-app) a **közzétételi** lapon, és használja azt az összes végponti lekérdezésben.

## <a name="viewing-azure-resource-metrics"></a>Azure-erőforrás metrikáinak megtekintése

### <a name="viewing-azure-resource-summary-usage"></a>Az Azure-erőforrások összegző használatának megtekintése
A LUIS használati adatait az Azure-ban tekintheti meg. Az **Áttekintés** oldalon a legutóbbi összefoglaló információk, például a hívások és a hibák láthatók. Ha LUIS-végponti kérelmet hoz létre, azonnal tekintse meg az **Áttekintés lapot**, és akár öt percet is igénybe vehet, hogy a használat megjelenjen.

![Összefoglaló használat megtekintése](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure-Erőforrás-használati diagramok testreszabása
A metrikák részletesebb áttekintést nyújtanak az adatokról.

![Alapértelmezett metrikák](./media/luis-usage-tiers/metrics-default.png)

A metrikák diagramjait az időszak és a metrika típusára állíthatja be.

![Egyéni metrikák](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Tranzakciók teljes küszöbértékének riasztása
Ha szeretné tudni, hogy mikor ért el egy bizonyos tranzakciós küszöbértéket, például 10 000 tranzakciót, létrehozhat egy riasztást.

![Alapértelmezett riasztások](./media/luis-usage-tiers/alert-default.png)

Adjon hozzá egy metrikai riasztást a **hívások teljes** metrikája számára egy adott időtartamra vonatkozóan. Adja meg az összes olyan személy e-mail-címét, akinek meg kell kapnia a riasztást. Webhookok hozzáadása a riasztást fogadó összes rendszerhez. Egy logikai alkalmazást is futtathat a riasztás elindítása után.

## <a name="next-steps"></a>További lépések

* Megtudhatja [, hogyan használhatja a verzióit](luis-how-to-manage-versions.md) az alkalmazás életciklusának szabályozására.
* Migrálás az új [szerzői erőforrásba](luis-migration-authoring.md)
