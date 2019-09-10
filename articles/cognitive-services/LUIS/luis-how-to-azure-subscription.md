---
title: Szerzői és futtatókörnyezeti kulcsok használata – LUIS
titleSuffix: Azure Cognitive Services
description: Ha először használja a Language Understanding (LUIS), nem kell létrehoznia authoring-kulcsot. Ha közzé szeretné tenni az alkalmazást, majd használja a futásidejű végpontját, létre kell hoznia és hozzá kell rendelnie a futásidejű kulcsot az alkalmazáshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: diberry
ms.openlocfilehash: 5c2e81cd11826a0325cd78384a22ec7eefb3a565
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844865"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Szerzői és futtatókörnyezeti erőforrás-kulcsok használata

A szerzői és futtatókörnyezeti erőforrások hitelesítést biztosítanak a LUIS-alkalmazás és az előrejelzési végpont számára.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Amikor bejelentkezik a LUIS-portálra, a következő lépések közül választhat:

* ingyenes [próbaverzió](#trial-key) , amely lehetővé teszi a szerzői műveletek és néhány előrejelzési végpont lekérdezését.
* új Azure LUIS authoring Resource – új erőforrás létrehozása. Ez nem ugyanaz, mint az előrejelzési végpontok erőforrása. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Jelentkezzen be a LUIS Portalra, és kezdje el a szerzői műveletek

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai) , és fogadja el a használati feltételeket.
1. Indítsa el a LUIS alkalmazást úgy, hogy kiválasztja a használni kívánt LUIS authoring-kulcs típusát: ingyenes próbaverziós kulcs vagy új Azure LUIS authoring Key. 

    ![Language Understanding szerzői erőforrás típusának kiválasztása](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Ha elkészült az erőforrás-kiválasztási folyamattal, [hozzon létre egy új alkalmazást](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Próbaverziós kulcs

A próbaverzió (kezdő) kulcsát Önnek kell megadnia. A hitelesítési kulcsként szolgál az előrejelzési végpont futtatókörnyezetének lekérdezéséhez, havonta akár 1000 lekérdezéssel. 

A **felhasználói beállítások** lapon és a " **Manage-> Azure-erőforrások** " oldalon is látható a Luis-portálon. 

Ha készen áll az előrejelzési végpont közzétételére, hozzon létre és rendeljen hozzá létrehozási és előrejelzési futtatókörnyezeti kulcsokat az alapszintű kulcs funkcióinak cseréjéhez. 

## <a name="create-resources-in-the-azure-portal"></a>Erőforrások létrehozása a Azure Portalban

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/free/). 
1. Válassza a **+ Erőforrás létrehozása** lehetőséget.
1. A keresőmezőbe írja be a `Language understanding` kifejezést.
1. A **Létrehozás** lehetőség kiválasztásával indíthatja el a létrehozás folyamatát. 
1. Hozzon létre **mindkettőt** szerzői és előrejelzési végpont futtatókörnyezeti kulcs létrehozásához. 
1. Adja meg az erőforrás létrehozásához szükséges adatokat, majd válassza a **Létrehozás** lehetőséget a folyamat befejezéséhez.

    ![A Language Understanding-erőforrás létrehozása](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Name (Név)|Cél|
    |--|--|
    |Erőforrás neve| Egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használ.|
    |Előfizetés neve| az erőforrásért fizetendő előfizetés.|
    |Resource group| Egy kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik, hogy az Azure-erőforrásokat az adott régióban való hozzáféréshez és felügyelethez csoportosítsa.|
    |Szerzői hely|A modellhez társított régió.|
    |A szerzői díjak szintjei|Az árképzési szint meghatározza a másodpercenkénti maximális tranzakciót és a havi értéket.|
    |Futtatókörnyezet helye|A közzétett előrejelzési végpont futtatókörnyezetéhez társított régió.|
    |Futtatókörnyezet árképzési szintje|Az árképzési szint meghatározza a másodpercenkénti maximális tranzakciót és a havi értéket.|

    Ha mindkét erőforrás létrejött, rendelje hozzá az erőforrásokat a LUIS-portálhoz.

## <a name="create-resources-in-azure-cli"></a>Erőforrások létrehozása az Azure CLI-ben

Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) használatával egyenként hozhatja létre az erőforrásokat. 

Erőforrás `kind`:

* Authoring`LUIS.Authoring`
* Jóslás`LUIS` 

1. Jelentkezzen be az Azure CLI-be:

    ```console
    az login
    ```

    Ekkor megnyílik egy böngésző, amely lehetővé teszi a megfelelő fiók kiválasztását és a hitelesítés megadását.

1. Hozzon létre egy nevű **Luis authoring-erőforrást** `LUIS.Authoring`, amely a `my-resource-group` (z) `westus` régióhoz tartozó meglévő erőforráscsoporthoz van elnevezve. `my-luis-authoring-resource` 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Hozzon létre `LUIS`egy, a `westus` régióhoz `my-resource-group` tartozó _meglévő_ erőforráscsoport `my-luis-prediction-resource` nevű **Luis előrejelzési végpont-erőforrást**. Ha az ingyenes szintjénél nagyobb átviteli sebességet szeretne használni, váltson `F0` `S0`a következőre:. További információ a [díjszabási szintekről és az átviteli sebességről](luis-boundaries.md#key-limits).

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > A LUIS portál **nem** használja ezeket a kulcsokat, amíg hozzá nem rendelik a Luis portálon a **> Azure-erőforrások kezeléséhez**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Szerzői erőforrás kiosztása a LUIS-portálon az összes alkalmazáshoz

Létrehozhat egy authoring-erőforrást egyetlen alkalmazáshoz vagy a LUIS összes alkalmazásához. Az alábbi eljárás az összes alkalmazást egyetlen szerzői erőforráshoz rendeli.

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai).
1. A felső navigációs sávon kattintson a jobb szélre, válassza ki a felhasználói fiókját, majd válassza a **Beállítások**lehetőséget.
1. A **felhasználói beállítások** lapon válassza a **szerzői erőforrás hozzáadása** lehetőséget, majd válasszon ki egy meglévő authoring-erőforrást. Kattintson a **Mentés** gombra. 

## <a name="assign-a-resource-to-an-app"></a>Erőforrás kiosztása egy alkalmazáshoz

A következő eljárással hozzárendelhet egyetlen erőforrást, szerzői vagy előrejelzési végponti futtatókörnyezetet az alkalmazáshoz.

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), majd válasszon ki egy alkalmazást a **saját alkalmazások** listából.
1. Navigáljon a **Manage-> Azure-erőforrások** lapra.

    ![A LUIS-portálon kattintson a Manage-> Azure-erőforrások elemre, és rendeljen hozzá egy erőforrást az alkalmazáshoz.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Válassza az előrejelzés vagy a szerzői erőforrás létrehozása fület, majd jelölje be az **előrejelzési erőforrás hozzáadása** vagy a **szerzői erőforrás hozzáadása** gomb. 
1. A megfelelő erőforrás megkereséséhez válassza ki a mezőket az űrlapon, majd válassza a **Mentés**lehetőséget.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Futásidejű erőforrás kiosztása a LUIS-portál használata nélkül

Az automatizálási célokra, például a CI/CD-folyamatok esetében érdemes automatizálni egy LUIS Runtime-erőforrás hozzárendelését egy LUIS-alkalmazásba. Ehhez a következő lépéseket kell elvégeznie:

1. Egy Azure Resource Manager a token beszerzése [webhely](https://resources.azure.com/api/token?plaintext=true). Ez a token hamarosan lejár, azonnal használható. A kérelem egy Azure Resource Manager-jogkivonatát adja vissza.

    ![Azure Resource Manager jogkivonat igénylése és Azure Resource Manager token fogadása](./media/luis-manage-keys/get-arm-token.png)

1. A token használatával kérheti a LUIS Runtime erőforrásait az előfizetések között, a [Get Luis Azure accounts API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)-ból, amelyhez a felhasználói fiókja hozzáfér. 

    A bejegyzés API megköveteli a következő beállításokat:

    |Fejléc|Érték|
    |--|--|
    |`Authorization`|Az érték `Authorization` van `Bearer {token}`. Figyelje meg, hogy a token értékét meg kell előznie a word `Bearer` és egy szóközt.| 
    |`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|

    Az API-t a LUIS előfizetések, beleértve az előfizetés-azonosító, az erőforráscsoportot és az erőforrás nevét adja vissza a fiók neve a JSON-objektumok tömbjét adja vissza. Egy elem a tömbben, amely a LUIS alkalmazás hozzárendelése a LUIS-erőforrás megkeresése. 

1. A jogkivonat hozzárendelése a LUIS erőforrás a [alkalmazáshoz az azure-fiókok a LUIS hozzárendelése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API-t. 

    A bejegyzés API megköveteli a következő beállításokat:

    |Típus|Beállítás|Érték|
    |--|--|--|
    |Fejléc|`Authorization`|Az érték `Authorization` van `Bearer {token}`. Figyelje meg, hogy a token értékét meg kell előznie a word `Bearer` és egy szóközt.|
    |Fejléc|`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|
    |Fejléc|`Content-type`|`application/json`|
    |A lekérdezési karakterlánc|`appid`|A LUIS-app azonosítója. 
    |Törzs||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "erőforráscsoport-2"<br>"AccountName": "a luis-uswest-S0-2"}|

    Ha ez az API sikeres volt, a 201 - létrehozott állapotát adja vissza. 

## <a name="unassign-resource"></a>Erőforrás hozzárendelésének megszüntetése

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), majd válasszon ki egy alkalmazást a **saját alkalmazások** listából.
1. Navigáljon a **Manage-> Azure-erőforrások** lapra.
1. Válassza az előrejelzés vagy a szerzői erőforrás létrehozása fület, majd válassza az erőforrás **hozzárendelésének megszüntetése** gombot. 

