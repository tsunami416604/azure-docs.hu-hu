---
title: Biztonságos hozzáférés és adatAzure Logic Apps
description: A paraméterek bemenetei, a HTTP-kérelmek eseményindítói, a futtatási előzmények, a Logic app-műveletek és a más szolgáltatásokkal való kapcsolatok Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: f27dfd1f907d106ddb3b1b9dd7534d56380149c2
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385494"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Biztonságos hozzáférés és az adatAzure Logic Apps

A Azure Logic Appsban lévő adathozzáférés szabályozásához és védelméhez a következő területeken állíthatja be a biztonságot:

* [Hozzáférés a HTTP-kérelmek eseményindítóhoz](#secure-triggers)
* [Hozzáférés a Logic app-műveletekhez](#secure-operations)
* [Hozzáférés a futtatási előzményekhez bemenetekhez és kimenetekhez](#secure-run-history)
* [Hozzáférés paraméter bemenetekhez](#secure-action-parameters)
* [Hozzáférés a Logic apps által hívott szolgáltatásokhoz és rendszerekhez](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Hozzáférés a HTTP-kérelmek eseményindítóhoz

Ha a logikai alkalmazás HTTP-kérelem alapú triggert használ, például a [kérést](../connectors/connectors-native-reqres.md) vagy a webhookot, korlátozhatja a hozzáférést, hogy csak a jogosult ügyfelek tudják elindítani a logikai alkalmazást. [](../connectors/connectors-native-webhook.md) A logikai alkalmazások által fogadott összes kérelem titkosítva van, és a SSL (SSL) protokollon keresztül biztosítva van. 

Az alábbi módokon biztosíthatja az ilyen típusú triggerekhez való hozzáférést:

* [Közös hozzáférési aláírások előállítása](#sas)
* [Bejövő IP-címek korlátozása](#restrict-incoming-ip-addresses)
* [Azure Active Directory, OAuth vagy más biztonsági szolgáltatás hozzáadása](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Közös hozzáférésű aláírások (SAS) előállítása

A logikai alkalmazás minden kérelem végpontja [közös hozzáférési aláírással (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) rendelkezik a végpont URL-címében, amely a következő formátumot követi:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Minden URL-cím `sp`tartalmaz `sv`egy, `sig` és egy lekérdezési paramétert a következő táblázatban leírtak szerint:

| Lekérdezési paraméter | Leírás |
|-----------------|-------------|
| `sp` | Megadja az engedélyezett HTTP-metódusok használatának engedélyeit. |
| `sv` | Megadja az aláírás generálásához használandó SAS-verziót. |
| `sig` | Meghatározza az triggerhez való hozzáférés hitelesítéséhez használandó aláírást. Ezt az aláírást a SHA256 algoritmussal hozza létre a rendszer az összes URL-útvonalon és tulajdonságon található titkos hozzáférési kulccsal. Soha nem érhető el vagy nem lett közzétéve, a kulcs titkosítva marad, és a logikai alkalmazás tárolja őket. A logikai alkalmazás csak azokat az eseményindítókat engedélyezi, amelyekben a titkos kulccsal létrehozott érvényes aláírás szerepel. |
|||

A közös hozzáférésű aláírással való hozzáférés biztonságossá tételével kapcsolatos további információkért tekintse meg a jelen témakör következő szakaszait:

* [Hozzáférési kulcsok újragenerálása](#access-keys)
* [Lejáró visszahívási URL-címek létrehozása](#expiring-urls)
* [Elsődleges vagy másodlagos kulccsal rendelkező URL-címek létrehozása](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

Ha bármikor új biztonságos hozzáférési kulcsot szeretne készíteni, használja az Azure REST API vagy Azure Portal. Az összes korábban létrehozott, a régi kulcsot használó URL-cím érvénytelenítve lett, és már nincs engedélye a logikai alkalmazás aktiválására. Az újragenerálás után lekért URL-címek az új hozzáférési kulccsal lesznek aláírva.

1. A Azure Portal nyissa meg azt a logikai alkalmazást, amely rendelkezik az újragenerált kulccsal.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **hozzáférési kulcsok**elemet.

1. Válassza ki azt a kulcsot, amelyet újra szeretne készíteni, majd fejezze be a folyamatot.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Lejáró visszahívási URL-címek létrehozása

Ha egy HTTP-kérelem alapú trigger végpontjának URL-címét más felekkel együtt osztja meg, létrehozhat olyan visszahívási URL-címeket, amelyek meghatározott kulcsokat használnak, és lejárati dátummal rendelkeznek. Így zökkenőmentesen válthat kulcsokat, vagy korlátozhatja a hozzáférést a logikai alkalmazás aktiválására egy adott TimeSpan alapján. Az URL-cím lejárati dátumának megadásához használja a [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), például:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja meg a `NotAfter`tulajdonságot egy JSON-dátum karakterlánc használatával. Ez a tulajdonság egy visszahívási URL-címet ad vissza `NotAfter` , amely csak a dátumig és időpontig érvényes.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Elsődleges vagy másodlagos titkos kulccsal rendelkező URL-címek létrehozása

A HTTP-kérelem alapú eseményindítók visszahívási URL-címeinek létrehozásakor vagy listázásakor megadhatja az URL-cím aláírásához használandó kulcsot. Egy adott kulcs által aláírt URL-cím létrehozásához használja a [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), például:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja meg a `KeyType` tulajdonságot a `Primary` következők `Secondary`egyikével: vagy. Ez a tulajdonság a megadott biztonságos kulcs által aláírt URL-címet adja vissza.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Bejövő IP-címek korlátozása

A közös hozzáférésű aláírással együtt érdemes korlátozni a logikai alkalmazást meghívó ügyfeleket is. Ha például az Azure API Management használatával kezeli a kérelem végpontját, akkor a logikai alkalmazás csak az API Management példány IP-címéről fogadhatja a kérelmeket.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>A bejövő IP-címtartományok korlátozása Azure Portal

1. A Azure Portalban nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **munkafolyamat-beállítások**elemet.

1. A **hozzáférés-vezérlési konfiguráció** > **engedélyezett bejövő IP-címek**területen válassza az **adott IP-címtartományok**lehetőséget.

1. Az **Eseményindítók IP-címtartományok**területén válassza ki az eseményindító által elfogadott IP-címtartományt.

   Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*

Ha azt szeretné, hogy a logikai alkalmazás csak beágyazott logikai alkalmazásként induljon el, az **engedélyezett bejövő IP-címek** listából válassza a **csak más Logic apps**lehetőséget. Ez a lehetőség üres tömböt ír a logikai alkalmazás erőforrásaiba. Így csak a Logic Apps szolgáltatástól érkező hívások (a szülő logikai alkalmazások) indíthatják el a beágyazott logikai alkalmazást.

> [!NOTE]
> Az IP-címektől függetlenül továbbra is futtathat egy olyan logikai alkalmazást, amely egy HTTP-kérelem alapú triggerrel `/triggers/<trigger-name>/run` rendelkezik az Azure REST API vagy a API Management használatával. Ebben a forgatókönyvben azonban továbbra is hitelesítésre van szükség az Azure REST API. Az összes esemény megjelenik az Azure naplóban. Ügyeljen rá, hogy ennek megfelelően állítsa be a hozzáférés-vezérlési házirendeket.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>A bejövő IP-tartományok korlátozása Azure Resource Manager sablonban

Ha [Azure Resource Manager sablonnal](../logic-apps/logic-apps-create-deploy-template.md)automatizálja a logikai alkalmazások központi telepítését, megadhatja az IP-tartományokat `accessControl` `triggers` a logikai alkalmazás erőforrás-definíciójának szakasza alapján, például:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth vagy más biztonsági szolgáltatás hozzáadása

Ha további hitelesítési protokollokat szeretne hozzáadni a logikai alkalmazáshoz, vegye fontolóra az [Azure API Management](../api-management/api-management-key-concepts.md)használatát. Ez a szolgáltatás lehetővé teszi, hogy API-ként tegye elérhetővé a logikai alkalmazást, és minden végponthoz részletes monitorozási, biztonsági, házirend-és dokumentációt biztosítson. API Management közzéteheti a logikai alkalmazás nyilvános vagy privát végpontját, amely Azure Active Directory, OAuth, tanúsítványt vagy más biztonsági szabványokat használhat. Ha API Management kap egy kérést, a szolgáltatás elküldi a kérést a logikai alkalmazásnak, valamint a szükséges átalakításokat vagy korlátozásokat a módszernek megfelelően. Ahhoz, hogy csak API Management aktiválja a logikai alkalmazást, használhatja a logikai alkalmazás bejövő IP-tartományának beállításait.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Hozzáférés a Logic app-műveletekhez

Engedélyezheti, hogy csak bizonyos felhasználók vagy csoportok futtassanak meghatározott műveleteket, például a Logic apps felügyeletét, szerkesztését és megtekintését. Az engedélyeik szabályozása érdekében az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) használatával rendeljen hozzá testreszabott vagy beépített szerepköröket az Azure-előfizetésében található tagokhoz:

* [Logikai alkalmazás közreműködői](../role-based-access-control/built-in-roles.md#logic-app-contributor): Lehetővé teszi a logikai alkalmazások kezelését, de a hozzáférését nem módosíthatja.

* [Logic app-operátor](../role-based-access-control/built-in-roles.md#logic-app-operator): Lehetővé teszi a Logic apps olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissítheti őket.

Annak megakadályozása érdekében, hogy mások módosíthassák vagy töröljék a logikai alkalmazást, használhatja az [Azure erőforrás](../azure-resource-manager/resource-group-lock-resources.md)-zárolást, amely megakadályozza, hogy mások módosíthassák vagy töröljék a termelési erőforrásokat.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Hozzáférés a futtatási előzményekhez

A logikai alkalmazások futtatása során az összes adatok titkosítva vannak az átvitel és a nyugalmi állapotban. Ha a logikai alkalmazás futása befejeződött, megtekintheti a Futtatás előzményeit, beleértve az egyes műveletek állapotával, időtartamával, bemenetével és kimenetével együtt futtatott lépéseket is. Ez a részletes információk betekintést biztosítanak a logikai alkalmazás futtatásának módjára, valamint az esetlegesen felmerülő problémák elhárítására.

Amikor hozzáfér a logikai alkalmazás futtatási előzményeihez, Logic Apps hitelesíti a hozzáférést, és hivatkozásokat biztosít a logikai alkalmazás futtatásához tartozó kérések és válaszok bemeneteit és kimeneteit illetően. A jelszavakat, titkos kulcsokat vagy más bizalmas adatokat kezelő műveletek esetében azonban meg kell akadályozni, hogy mások megtekintsék és hozzáférjenek az adatokhoz. Ha például a logikai alkalmazás a HTTP-műveletek hitelesítéséhez [Azure Key Vault](../key-vault/key-vault-whatis.md) titkos kulcsot kap, akkor a titkos kulcsot el szeretné rejteni a nézetből.

A logikai alkalmazás futtatási előzményeiben található bemenetekhez és kimenetekhez való hozzáférés szabályozásához a következő lehetőségek közül választhat:

* [Korlátozza a hozzáférést az IP-címtartomány alapján](#restrict-ip).

  Ez a beállítás lehetővé teszi a futtatási előzmények biztonságos elérését egy adott IP-címtartomány kérései alapján.

* [Adatok elrejtése a futtatási előzményekből a elhomályosítás használatával](#obfuscate).

  Számos eseményindító és művelet esetében elrejtheti a bemeneteit, kimeneteit vagy mindkettőt egy logikai alkalmazás futtatási előzményeiből.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP-címtartomány általi hozzáférés korlátozása

Korlátozhatja a logikai alkalmazás futtatási előzményeinek bemeneteit és kimeneteit úgy, hogy csak adott IP-címtartományok kérései megtekinthessék ezeket az adatokat. Ha például meg szeretné tiltani a bemenetek és kimenetek elérését, adja meg az IP-címtartományt `0.0.0.0-0.0.0.0`, például:. Csak egy rendszergazdai engedélyekkel rendelkező személy távolíthatja el ezt a korlátozást, amely lehetővé teszi, hogy "igény szerinti" hozzáférést biztosítson a logikai alkalmazás adataihoz. Megadhatja azokat az IP-tartományokat, amelyek a logikai alkalmazások telepítéséhez használt Azure Portal vagy egy Azure Resource Manager sablon alapján korlátozzák.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure Portal IP-tartományának korlátozása

1. A Azure Portalban nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **munkafolyamat-beállítások**elemet.

1. A **hozzáférés-vezérlési konfiguráció** > **engedélyezett bejövő IP-címek**területen válassza az **adott IP-címtartományok**lehetőséget.

1. A **tartalom IP-címtartományok**területén adja meg azokat az IP-címtartományt, amelyek hozzáférhetnek a tartalomhoz a bemenetek és a kimenetek között. 

   Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager sablonban lévő IP-tartományok korlátozása

Ha [Azure Resource Manager sablonnal](../logic-apps/logic-apps-create-deploy-template.md)automatizálja a logikai alkalmazások központi telepítését, megadhatja az IP-tartományokat `accessControl` `contents` a logikai alkalmazás erőforrás-definíciójának szakasza alapján, például:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Adatok elrejtése a futtatási előzményekből a elhomályosítás használatával

Számos eseményindító és művelet rendelkezik olyan beállításokkal, amelyekkel elrejtheti a bemeneteket, kimeneteket vagy mindkettőt egy logikai alkalmazás futtatási előzményeiből. Az alábbi szempontokat érdemes áttekinteni [,](#obfuscation-considerations) ha ezeket a beállításokat használja az adatvédelmek biztosításához.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Biztonságos bemenetek és kimenetek a tervezőben

1. Ha a logikai alkalmazás még nincs megnyitva [](https://portal.azure.com)a Azure Portalban, nyissa meg a logikai alkalmazást a Logic app Designerben.

   ![Minta logikai alkalmazás megnyitása](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. Az aktiválás vagy művelet esetében, ahol az adatvédelmet szeretné biztosítani, kattintson a három pontra ( **..** .), majd válassza a **Beállítások**lehetőséget.

   ![A "beállítások" megnyitása](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Kapcsolja be a **biztonságos bemeneteket**, a **biztonságos kimeneteket**vagy mindkettőt. Ha elkészült, válassza a **Kész** lehetőséget.

   ![Biztonságos bemenetek és kimenetek bekapcsolása](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A művelet vagy az aktiválás mostantól egy lakat ikont jelenít meg a címsorban.

   ![A címsor ikonjának zárolása](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   A korábbi műveletekből származó biztonságos kimeneteket képviselő tokenek a zárolási ikonokat is megjelenítik. Ha például egy ilyen kimenetet választ ki a dinamikus tartalmak listájából egy műveletben való használatra, akkor a jogkivonat zárolási ikont jelenít meg.

   ![Kimenet kiválasztása](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. A logikai alkalmazás futtatása után megtekintheti a futtatási előzményeket.

   1. A logikai alkalmazás **Áttekintés** paneljén válassza ki a megtekinteni kívánt futtatást.

   1. A **logikai alkalmazás futtatása** ablaktáblán bontsa ki az áttekinteni kívánt műveleteket.

      Ha úgy döntött, hogy mindkét bemenetet és kimenetet is biztonságossá teszi, akkor ezek az értékek már rejtve maradnak.

      ![Rejtett adatértékek a futtatási előzményekben](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Biztonságos bemenetek és kimenetek kód nézetben

Az alapul szolgáló trigger vagy művelet definíciójában adja hozzá vagy frissítse `runtimeConfiguration.secureData.properties` a tömböt a következő értékek egyikével vagy mindkettővel:

* `"inputs"`: A bemenetek védelme a futtatási előzményekben.
* `"outputs"`: A kimenetek védelme a futtatási előzményekben.

Az alábbi szempontokat érdemes áttekinteni [,](#obfuscation-considerations) ha ezeket a beállításokat használja az adatvédelmek biztosításához.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>A bemenetek és kimenetek elrejtésével kapcsolatos megfontolások

* Ha a bemeneteket és kimeneteket egy triggerhez vagy művelethez védi, Logic Apps nem küldi el a védett adatokat az Azure Log Analyticsnak. Emellett nem adhat hozzá [nyomon követett tulajdonságokat](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) az adott triggerhez vagy művelethez a figyeléshez.

* A [munkafolyamat-előzmények kezelésére szolgáló Logic apps API](https://docs.microsoft.com/rest/api/logic/) nem ad vissza biztonságos kimenetet.

* Olyan műveletekben, amelyekben biztonságos a bemenetek védelme, vagy amelyek explicit módon biztonságos kimeneteket használnak, és ez a művelet visszaküldi a biztonságos adatokat tartalmazó kimeneteket, manuálisan kell bekapcsolnia a **biztonsági kimeneteket** ebben a műveletben a kimenetek védelméhez.

* Győződjön meg arról, hogy be van kapcsolva a **biztonságos bemenetek** vagy a **biztonságos kimenetek** az alsóbb rétegbeli műveletekben, ahol az adatok védelméhez várható a futtatási előzmények.

  **Biztonságos kimenetek beállítása**

  Ha manuálisan kapcsolja be a **biztonságos kimeneteket** egy triggerben vagy műveletben, a Logic apps a futtatási előzményekben rögzíti ezeket a kimeneteket. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja ezeket a biztonságos kimeneteket bemenetként, Logic Apps elrejti ezt a műveletet a futtatási előzményekben, de *nem engedélyezi* a művelet **biztonságos bevitelének** beállítását.

  ![A legtöbb művelethez bemenetként és alsóbb rétegbeli hatásként biztosított kimenetek](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Az összeállítási, elemzési JSON-és válasz-műveletek csak a **biztonságos bemenetek** beállítással rendelkeznek. Ha be van kapcsolva, a beállítás ezen műveletek kimeneteit is elrejti. Ha ezek a műveletek explicit módon használják a felsőbb rétegbeli biztonságos kimeneteket bemenetként, Logic Apps elrejti ezeket a műveleteket a bemenetek és a kimenetek között, de *nem engedélyezi* ezeket a műveleteket a " **biztonságos bemenetek** " beállításban. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja a rejtett kimeneteket az összeállítás, az elemzés JSON-vagy a válasz-műveletekből bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemeneteit és kimeneteit*.

  ![Biztonságos kimenetek bemenetként, ha az adott műveletekre gyakorolt hatás van](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Biztonságos bemenetek beállítása**

  Ha manuálisan kapcsolja be a **biztonságos bemeneteket** egy triggerben vagy műveletben, a Logic apps a futtatási előzményekben rögzíti ezeket a bemeneteket. Ha egy alsóbb rétegbeli művelet explicit módon az adott triggerből vagy műveletből származó látható kimeneteket használ bemenetként, Logic Apps elrejti ezt az alsóbb rétegbeli műveletet a futtatási előzményekben, de *nem engedélyezi* a **biztonságos bemeneteket** ebben a műveletben, és nem rejti el a műveletet kimenetek.

  ![A legtöbb művelethez kapcsolódó biztonságos bemenetek és alsóbb rétegbeli hatások](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Ha az összeállítás, a JSON és a Response művelet explicit módon használja az triggerből vagy a biztonságos bemenetekkel rendelkező műveletből származó látható kimeneteket, Logic Apps elrejti ezeket a műveleteket a bemeneteken és kimeneteken, de *nem engedélyezi* a művelet **biztonságos bemeneteit** . beállítás. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja a rejtett kimeneteket az összeállítás, az elemzés JSON-vagy a válasz-műveletekből bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemeneteit és kimeneteit*.

  ![Biztonságos bemenetek és alsóbb rétegbeli hatás az egyes műveletekre](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Hozzáférés paraméter bemenetekhez

Ha különböző környezetekben végez üzembe helyezést, érdemes parameterizing a munkafolyamat-definícióban szereplő értékeket, amelyek az adott környezettől függően változnak. Így [Azure Resource Manager sablonnal](../azure-resource-manager/resource-group-authoring-templates.md#parameters) üzembe helyezheti a logikai alkalmazást, védetté teheti a bizalmas adatokat, és biztonságos paramétereket határozhat meg, és ezeket a paramétereket külön-külön adhatja meg a sablon paramétereinek használatával. [ fájl](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Ha például [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)használatával hitelesíti a http-műveleteket, megadhatja és biztonságossá teheti azokat a paramétereket, amelyek elfogadják a hitelesítéshez használt ügyfél-azonosítót és az ügyfél titkos kulcsát. A logikai alkalmazáshoz tartozó paraméterek megadásához használja a `parameters` logikai alkalmazás munkafolyamat-definícióján belüli szakaszt. A logikai alkalmazás szerkesztése vagy a futtatási előzmények megtekintésekor nem kívánt paraméterek értékének megadásához adja meg a paramétereket a vagy `securestring` `secureobject` a típus használatával, és használja a kódolást szükség szerint. Az ilyen típusú paramétereket nem adja vissza a rendszer az erőforrás-definícióval, és nem érhető el, amikor az üzembe helyezés után megtekinti az erőforrást. A paraméter értékének futtatókörnyezetben való eléréséhez használja `@parameters('<parameter-name>')` a kifejezést a munkafolyamat-definícióban. A kifejezés kiértékelése csak futásidőben történik, és a munkafolyamat- [definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md)határozza meg.

> [!NOTE]
> Ha egy HTTP-kérelem fejlécében vagy törzsében egy paramétert használ, akkor ez a paraméter látható lesz, ha megtekinti a logikai alkalmazás futtatási előzményeit és a kimenő HTTP-kérést. Győződjön meg arról, hogy a tartalom-hozzáférési házirendeket is megfelelően állította be. Az engedélyezési fejlécek soha nem láthatók bemeneteken vagy kimeneteken keresztül. Tehát ha van titok, a titkos kód nem kérhető le.

További információ: [biztonságos paraméterek a munkafolyamat-definíciókban](#secure-parameters-workflow) a témakör későbbi részében.

Ha [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters)-sablonokkal automatizálja az üzembe helyezést, megadhatja a biztonságos sablon paramétereit, amelyek kiértékelése a `securestring` telepítéskor történik a és `secureobject` a típus használatával. A sablon paramétereinek megadásához használja a sablon legfelső `parameters` szintű szakaszát, amely külön és eltér a munkafolyamat- `parameters` definíció szakaszával. A sablon paramétereinek értékeinek megadásához használjon egy külön [paraméter-fájlt](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Ha például a titkokat használja, megadhatja és használhatja a biztonságos sablon paramétereit, amelyek lekérik ezeket a titkokat [Azure Key Vaultról](../key-vault/key-vault-whatis.md) a telepítéskor. Ezután hivatkozhat a Key vaultra és a titkos kulcsra a paraméter fájljában. További információkért tekintse meg a következő témaköröket:

* [A Azure Key Vault használata a biztonságos paraméterek értékének átadására az üzembe helyezéskor](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Biztonságos paraméterek Azure Resource Manager](#secure-parameters-deployment-template) -sablonokban a témakör későbbi részében

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Biztonságos paraméterek a munkafolyamat-definíciókban

A logikai alkalmazás munkafolyamat-definíciójában lévő bizalmas adatok védelméhez használjon biztonságos paramétereket, hogy a logikai alkalmazás mentése után ne legyen látható az információ. Tegyük fel például, hogy egy HTTP-művelethez egyszerű hitelesítés szükséges, amely felhasználónevet és jelszót használ. A munkafolyamat-definícióban `parameters` a szakasz a `basicAuthPasswordParam` `securestring` és `basicAuthUsernameParam` a paramétereket a típus használatával határozza meg. A művelet definíciója ezeket a `authentication` paramétereket a szakaszon hivatkozik.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Biztonságos paraméterek Azure Resource Manager-sablonokban

A logikai alkalmazások Resource Manager-sablonjai több `parameters` szakaszt is tartalmaz. A jelszavak, kulcsok, titkos kódok és egyéb bizalmas adatok védelméhez adja meg a biztonságos paramétereket a sablon szintjén és a munkafolyamat-definíciós `securestring` szinten `secureobject` a vagy a típus használatával. Ezután tárolhatja ezeket az értékeket [Azure Key Vault](../key-vault/key-vault-whatis.md) , és a [paraméter fájllal](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) hivatkozhat a Key vaultra és a titkos kulcsra. A sablon ezután beolvassa ezt az információt a telepítéskor. További információ: a [Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/resource-manager-keyvault-parameter.md)átadására a telepítéskor.

További információ az alábbi `parameters` fejezetekről:

* A sablon legfelső szintjén egy `parameters` szakasz határozza meg azokat az értékeket, amelyeket a sablon a *telepítéskor*használ. Ezek az értékek például tartalmazhatnak egy adott központi telepítési környezethez tartozó kapcsolódási karakterláncokat is. Ezeket az értékeket külön [paraméteres fájlban](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)tárolhatja, így az értékek könnyebben módosíthatók.

* A logikai alkalmazás erőforrás-definícióján belül, de a munkafolyamat-definíción `parameters` kívül egy szakasz a munkafolyamat-definíció paramétereinek értékeit határozza meg. Ebben a szakaszban ezeket az értékeket a sablon paramétereit hivatkozó sablon-kifejezések használatával rendelheti hozzá. Ezeket a kifejezéseket a rendszer kiértékeli a telepítéskor.

* A munkafolyamat-definícióban egy `parameters` szakasz határozza meg, hogy a logikai alkalmazás milyen paramétereket használ futásidőben. Ezeket a paramétereket a logikai alkalmazás munkafolyamatán belül hivatkozhat a munkafolyamat-definíciós kifejezések használatával, amelyek kiértékelése futásidőben történik.

Ez a példa több, a `securestring` típust használó biztonságos paraméter-definícióval rendelkező sablont tartalmaz:

| Paraméternév | Leírás |
|----------------|-------------|
| `TemplatePasswordParam` | A munkafolyamat-definíció `basicAuthPasswordParam` paraméterének átadott jelszót fogadó sablon-paraméter |
| `TemplatePasswordParam` | A munkafolyamat-definíció `basicAuthUserNameParam` paraméterének átadandó felhasználónevet megadó sablon-paraméter |
| `basicAuthPasswordParam` | Egy munkafolyamat-definíciós paraméter, amely egy HTTP-műveletben fogadja el az alapszintű hitelesítéshez szükséges jelszót |
| `basicAuthUserNameParam` | Munkafolyamat-definíciós paraméter, amely elfogadja a felhasználónevet az egyszerű hitelesítéshez egy HTTP-műveletben |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Hozzáférés a Logic apps által hívott szolgáltatásokhoz és rendszerekhez

Íme néhány módszer, amelyekkel biztonságossá teheti azokat a végpontokat, amelyekben a logikai alkalmazásnak szüksége van a küldési kérelmek elérésére:

* Hitelesítés hozzáadása a kimenő kérésekhez.

  Ha HTTP-, HTTP + hencegő (nyílt API) vagy webhook művelettel dolgozik, akkor a logikai alkalmazás által elküldett kérelemhez is hozzáadhat hitelesítést. Használhat például egyszerű hitelesítést, tanúsítványalapú hitelesítést vagy Azure Active Directory hitelesítést. További információ: triggerek [vagy műveletek hitelesítése](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* A logikai alkalmazás IP-címeitől való hozzáférés korlátozása.

  A Logic apps-végpontokra irányuló összes hívás a logikai alkalmazások régiói alapján megadott kijelölt IP-címekről származik. Hozzáadhat olyan szűrést, amely csak az adott IP-címekről fogad kérelmeket. Ezeknek az IP-címeknek a megszerzéséhez tekintse meg [Azure Logic apps korlátozásait és konfigurációját](logic-apps-limits-and-config.md#configuration).

* Felügyelt identitásként hitelesítheti és érheti el az erőforrásokat.

  Ha más Azure Active Directory-(Azure AD-) bérlők erőforrásaihoz fér hozzá, a logikai alkalmazás a hitelesítő adatok és a titkos azonosítók helyett a felügyelt identitás (korábbi nevén Managed Service Identity vagy MSI) használatával is hitelesítheti identitását. Az Azure kezeli ezt az identitást, és segít a hitelesítő adatok biztonságossá tételében, mert nem kell a titkokat megadnia vagy elforgatnia. A rendszerhez rendelt felügyelt identitás beállításával és használatával kapcsolatos további információkért lásd: Azure Logic Appsban található [felügyelt identitásokkal rendelkező erőforrások hitelesítése és elérése](../logic-apps/create-managed-service-identity.md).

* Biztonságos kapcsolatok a helyszíni rendszerekkel.

  Azure Logic Apps a biztonságos és megbízható helyszíni kommunikációhoz biztosít integrációt ezekkel a szolgáltatásokkal.

  * Helyszíni adatátjáró

    Azure Logic Apps számos felügyelt összekötő biztosít biztonságos kapcsolatot a helyszíni rendszerekkel, például a fájlrendszerrel, az SQL-, a SharePoint-és a DB2-szolgáltatással. Az átjáró a helyszíni forrásokból érkező adatokat a Azure Service Bus keresztül küldi el titkosított csatornákon. Minden forgalom biztonságos kimenő adatforgalomból származik az átjáró ügynökének. Ismerje meg, [Hogyan működik a](logic-apps-gateway-install.md#gateway-cloud-service)helyszíni adatátjáró.

  * Kapcsolódjon az Azure API Management

    Az [Azure API Management](../api-management/api-management-key-concepts.md) helyszíni kapcsolódási lehetőségeket, például helyek közötti virtuális magánhálózat és ExpressRoute-integrációt biztosít a biztonságos proxyhoz és a helyszíni rendszerekkel való kommunikációhoz. A Logic app Designerben a logikai alkalmazás munkafolyamatában kiválaszthat egy API Management által elérhetővé tett API-t, amely gyors hozzáférést biztosít a helyszíni rendszerekhez.

## <a name="next-steps"></a>További lépések

* [Központi telepítési sablonok létrehozása](logic-apps-create-deploy-template.md)  
* [Logikai alkalmazások figyelése](logic-apps-monitor-your-logic-apps.md)  
* [Logikai alkalmazások hibáinak és problémáinak diagnosztizálása](logic-apps-diagnosing-failures.md)  
* [A logikai alkalmazások telepítésének automatizálása](logic-apps-azure-resource-manager-templates-overview.md)
