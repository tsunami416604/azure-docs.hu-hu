---
title: A szerzői és a futásidejű kulcsok használata - LUIS
titleSuffix: Azure Cognitive Services
description: Amikor először használja a language understanding (LUIS) nyelvet, nem kell létrehoznia egy szerzői kulcsot. Ha közzé kívánja tenni az alkalmazást, majd a futásidejű végpontot használja, létre kell hoznia és hozzá kell rendelnie a futásidejű kulcsot az alkalmazáshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219972"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Szerzői és futásidejű erőforráskulcsok használata

A szerzői és a futásidejű erőforrások hitelesítést biztosítanak a LUIS-alkalmazásnak és az előrejelzési végpontnak.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Amikor bejelentkezik a LUIS-portálra, folytathatja a következőt:

* egy ingyenes [próbakulcs](#trial-key) – szerzői és néhány előrejelzési végpont-lekérdezés biztosítása.
* egy Azure [LUIS szerzői](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) erőforrás. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Jelentkezzen be a LUIS-portálra, és kezdje meg a szerzői jog

1. Jelentkezzen be a [LUIS-portálra,](https://www.luis.ai) és fogadja el a használati feltételeket.
1. Kezdje el a LUIS-alkalmazást a használni kívánt LUIS-szerzői kulcs típusának kiválasztásával: ingyenes próbakulcs vagy új Azure LUIS szerzői kulcs. 

    ![A Nyelvi megértés szerzői erőforrástípusának kiválasztása](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Ha végzett az erőforrás-kiválasztási folyamattal, [hozzon létre egy új alkalmazást.](luis-how-to-start-new-app.md#create-new-app-in-luis) 

## <a name="trial-key"></a>Próbakulcs

A próbagomb (indító) az Ön számára biztosított. A hitelesítési kulcs ként használja az előrejelzési végpont futásidejű, havonta legfeljebb 1000 lekérdezések lekérdezése. 

A **Felhasználói beállítások** lapon és a **-> Azure-erőforrások kezelése** lapon is látható a LUIS portálon. 

Ha készen áll az előrejelzési végpont közzétételére, hozzon létre és rendeljen hozzá szerzői és előrejelzési futásidejű kulcsokat, hogy cserélje le az induló kulcs funkcióit. 

## <a name="create-resources-in-the-azure-portal"></a>Erőforrások létrehozása az Azure Portalon

1. [Ezen a hivatkozáson](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) keresztül megnyithatja az Azure Portalon az erőforrások létrehozásához.
1. Válassza **a Mindkettő** lehetőséget egy szerzői és egy előrejelzési végpont futásidejű kulcs létrehozásához. 
1. Adja meg az erőforrás létrehozásához szükséges adatokat, majd a folyamat befejezéséhez válassza a **Létrehozás** gombot.

    ![A nyelvtudáserőforrás létrehozása](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Név|Cél|
    |--|--|
    |Erőforrás neve| Egyéni név, amely a szerzői és előrejelzési végpont-lekérdezések URL-címének részeként használatos.|
    |Előfizetés neve| az erőforrásért kiszámlázott előfizetés.|
    |Erőforráscsoport| A kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását az ugyanabban a régióban való hozzáféréshez és kezeléshez.|
    |Szerzői hely|A modellhez társított régió.|
    |Szerzői tarifacsomag|A tarifacsomag határozza meg a maximális tranzakció másodpercenként és havonta.|
    |Futásidejű hely|A közzétett előrejelzési végpont futásidejéhez társított régió.|
    |Futásidejű tarifacsomag|A tarifacsomag határozza meg a maximális tranzakció másodpercenként és havonta.|

    Mindkét erőforrás létrehozása után rendelje hozzá az erőforrásokat a LUIS-portálon.

## <a name="create-resources-in-azure-cli"></a>Erőforrások létrehozása az Azure CLI-ben

Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) használatával minden egyes erőforrást külön-külön hozhat létre. 

Erőforrás `kind`:

* Authoring:`LUIS.Authoring`
* Jóslás:`LUIS` 

1. Jelentkezzen be az Azure CLI-be:

    ```azurecli
    az login
    ```

    Ez megnyit egy böngészőt, amely lehetővé teszi a megfelelő fiók kiválasztását és a hitelesítés biztosítását.

1. Hozzon létre egy luis `LUIS.Authoring` **authoring erőforrást**, `my-resource-group` amely `westus` a régióhoz elnevezett _meglévő_ erőforráscsoportban van elnevezve. `my-luis-authoring-resource` 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Hozzon létre egy **LUIS-előrejelzési végponterőforrást,** `LUIS`amely `my-resource-group` a `westus` régióhoz elnevezett _meglévő_ erőforráscsoportban van elnevezve. `my-luis-prediction-resource` Ha az ingyenes szintnél nagyobb átviteli `F0` átaputét szeretné elérni, módosítsa a. `S0` További információ [a tarifacsomagokról és az átviteli műveletekről.](luis-boundaries.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Ezeket a kulcsokat a LUIS-portál **csak** akkor használja, ha a **-> Azure-erőforrások kezelése**szolgáltatás luis portálján van hozzárendelve.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Szerzői erőforrás hozzárendelése a LUIS-portálon az összes alkalmazáshoz

Hozzárendelhet egy szerzői erőforrást egyetlen alkalmazáshoz vagy a LUIS összes alkalmazásához. Az alábbi eljárás az összes alkalmazást egyetlen szerzői erőforráshoz rendeli.

1. Jelentkezzen be a [LUIS-portálra.](https://www.luis.ai)
1. A felső navigációs sávon, a jobb szélen jelölje ki a felhasználói fiókját, majd válassza a **Beállítások lehetőséget.**
1. A **Felhasználói beállítások** lapon válassza a **Szerzői erőforrás hozzáadása** lehetőséget, majd jelöljön ki egy meglévő szerzői erőforrást. Kattintson a **Mentés** gombra. 

## <a name="assign-a-resource-to-an-app"></a>Erőforrás hozzárendelése alkalmazáshoz

Egyetlen erőforrást, szerzői vagy előrejelzési végponti futásidejűt rendelhet egy alkalmazáshoz a következő eljárással.

1. Jelentkezzen be a [LUIS-portálra,](https://www.luis.ai)majd válasszon ki egy alkalmazást a **Saját alkalmazások** listából.
1. Nyissa meg a **-> Azure-erőforrások kezelése** lapot.

    ![Válassza ki a Manage -> Azure-erőforrások a LUIS portálon erőforrás hozzárendeléséhez az alkalmazáshoz.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Válassza az Előrejelzés vagy Szerzői erőforrás lapot, majd válassza az **Előrejelzési erőforrás hozzáadása** vagy **a Szerzői erőforrás hozzáadása** gombot. 
1. Jelölje ki az űrlap mezőit a megfelelő erőforrás megkereséséhez, majd kattintson a **Mentés gombra.**  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Runtime erőforrás hozzárendelése a LUIS-portál használata nélkül

Automatizálási célokra, például egy CI/CD-folyamat, érdemes automatizálni a LUIS futásidejű erőforrás hozzárendelését egy LUIS-alkalmazáshoz. Ehhez a következő lépéseket kell végrehajtania:

1. Szerezzen be egy Azure Resource Manager-jogkivonatot erről a [webhelyről.](https://resources.azure.com/api/token?plaintext=true) Ez a jogkivonat lejár, ezért azonnal használja. A kérelem egy Azure Resource Manager-jogkivonatot ad vissza.

    ![Azure Resource Manager-token kérése és az Azure Resource Manager token fogadása](./media/luis-manage-keys/get-arm-token.png)

1. A jogkivonat használatával kérje a LUIS futásidejű erőforrások között előfizetések, a [Luis azure-fiókok bekérése API-ból,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)amely a felhasználói fiók hozzáféréssel rendelkezik. 

    A POST API-hoz a következő beállítások szükségesek:

    |Fejléc|Érték|
    |--|--|
    |`Authorization`|Az értéke `Authorization` `Bearer {token}`. Figyelje meg, hogy a token `Bearer` értéket a szónak és egy szóköznek kell megelőznie.| 
    |`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|

    Ez az API a LUIS-előfizetések JSON-objektumainak tömbjét adja vissza, beleértve az előfizetés-azonosítót, az erőforráscsoportot és az erőforrásnevet, és a fióknévként visszaadott. Keresse meg a tömb egyetlen elem, amely a LUIS-erőforrás hozzárendelni a LUIS app. 

1. Rendelje hozzá a jogkivonatot a LUIS-erőforráshoz a [LUIS azure-fiókok hozzárendelése alkalmazás API-hoz.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) 

    A POST API-hoz a következő beállítások szükségesek:

    |Típus|Beállítás|Érték|
    |--|--|--|
    |Fejléc|`Authorization`|Az értéke `Authorization` `Bearer {token}`. Figyelje meg, hogy a token `Bearer` értéket a szónak és egy szóköznek kell megelőznie.|
    |Fejléc|`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|
    |Fejléc|`Content-type`|`application/json`|
    |Querystring|`appid`|A LUIS-app azonosítója. 
    |Törzs||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Ha ez az API sikeres, 201 – létrehozott állapotot ad vissza. 

## <a name="unassign-resource"></a>Erőforrás hozzárendelésének visszavonása

1. Jelentkezzen be a [LUIS-portálra,](https://www.luis.ai)majd válasszon ki egy alkalmazást a **Saját alkalmazások** listából.
1. Nyissa meg a **-> Azure-erőforrások kezelése** lapot.
1. Válassza az Előrejelzés vagy szerzői erőforrás lapot, majd válassza az **Erőforrás hozzárendelésének visszavonása** gombot az erőforráshoz. 

Ha egy erőforrás hozzárendelését, nem törlődik az Azure-ból. Csak a LUIS-tól van lekötve. 

## <a name="reset-authoring-key"></a>Szerzői kulcs alaphelyzetbe állítása

**Az [erőforrások áttelepítése](luis-migration-authoring.md) alkalmazások szerzői:** ha a szerzői kulcs sérül, állítsa alaphelyzetbe a kulcsot az Azure Portalon a **Keys** lapon az adott szerzői erőforrás. 

**A még nem áttelepített alkalmazások esetében:** a kulcs alaphelyzetbe áll a LUIS-portálon lévő összes alkalmazásban. Ha az alkalmazásokat a szerzői API-kon keresztül hozhatja létre, módosítania kell az Ocp-Apim-Subscription-Key értékét az új kulcsra.

## <a name="regenerate-azure-key"></a>Az Azure-kulcs újragenerálása

Az Azure-kulcsok az Azure Portalon, a **Keys** lapon.

## <a name="delete-account"></a>Fiók törlése

[Az Adattárolás és -eltávolítás](luis-concept-data-storage.md#accounts) című témakörben talál információt arról, hogy milyen adatok törlődnek a fiók törlésekor.

## <a name="change-pricing-tier"></a>A tarifacsomag módosítása

1.  Az [Azure-ban](https://portal.azure.com)keresse meg a LUIS-előfizetést. Válassza ki a LUIS-előfizetést.
    ![A LUIS-előfizetés megkeresése](./media/luis-usage-tiers/find.png)
1.  Válassza ki **a tarifacsomag** annak érdekében, hogy az elérhető tarifacsomagok megtekintéséhez. 
    ![Tarifacsomagok megtekintése](./media/luis-usage-tiers/subscription.png)
1.  Válassza ki a tarifacsomagot, és válassza a **Kijelölés** lehetőséget a módosítás mentéséhez. 
    ![A LUIS fizetési szint módosítása](./media/luis-usage-tiers/plans.png)
1.  Amikor a díjszabás módosítása befejeződött, egy előugró ablak ellenőrzi az új tarifacsomagot. 
    ![A LUIS fizetési szint ellenőrzése](./media/luis-usage-tiers/updated.png)
1. Ne felejtse el [hozzárendelni ezt](#assign-a-resource-to-an-app) a végpontkulcsot a **Közzététel** lapon, és használja azt minden végponti lekérdezésben. 

## <a name="viewing-azure-resource-metrics"></a>Az Azure-erőforrások metrikáinak megtekintése

### <a name="viewing-azure-resource-summary-usage"></a>Az Azure-erőforrások összegző használatának megtekintése
A LUIS használati információit az Azure-ban tekintheti meg. Az **Áttekintés** lap a legutóbbi összesítő információkat jeleníti meg, beleértve a hívásokat és a hibákat is. Ha luis-végpont-kérelmet, majd azonnal nézze meg az **áttekintése lapot,** legfeljebb öt percet a használat jelenik meg.

![Összegző használat megtekintése](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Az Azure-erőforrások használati diagramjainak testreszabása
A metrikák részletesebb képet adnak az adatokról.

![Alapértelmezett mutatók](./media/luis-usage-tiers/metrics-default.png)

A metrikadiagramokat az időszak és a metrikatípushoz konfigurálhatja. 

![Egyéni metrikák](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Összes tranzakcióküszöbérték riasztás
Ha tudni szeretné, hogy mikor ért el egy bizonyos tranzakciós küszöbértéket, például 10 000 tranzakciót, létrehozhat egy riasztást. 

![Alapértelmezett riasztások](./media/luis-usage-tiers/alert-default.png)

Adjon hozzá egy metrika riasztást az **összes hívás** metrika egy bizonyos időszakra. Adja meg az összes olyan ember e-mail címét, amelyiknek meg kell kapnia a riasztást. Webhookok hozzáadása minden olyan rendszerhez, amelynek meg kell kapnia a riasztást. A logikai alkalmazást is futtathatja, amikor a riasztás aktiválódik. 

## <a name="next-steps"></a>További lépések

* Ismerje [meg, hogyan vezérelheti](luis-how-to-manage-versions.md) a verziók az alkalmazás életciklusát.
* Ismerje meg a fogalmakat, beleértve a [szerzői erőforrást](luis-concept-keys.md#authoring-key) és az adott erőforrás [közreműködőit.](luis-concept-keys.md#contributions-from-other-authors)
* Útmutató szerzői és futásidejű erőforrások [létrehozásához](luis-how-to-azure-subscription.md)
* Áttelepítés az új [szerzői erőforrásra](luis-migration-authoring.md) 