Ha törli az erőforrás hozzárendelését, az nem törlődik az Azure-ból. Csak byl odpojen OD LUIS. 

## <a name="reset-authoring-key"></a>Szerzői kulcs visszaállítása

**Az [áttelepített erőforrás-alkalmazások létrehozásához](luis-migration-authoring.md)** : Ha a szerzői kulcs biztonsága sérül, állítsa alaphelyzetbe a kulcsot a Azure Portal az adott authoring Resource **kulcsok** lapján. 

**Még nem telepített alkalmazások esetén**: a kulcs alaphelyzetbe áll a Luis-portál összes alkalmazásán. Ha a szerzői API-kon keresztül készíti el az alkalmazásokat, a OCP-APIM-Subscription-Key értékét az új kulcsra kell módosítania.

## <a name="regenerate-azure-key"></a>Azure-kulcs újrakészítése

Az Azure-kulcsok újragenerálása a Azure Portal a **kulcsok** lapon.

## <a name="delete-account"></a>Fiók törlése

Lásd: [adattárolási és -eltávolítási](luis-concept-data-storage.md#accounts) milyen adat törlődik, ha a fiók törlése kapcsolatos információkat.

## <a name="change-pricing-tier"></a>Tarifacsomag-váltás

1.  A [Azure](https://portal.azure.com), keresse meg a LUIS-előfizetés. Válassza ki a LUIS-előfizetést.
    ![Keresse meg a LUIS-előfizetés](./media/luis-usage-tiers/find.png)
1.  A rendelkezésre álló díjszabási szintek megtekintéséhez válassza az **árképzési szintet** . 
    ![Árképzési szintek megtekintése](./media/luis-usage-tiers/subscription.png)
1.  Válassza ki a díjszabási szintet, és válassza a **kiválasztás** lehetőséget a módosítás mentéséhez. 
    ![A LUIS fizetési szint módosítása](./media/luis-usage-tiers/plans.png)
1.  Ha a díjszabás módosítása befejeződött, egy előugró ablak ellenőrzi az új tarifacsomag. 
    ![A LUIS támogatási csomag ellenőrzése](./media/luis-usage-tiers/updated.png)
1. Ne felejtse el [rendelje hozzá a végpont kulcs](#assign-a-resource-to-an-app) a a **közzététel** lapon, és használhatja az összes endpoint lekérdezés. 

## <a name="viewing-azure-resource-metrics"></a>Azure-erőforrás metrikáinak megtekintése

### <a name="viewing-azure-resource-summary-usage"></a>Az Azure-erőforrások összegző használatának megtekintése
A LUIS-használati adatokat megtekintheti az Azure-ban. A **áttekintése** lapon többek között a hívások és hibák legutóbbi összegző információit jeleníti meg. Ha Ön kérést egy LUIS végpontot, majd azonnal tekintse meg a **áttekintőlapján**, akár öt perc alatt jelenik meg a használat engedélyezése.

![Összefoglaló használat megtekintése](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure-Erőforrás-használati diagramok testreszabása
Metrikák az adatok részletesebb betekintést biztosít.

![Alapértelmezett mérőszámok](./media/luis-usage-tiers/metrics-default.png)

Konfigurálhatja a mérőszámdiagramok időszak és a metrika típusa. 

![Egyéni metrikák](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Összes tranzakciós küszöbértékének riasztási
Ha szeretné tudni, hogy ha egy bizonyos tranzakció küszöbértéket, például 10 000 tranzakció, elérte a riasztás is létrehozhat. 

![Alapértelmezett riasztások](./media/luis-usage-tiers/alert-default.png)

Metrikariasztás hozzáadása a **összes hívás** metrika egy bizonyos ideig. Adja hozzá, amelyek megkapják a riasztás minden személyek e-mail címét. Adja hozzá a webhookok az összes rendszer, amelyre a riasztást kapni. A riasztás akkor aktiválódik, ha egy logikai alkalmazást is futtathatja. 

## <a name="next-steps"></a>További lépések

* Megtudhatja [, hogyan használhatja a verzióit](luis-how-to-manage-versions.md) az alkalmazás életciklusának szabályozására.
* Ismerje meg az adott erőforrással kapcsolatos fogalmakat, például a [szerzői erőforrást](luis-concept-keys.md#authoring-key) és a [közreműködőket](luis-concept-keys.md#contributions-from-other-authors) .
* Útmutató szerzői és futtatókörnyezeti erőforrások [létrehozásához](luis-how-to-azure-subscription.md)
* Migrálás az új [szerzői erőforrásba](luis-migration-authoring.md) 