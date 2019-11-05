---
title: Biztonságos hozzáférés és adatAzure Logic Apps
description: Biztonság hozzáadása a bemenetek, a kimenetek, a kérelmeken alapuló eseményindítók, a futtatási előzmények, a felügyeleti feladatok és a más erőforrásokhoz való hozzáférés Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 2177ba8b3864e8d453a097b391a18ebbbb5baa11
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499928"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Biztonságos hozzáférés és az adatAzure Logic Apps

A Azure Logic Appsban lévő adathozzáférés szabályozásához és védelméhez a következő területeken állíthatja be a biztonságot:

* [Hozzáférés kérés-alapú eseményindítóhoz](#secure-triggers)
* [Hozzáférés a Logic app-műveletekhez](#secure-operations)
* [Hozzáférés a futtatási előzményekhez bemenetekhez és kimenetekhez](#secure-run-history)
* [Hozzáférés paraméter bemenetekhez](#secure-action-parameters)
* [Hozzáférés a Logic apps által hívott szolgáltatásokhoz és rendszerekhez](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Hozzáférés kérés-alapú eseményindítóhoz

Ha a logikai alkalmazás egy kérelem alapú triggert használ, amely fogadja a bejövő hívásokat vagy kéréseket, például a [kérést](../connectors/connectors-native-reqres.md) vagy a [webhookot](../connectors/connectors-native-webhook.md) , korlátozhatja a hozzáférést, hogy csak a jogosult ügyfelek tudják meghívni a logikai alkalmazást. A logikai alkalmazások által fogadott összes kérelem titkosítva van, és a SSL (SSL) protokollon keresztül biztosítva van.

Az alábbi módokon biztosíthatja az ilyen típusú triggerekhez való hozzáférést:

* [Közös hozzáférési aláírások előállítása](#sas)
* [Bejövő IP-címek korlátozása](#restrict-inbound-ip-addresses)
* [Azure Active Directory, OAuth vagy más biztonsági szolgáltatás hozzáadása](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Közös hozzáférésű aláírások (SAS) előállítása

A logikai alkalmazás minden kérelem végpontja [közös hozzáférési aláírással (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) rendelkezik a végpont URL-címében, amely a következő formátumot követi:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Minden URL-cím tartalmazza a `sp`, `sv`és `sig` lekérdezési paramétert a következő táblázatban leírtak szerint:

| Lekérdezési paraméter | Leírás |
|-----------------|-------------|
| `sp` | Megadja az engedélyezett HTTP-metódusok használatának engedélyeit. |
| `sv` | Megadja az aláírás generálásához használandó SAS-verziót. |
| `sig` | Meghatározza az triggerhez való hozzáférés hitelesítéséhez használandó aláírást. Ezt az aláírást a SHA256 algoritmussal hozza létre a rendszer az összes URL-útvonalon és tulajdonságon található titkos hozzáférési kulccsal. Soha nem érhető el vagy nem lett közzétéve, a kulcs titkosítva marad, és a logikai alkalmazás tárolja őket. A logikai alkalmazás csak azokat az eseményindítókat engedélyezi, amelyekben a titkos kulccsal létrehozott érvényes aláírás szerepel. |
|||

Az SAS-vel való hozzáférés biztonságossá tételével kapcsolatos további információkért tekintse meg a jelen témakör következő szakaszait:

* [Hozzáférési kulcsok újragenerálása](#access-keys)
* [Lejáró visszahívási URL-címek létrehozása](#expiring-urls)
* [Elsődleges vagy másodlagos kulccsal rendelkező URL-címek létrehozása](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

Ha bármikor új biztonságos hozzáférési kulcsot szeretne készíteni, használja az Azure REST API vagy Azure Portal. Az összes korábban létrehozott, a régi kulcsot használó URL-cím érvénytelenítve lett, és már nincs engedélye a logikai alkalmazás aktiválására. Az újragenerálás után lekért URL-címek az új hozzáférési kulccsal lesznek aláírva.

1. A [Azure Portal](https://portal.azure.com)nyissa meg azt a logikai alkalmazást, amely rendelkezik az újragenerált kulccsal.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **hozzáférési kulcsok**elemet.

1. Válassza ki azt a kulcsot, amelyet újra szeretne készíteni, majd fejezze be a folyamatot.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Lejáró visszahívási URL-címek létrehozása

Ha egy kérelem-alapú trigger végpontjának URL-címét megosztja más felekkel, létrehozhat olyan visszahívási URL-címeket, amelyek meghatározott kulcsokat használnak, és lejárati dátummal rendelkeznek. Így zökkenőmentesen válthat kulcsokat, vagy korlátozhatja a hozzáférést a logikai alkalmazás aktiválására egy adott TimeSpan alapján. Az URL-cím lejárati dátumának megadásához használja a [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), például:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja meg a `NotAfter`tulajdonságot egy JSON-dátum karakterlánc használatával. Ez a tulajdonság egy visszahívási URL-címet ad vissza, amely csak a `NotAfter` dátumig és időpontig érvényes.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Elsődleges vagy másodlagos titkos kulccsal rendelkező URL-címek létrehozása

A kérelem alapú trigger visszahívási URL-címeinek létrehozásakor vagy listázásakor megadhatja az URL-cím aláírásához használni kívánt kulcsot. Egy adott kulcs által aláírt URL-cím létrehozásához használja a [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), például:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja meg a `KeyType` tulajdonságot `Primary` vagy `Secondary`. Ez a tulajdonság a megadott biztonságos kulcs által aláírt URL-címet adja vissza.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Bejövő IP-címek korlátozása

A közös hozzáférésű aláírás (SAS) mellett érdemes lehet kifejezetten korlátozni a logikai alkalmazást meghívó ügyfeleket is. Ha például az Azure API Management használatával kezeli a kérelem végpontját, akkor a logikai alkalmazás csak az API Management példány IP-címéről fogadhatja a kérelmeket.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Bejövő IP-címtartományok korlátozása Azure Portal

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **munkafolyamat-beállítások**elemet.

1. A **hozzáférés-vezérlés konfigurációja** > **engedélyezett bejövő IP-címek**területen válassza az **adott IP-címtartományok**lehetőséget.

1. Az **Eseményindítók IP-címtartományok**területén válassza ki az eseményindító által elfogadott IP-címtartományt.

   Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*

Ha azt szeretné, hogy a logikai alkalmazás csak beágyazott logikai alkalmazásként induljon el, az **engedélyezett bejövő IP-címek** listából válassza a **csak más Logic apps**lehetőséget. Ez a lehetőség üres tömböt ír a logikai alkalmazás erőforrásaiba. Így csak a Logic Apps szolgáltatástól érkező hívások (a szülő logikai alkalmazások) indíthatják el a beágyazott logikai alkalmazást.

> [!NOTE]
> Az IP-címektől függetlenül továbbra is futtathat egy olyan logikai alkalmazást, amely egy kérelem-alapú triggerrel rendelkezik `/triggers/<trigger-name>/run` az Azure REST API vagy a API Management használatával. Ebben a forgatókönyvben azonban továbbra is hitelesítésre van szükség az Azure REST API. Az összes esemény megjelenik az Azure naplóban. Ügyeljen rá, hogy ennek megfelelően állítsa be a hozzáférés-vezérlési házirendeket.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Bejövő IP-tartományok korlátozása Azure Resource Manager sablonban

Ha [Resource Manager-sablonok használatával automatizálja a Logic apps üzembe helyezését](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), megadhatja az IP-tartományokat a logikai alkalmazás erőforrás-definíciójának `triggers` szakaszának `accessControl` szakaszának használatával, például:

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

Ha további hitelesítési protokollokat szeretne hozzáadni a logikai alkalmazáshoz, érdemes lehet az [Azure API Management](../api-management/api-management-key-concepts.md) szolgáltatást használni. Ez a szolgáltatás lehetővé teszi a logikai alkalmazás API-ként való kihirdetését, és részletes monitorozást, biztonságot, szabályzatot és dokumentációt biztosít bármely végpont számára. A API Management nyilvános vagy privát végpontot tehet közzé a logikai alkalmazás számára. Ezután az adott végponthoz való hozzáférés engedélyezéséhez Azure Active Directory-, OAuth-, tanúsítvány-vagy egyéb biztonsági szabványokat is használhat. Ha API Management kap egy kérést, a szolgáltatás elküldi a kérést a logikai alkalmazásnak, valamint a szükséges átalakításokat vagy korlátozásokat a módszernek megfelelően. Ahhoz, hogy csak API Management aktiválja a logikai alkalmazást, használhatja a logikai alkalmazás bejövő IP-tartományának beállításait.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Hozzáférés a Logic app-műveletekhez

Csak meghatározott felhasználók vagy csoportok számára engedélyezheti adott feladatok futtatását, például a Logic apps felügyeletét, szerkesztését és megtekintését. Az engedélyeik szabályozásához használja az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) , hogy testreszabott vagy beépített szerepköröket rendeljen az Azure-előfizetése tagjaihoz:

* [Logic app közreműködő](../role-based-access-control/built-in-roles.md#logic-app-contributor): lehetővé teszi a logikai alkalmazások kezelését, de nem tudja módosítani őket.

* [Logikai alkalmazás kezelője](../role-based-access-control/built-in-roles.md#logic-app-operator): lehetővé teszi a Logic apps olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissítheti őket.

Annak megakadályozása érdekében, hogy mások módosíthassák vagy töröljék a logikai alkalmazást, használhatja az [Azure erőforrás-zárolást](../azure-resource-manager/resource-group-lock-resources.md). Ez a funkció megakadályozza, hogy mások módosíthassák vagy töröljék a termelési erőforrásokat.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Hozzáférés a futtatási előzményekhez

A logikai alkalmazások futtatása során az összes adatok [titkosítva](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) vannak az átvitel során TRANSPORT Layer Security (TLS) és [a nyugalmi](../security/fundamentals/encryption-atrest.md)állapotban. Ha a logikai alkalmazás futása befejeződött, megtekintheti a Futtatás előzményeit, beleértve az egyes műveletek állapotával, időtartamával, bemenetével és kimenetével együtt futtatott lépéseket is. Ez a részletes információk betekintést biztosítanak a logikai alkalmazás futtatásának módjára, valamint az esetlegesen felmerülő problémák elhárítására.

Amikor megtekinti a logikai alkalmazás futási előzményeit, Logic Apps hitelesíti a hozzáférést, majd az egyes futtatásokhoz tartozó kérelmek és válaszok bemeneteit és kimeneteit is tartalmazza. A jelszavakat, titkos kulcsokat vagy más bizalmas adatokat kezelő műveletek esetében azonban meg kell akadályozni, hogy mások megtekintsék és hozzáférjenek az adatokhoz. Ha például a logikai alkalmazás a HTTP-műveletek hitelesítéséhez [Azure Key Vault](../key-vault/key-vault-overview.md) titkos kulcsot kap, akkor a titkos kulcsot el szeretné rejteni a nézetből.

A logikai alkalmazás futtatási előzményeiben található bemenetekhez és kimenetekhez való hozzáférés szabályozásához a következő lehetőségek közül választhat:

* [Korlátozza a hozzáférést az IP-címtartomány alapján](#restrict-ip).

  Ez a beállítás lehetővé teszi a futtatási előzmények biztonságos elérését egy adott IP-címtartomány kérései alapján.

* [Adatok elrejtése a futtatási előzményekből a elhomályosítás használatával](#obfuscate).

  Számos eseményindító és művelet esetében elrejtheti a bemeneteit, kimeneteit vagy mindkettőt egy logikai alkalmazás futtatási előzményeiből.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP-címtartomány általi hozzáférés korlátozása

Korlátozhatja a logikai alkalmazás futtatási előzményeinek bemeneteit és kimeneteit úgy, hogy csak adott IP-címtartományok kérései megtekinthessék ezeket az adatokat. Ha például meg szeretné tiltani a bemenetek és kimenetek elérését, adja meg az IP-címtartományt, például `0.0.0.0-0.0.0.0`. Csak egy rendszergazdai engedélyekkel rendelkező személy távolíthatja el ezt a korlátozást, amely lehetővé teszi, hogy "igény szerinti" hozzáférést biztosítson a logikai alkalmazás adataihoz. Megadhatja azokat az IP-tartományokat, amelyek a logikai alkalmazások telepítéséhez használt Azure Portal vagy egy Azure Resource Manager sablon alapján korlátozzák.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure Portal IP-tartományának korlátozása

1. A Azure Portalban nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **munkafolyamat-beállítások**elemet.

1. A **hozzáférés-vezérlés konfigurációja** > **engedélyezett bejövő IP-címek**területen válassza az **adott IP-címtartományok**lehetőséget.

1. A **tartalom IP-címtartományok**területén adja meg azokat az IP-címtartományt, amelyek hozzáférhetnek a tartalomhoz a bemenetek és a kimenetek között. 

   Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager sablonban lévő IP-tartományok korlátozása

Ha [Resource Manager-sablonok használatával automatizálja a Logic apps üzembe helyezését](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), megadhatja az IP-tartományokat a logikai alkalmazás erőforrás-definíciójának `contents` szakaszának `accessControl` szakaszának használatával, például:

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

Számos eseményindító és művelet rendelkezik olyan beállításokkal, amelyekkel elrejtheti a bemeneteket, kimeneteket vagy mindkettőt egy logikai alkalmazás futtatási előzményeiből. Az alábbi [szempontokat érdemes áttekinteni,](#obfuscation-considerations) ha ezeket a beállításokat használja az adatvédelmek biztosításához.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Biztonságos bemenetek és kimenetek a tervezőben

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

   ![Logikai alkalmazás megnyitása a Logic app Designerben](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Az aktiválás vagy művelet esetében, ahol az adatvédelmet szeretné biztosítani, kattintson a három pontra ( **..** .), majd válassza a **Beállítások**lehetőséget.

   ![Trigger vagy művelet beállításainak megnyitása](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Kapcsolja be a **biztonságos bemeneteket**, a **biztonságos kimeneteket**vagy mindkettőt. Ha elkészült, válassza a **Kész** lehetőséget.

   ![Biztonságos bemenetek és kimenetek bekapcsolása](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A művelet vagy az aktiválás mostantól egy lakat ikont jelenít meg a címsorban.

   ![A művelet vagy az trigger címsorában a zárolás ikon látható](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   A korábbi műveletekből származó biztonságos kimeneteket képviselő tokenek a zárolási ikonokat is megjelenítik. Ha például egy ilyen kimenetet választ ki a dinamikus tartalmak listájából egy műveletben való használatra, akkor a jogkivonat zárolási ikont jelenít meg.

   ![Válassza ki a tokent a biztonságos kimenethez](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. A logikai alkalmazás futtatása után megtekintheti a futtatási előzményeket.

   1. A logikai alkalmazás **Áttekintés** paneljén válassza ki a megtekinteni kívánt futtatást.

   1. A **logikai alkalmazás futtatása** ablaktáblán bontsa ki az áttekinteni kívánt műveleteket.

      Ha úgy döntött, hogy mindkét bemenetet és kimenetet is biztonságossá teszi, akkor ezek az értékek már rejtve maradnak.

      ![Rejtett bemenetek és kimenetek a futtatási előzményekben](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Biztonságos bemenetek és kimenetek kód nézetben

Az alapul szolgáló trigger vagy művelet definíciójában adja hozzá vagy frissítse a `runtimeConfiguration.secureData.properties` tömböt az alábbi értékek egyikével vagy mindkettővel:

* `"inputs"`: a bemenetek védelme a futtatási előzményekben.
* `"outputs"`: a kimenetek védelme a futtatási előzményekben.

Az alábbi [szempontokat érdemes áttekinteni,](#obfuscation-considerations) ha ezeket a beállításokat használja az adatvédelmek biztosításához.

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

* Ha olyan művelet kimeneteit szeretné biztonságossá tenni, amely védelmet biztosít a bemenetek számára, vagy explicit módon biztonságos kimeneteket használ, manuálisan kapcsolja be a **biztonságos kimeneteket** az adott műveletben.

* Győződjön meg arról, hogy be van kapcsolva a **biztonságos bemenetek** vagy a **biztonságos kimenetek** az alsóbb rétegbeli műveletekben, ahol az adatok védelméhez várható a futtatási előzmények.

  **Biztonságos kimenetek beállítása**

  Ha manuálisan kapcsolja be a **biztonságos kimeneteket** egy triggerben vagy műveletben, a Logic apps a futtatási előzményekben rögzíti ezeket a kimeneteket. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja ezeket a biztonságos kimeneteket bemenetként, Logic Apps elrejti ezt a műveletet a futtatási előzményekben, de *nem engedélyezi* a művelet **biztonságos bevitelének** beállítását.

  ![A legtöbb művelethez bemenetként és alsóbb rétegbeli hatásként biztosított kimenetek](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Az összeállítási, elemzési JSON-és válasz-műveletek csak a **biztonságos bemenetek** beállítással rendelkeznek. Ha be van kapcsolva, a beállítás ezen műveletek kimeneteit is elrejti. Ha ezek a műveletek explicit módon használják a felsőbb rétegbeli biztonságos kimeneteket bemenetként, Logic Apps elrejti ezeket a műveleteket a bemenetek és a kimenetek között, de *nem engedélyezi* ezeket a műveleteket a " **biztonságos bemenetek** " beállításban. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja a rejtett kimeneteket az összeállítás, az elemzés JSON-vagy a válasz-műveletekből bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemeneteit és kimeneteit*.

  ![Biztonságos kimenetek bemenetként, ha az adott műveletekre gyakorolt hatás van](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Biztonságos bemenetek beállítása**

  Ha manuálisan kapcsolja be a **biztonságos bemeneteket** egy triggerben vagy műveletben, a Logic apps a futtatási előzményekben rögzíti ezeket a bemeneteket. Ha egy alsóbb rétegbeli művelet explicit módon az adott triggerből vagy műveletből származó látható kimeneteket használ bemenetként, Logic Apps elrejti ezt az alsóbb rétegbeli műveletet a futtatási előzményekben, de *nem engedélyezi* a **biztonságos bemeneteket** ebben a műveletben, és nem rejti el a műveletet kimenetek.

  ![A legtöbb művelethez kapcsolódó biztonságos bemenetek és alsóbb rétegbeli hatások](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Ha az összeállítás, a JSON és a Response művelet explicit módon használja az triggerből vagy a biztonságos bemenetekkel rendelkező műveletből származó látható kimeneteket, Logic Apps elrejti ezeket a műveleteket a bemeneteken és kimeneteken, de *nem engedélyezi* a művelet **biztonságos bemeneteit** . beállítás. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja a rejtett kimeneteket az összeállítás, az elemzés JSON-vagy a válasz-műveletekből bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemeneteit és kimeneteit*.

  ![Biztonságos bemenetek és alsóbb rétegbeli hatás az egyes műveletekre](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Hozzáférés paraméter bemenetekhez

Ha különböző környezetekben végez üzembe helyezést, érdemes parameterizing a munkafolyamat-definícióban szereplő értékeket, amelyek az adott környezettől függően változnak. Így elkerülhetők a rögzített adatok egy [Azure Resource Manager sablonnal](../azure-resource-manager/template-deployment-overview.md) a logikai alkalmazás üzembe helyezéséhez, a bizalmas adatok védelméhez a biztonságos paraméterek meghatározásával, valamint az adatok továbbítása külön bemenetként a [sablon paramétereinek](../azure-resource-manager/template-parameters.md) használatával egy [paraméter-fájl](../azure-resource-manager/resource-manager-parameter-files.md).

Ha például [Azure Active Directory](#azure-active-directory-oauth-authentication)használatával hitelesíti a http-műveleteket, megadhatja és biztonságossá teheti azokat a paramétereket, amelyek elfogadják a hitelesítéshez használt ügyfél-azonosítót és az ügyfél titkos kulcsát. Ha ezeket a paramétereket a logikai alkalmazásban szeretné megadni, használja a Logic apps munkafolyamat-definíciójának `parameters` szakaszát, és a Resource Manager-sablont a telepítéshez. A logikai alkalmazás szerkesztése vagy a futtatási előzmények megtekintésekor nem kívánt paraméterek elrejtéséhez adja meg a paramétereket a `securestring` vagy `secureobject` típus használatával, és szükség szerint használja a kódolást. Az ilyen típusú paramétereket nem adja vissza a rendszer az erőforrás-definícióval, és nem érhető el, amikor az üzembe helyezés után megtekinti az erőforrást. Ha a paraméter értékeit futásidőben szeretné elérni, használja a `@parameters('<parameter-name>')` kifejezést a munkafolyamat-definíciójában. A kifejezés kiértékelése csak futásidőben történik, és a munkafolyamat- [definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md)határozza meg.

> [!NOTE]
> Ha a kérelem fejlécében vagy törzsében paramétert használ, akkor ez a paraméter látható lesz a logikai alkalmazás futtatási előzményeinek és a kimenő HTTP-kérések megtekintésekor. Győződjön meg arról, hogy a tartalom-hozzáférési házirendeket is megfelelően állította be. A futtatási előzményekben lévő bemenetek és kimenetek elrejtéséhez is használhatja a [elhomályosítás](#obfuscate) lehetőséget. Az engedélyezési fejlécek soha nem láthatók bemeneteken vagy kimeneteken keresztül. Tehát ha van titok, a titkos kód nem kérhető le.

További információt a jelen témakör következő részeiben talál:

* [Biztonságos paraméterek a munkafolyamat-definíciókban](#secure-parameters-workflow)
* [Adatok elrejtése a futtatási előzményekből a elhomályosítás használatával](#obfuscate)

Ha [Resource Manager-sablonok használatával automatizálja a Logic apps üzembe helyezését](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), megadhatja a biztonságos [sablon paramétereit](../azure-resource-manager/template-parameters.md), amelyeket a rendszer a telepítéskor értékel ki a `securestring` és a `secureobject` típus használatával. A sablon paramétereinek definiálásához használja a sablon legfelső szintű `parameters` szakaszát, amely külön és eltér a munkafolyamat-definíció `parameters` szakaszával. A sablon paramétereinek értékeinek megadásához használjon egy külön [paraméter-fájlt](../azure-resource-manager/resource-manager-parameter-files.md).

Ha például a titkokat használja, megadhatja és használhatja a biztonságos sablon paramétereit, amelyek lekérik ezeket a titkokat [Azure Key Vaultról](../key-vault/key-vault-overview.md) a telepítéskor. Ezután hivatkozhat a Key vaultra és a titkos kulcsra a paraméter fájljában. További információkért tekintse meg a következő témaköröket:

* [Bizalmas értékek átadása az üzembe helyezés során Azure Key Vault használatával](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Biztonságos paraméterek Azure Resource Manager-sablonokban](#secure-parameters-deployment-template) a témakör későbbi részében

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Biztonságos paraméterek a munkafolyamat-definíciókban

A logikai alkalmazás munkafolyamat-definíciójában lévő bizalmas adatok védelméhez használjon biztonságos paramétereket, hogy a logikai alkalmazás mentése után ne legyen látható az információ. Tegyük fel például, hogy egy HTTP-művelethez egyszerű hitelesítés szükséges, amely felhasználónevet és jelszót használ. A munkafolyamat-definícióban a `parameters` szakasz a `securestring` típus használatával határozza meg a `basicAuthPasswordParam` és a `basicAuthUsernameParam` paramétereket. A művelet definíciója ezeket a paramétereket a `authentication` szakaszban hivatkozik.

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

A logikai alkalmazások [Resource Manager-sablonjai](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) több `parameters` szakaszt is tartalmaz. A jelszavak, kulcsok, titkos kódok és egyéb bizalmas adatok védelméhez adja meg a biztonságos paramétereket a sablon szintjén és a munkafolyamat-definíciós szinten a `securestring` vagy `secureobject` típus használatával. Ezután tárolhatja ezeket az értékeket [Azure Key Vault](../key-vault/key-vault-overview.md) , és a [paraméter fájllal](../azure-resource-manager/resource-manager-parameter-files.md) hivatkozhat a Key vaultra és a titkos kulcsra. A sablon ezután beolvassa ezt az információt a telepítéskor. További információ: [bizalmas értékek átadása az üzembe helyezés során Azure Key Vault használatával](../azure-resource-manager/resource-manager-keyvault-parameter.md).

További információ ezekről a `parameters`i fejezetekről:

* A sablon legfelső szintjén egy `parameters` szakasz határozza meg a sablon által a *telepítéskor*használt értékek paramétereit. Ezek az értékek például tartalmazhatnak egy adott központi telepítési környezethez tartozó kapcsolódási karakterláncokat is. Ezeket az értékeket külön [paraméteres fájlban](../azure-resource-manager/resource-manager-parameter-files.md)tárolhatja, így az értékek könnyebben módosíthatók.

* A logikai alkalmazás erőforrás-definícióján belül, de a munkafolyamat-definíción kívül egy `parameters` szakasz határozza meg a munkafolyamat-definíció paramétereinek értékeit. Ebben a szakaszban ezeket az értékeket a sablon paramétereit hivatkozó sablon-kifejezések használatával rendelheti hozzá. Ezeket a kifejezéseket a rendszer kiértékeli a telepítéskor.

* A munkafolyamat-definícióban egy `parameters` szakasz határozza meg, hogy a logikai alkalmazás milyen paramétereket használ futásidőben. Ezeket a paramétereket a logikai alkalmazás munkafolyamatán belül hivatkozhat a munkafolyamat-definíciós kifejezések használatával, amelyek kiértékelése futásidőben történik.

Ez a példa több olyan biztonságos paraméter-definíciót tartalmaz, amelyek a `securestring` típust használják:

| Paraméter neve | Leírás |
|----------------|-------------|
| `TemplatePasswordParam` | A munkafolyamat-definíció `basicAuthPasswordParam` paramétereként átadott jelszót megadó sablon-paraméter |
| `TemplateUsernameParam` | Egy olyan sablon paraméter, amely fogadja a felhasználónevet, amelyet a rendszer a munkafolyamat-definíció `basicAuthUserNameParam` paraméterének továbbít. |
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

Íme néhány módszer, amelyekkel biztonságossá teheti a logikai alkalmazástól érkező hívásokat vagy kéréseket fogadó végpontokat:

* Hitelesítés hozzáadása a kimenő kérelmekhez.

  Ha olyan HTTP-alapú triggerrel vagy művelettel dolgozik, amely kimenő hívásokat végez, például HTTP, HTTP + henceg vagy webhook, akkor a logikai alkalmazás által küldött kérelemhez is hozzáadhat hitelesítést. Használhat például egyszerű hitelesítést, ügyféltanúsítvány-alapú hitelesítést, [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) hitelesítést vagy felügyelt identitást. További információ: a [kimenő hívások hitelesítésének hozzáadása](#add-authentication-outbound) a témakör későbbi részében.

* A logikai alkalmazás IP-címeitől való hozzáférés korlátozása.

  A Logic apps-végpontokra irányuló összes hívás a logikai alkalmazások régiói alapján megadott kijelölt IP-címekről származik. Hozzáadhat olyan szűrést, amely csak az adott IP-címekről fogad kérelmeket. Ezeknek az IP-címeknek a megszerzéséhez tekintse meg [Azure Logic apps korlátozásait és konfigurációját](logic-apps-limits-and-config.md#configuration).

* Biztonságos kapcsolatok a helyszíni rendszerekkel.

  Azure Logic Apps a biztonságos és megbízható helyszíni kommunikációhoz biztosít integrációt ezekkel a szolgáltatásokkal.

  * Helyi adatátjáró

    Azure Logic Apps számos felügyelt összekötő biztosít biztonságos kapcsolatot a helyszíni rendszerekkel, például a fájlrendszerrel, az SQL-, a SharePoint-és a DB2-szolgáltatással. Az átjáró a helyszíni forrásokból érkező adatokat a Azure Service Bus keresztül küldi el titkosított csatornákon. Minden forgalom biztonságos kimenő adatforgalomból származik az átjáró ügynökének. Ismerje meg, [Hogyan működik a helyszíni adatátjáró](logic-apps-gateway-install.md#gateway-cloud-service).

  * Kapcsolódjon az Azure API Management

    Az [Azure API Management](../api-management/api-management-key-concepts.md) helyszíni kapcsolódási lehetőségeket, például helyek közötti virtuális magánhálózat és ExpressRoute-integrációt biztosít a biztonságos proxyhoz és a helyszíni rendszerekkel való kommunikációhoz. A Logic app Designerben a logikai alkalmazás munkafolyamatában kiválaszthat egy API Management által elérhetővé tett API-t, amely gyors hozzáférést biztosít a helyszíni rendszerekhez.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Hitelesítés hozzáadása a kimenő hívásokhoz

A HTTP-és HTTPS-végpontok különböző típusú hitelesítést támogatnak. A kimenő hívások vagy a végpontokhoz hozzáférő kérelmek elvégzéséhez használt trigger vagy művelet alapján a különböző hitelesítési típusok közül választhat. Győződjön meg arról, hogy védi a logikai alkalmazás által kezelt bizalmas adatokat, biztonságos paramétereket használ, és szükség szerint kódolja az adatokat. A paraméterek használatával és biztonságossá tételével kapcsolatos további információkért lásd: [hozzáférés paraméter bemenetekhez](#secure-action-parameters).

| Hitelesítés típusa | Támogató |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + hencegés, HTTP webhook |
| [Ügyféltanúsítvány](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + hencegés, HTTP webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + hencegés, HTTP webhook |
| [Nyers](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + hencegés, HTTP webhook |
| [Felügyelt identitás](#managed-identity-authentication) (csak rendszerhez rendelt) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + hencegés, HTTP webhook |
|||

> [!NOTE]
> A Logic app Designerben előfordulhat, hogy egyes eseményindítók és műveletek elrejtik **a hitelesítési tulajdonságot** , ahol megadhatja a hitelesítési típust. Ha szeretné, hogy a tulajdonság megjelenjen ezekben az esetekben, az trigger vagy a művelet területen nyissa meg az **új paraméter hozzáadása** listát, és válassza a **hitelesítés**lehetőséget. További információ: [hozzáférés hitelesítése felügyelt identitással](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Alapszintű hitelesítés

Ha az [alapszintű](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) beállítás elérhető, akkor a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | Basic | A használni kívánt hitelesítési típus |
| **Felhasználónév** | `username` | Igen | <*felhasználónév*>| A cél szolgáltatási végponthoz való hozzáférés hitelesítéséhez használt Felhasználónév |
| **Jelszó** | `password` | Igen | <*jelszó*> | A cél szolgáltatási végponthoz való hozzáférés hitelesítéséhez használt jelszó |
||||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kifejezésekkel érheti el ezeket a paramétereket futásidőben. Ez a példa a HTTP-művelet definíciója meghatározza a hitelesítési `type` `Basic` és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékeinek lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés

Ha az [ügyféltanúsítvány](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) lehetőség elérhető, akkor a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | **Ügyféltanúsítvány** <br>vagy <br>`ClientCertificate` | A SSL-(SSL-) Ügyféltanúsítványok esetében használandó hitelesítési típus. Míg az önaláírt tanúsítványok támogatottak, az önaláírt tanúsítványok nem támogatottak az SSL-hez. |
| **Pfx** | `pfx` | Igen | <*kódolású-pfx-file-content*> | A Base64 kódolású tartalom egy személyes információcsere (PFX) fájlból |
| **Jelszó** | `password`| Igen | <*jelszó – pfx-fájl*> | A PFX-fájl eléréséhez használt jelszó |
|||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kifejezésekkel érheti el ezeket a paramétereket futásidőben. Ez a példa a HTTP-művelet definíciója meghatározza a hitelesítési `type` `ClientCertificate` és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékeinek lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

A szolgáltatások ügyféltanúsítvány-alapú hitelesítéssel történő biztonságossá tételével kapcsolatos további információkért tekintse meg a következő témaköröket:

* [API-k biztonságossá tétele az Azure-beli ügyféltanúsítvány-alapú hitelesítés használatával API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Az ügyféltanúsítvány-alapú hitelesítést használó Azure-API Management](../api-management/api-management-howto-mutual-certificates.md)
* [A REST-szolgáltatás biztonságossá tétele Ügyféltanúsítványok használatával](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
* [Tanúsítvány hitelesítő adatai az alkalmazás hitelesítéséhez](../active-directory/develop/active-directory-certificate-credentials.md)
* [Használjon SSL-tanúsítványt az alkalmazás kódjában Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth-hitelesítés

Ha a [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) lehetőség elérhető, akkor a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | **Active Directory OAuth** <br>vagy <br>`ActiveDirectoryOAuth` | A használni kívánt hitelesítési típus. A Logic Apps jelenleg a [OAuth 2,0 protokollt](../active-directory/develop/v2-overview.md)követi. |
| **Bérlő** | `tenant` | Igen | <*bérlő-azonosító*> | Az Azure AD-bérlő bérlői azonosítója |
| **Célközönség** | `audience` | Igen | *erőforrás-engedélyezés* <> | Az engedélyezéshez használni kívánt erőforrás, például `https://management.core.windows.net/` |
| **Ügyfél-azonosító** | `clientId` | Igen | <*ügyfél-azonosító*> | Az engedélyezést kérő alkalmazás ügyfél-azonosítója |
| **Hitelesítő adat típusa** | `credentialType` | Igen | Tanúsítvány <br>vagy <br>Titkos | Az ügyfél által a hitelesítés kérelmezéséhez használt hitelesítő adat típusa. Ez a tulajdonság és érték nem jelenik meg a logikai alkalmazás alapjául szolgáló definícióban, de meghatározza a kiválasztott hitelesítőadat-típushoz megjelenő tulajdonságokat. |
| **Titkos kód** | `secret` | Igen, de csak a "titkos" hitelesítő adatok típusához | <*ügyfél-titkos*> | Az engedélyezést kérő ügyfél titka |
| **Pfx** | `pfx` | Igen, de csak a "tanúsítvány" hitelesítő adat típusa esetén | <*kódolású-pfx-file-content*> | A Base64 kódolású tartalom egy személyes információcsere (PFX) fájlból |
| **Jelszó** | `password` | Igen, de csak a "tanúsítvány" hitelesítő adat típusa esetén | <*jelszó – pfx-fájl*> | A PFX-fájl eléréséhez használt jelszó |
| **Hitelesítésszolgáltató** | `authority` | Nem | <*URL-cím-a-Authority-token-kiállító*> | A hitelesítési jogkivonatot biztosító szolgáltató URL-címe. Alapértelmezés szerint ez az érték `https://login.windows.net`. <p>**Megjegyzés**: Ha ezt a tulajdonságot szeretné láthatóvá tenni a tervezőben, az triggeren vagy a műveleten nyissa meg az **új paraméter hozzáadása** listát, és válassza a **hitelesítésszolgáltató**lehetőséget. |
|||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kifejezésekkel érheti el ezeket a paramétereket futásidőben. Ez a példa a HTTP-művelet definíciója meghatározza a hitelesítési `type` `ActiveDirectoryOAuth`ként, a hitelesítő adat típusát `Secret`ként, és a [Parameters () függvénnyel](../logic-apps/workflow-definition-language-functions-reference.md#parameters) beolvassa a paraméterek értékét:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Nyers hitelesítés

Ha a **RAW** beállítás elérhető, akkor használhatja ezt a hitelesítési típust, ha olyan [hitelesítési sémákat](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) kell használnia, amelyek nem követik a [OAuth 2,0 protokollt](https://oauth.net/2/). Ezzel a típussal manuálisan hozza létre a kimenő kérelemmel elküldött engedélyezési fejléc értékét, és adja meg a fejléc értékét az triggerben vagy a műveletben.

Íme például egy minta fejléc egy HTTPS-kérelemhez, amely a [OAuth 1,0 protokollt](https://tools.ietf.org/html/rfc5849)követi:

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

A nyers hitelesítést támogató triggerben vagy műveletben a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | nyers | A használni kívánt hitelesítési típus |
| **Érték** | `value` | Igen | <*Authorization-header-value*> | A hitelesítéshez használandó engedélyezési fejléc értéke |
||||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kifejezésekkel érheti el ezeket a paramétereket futásidőben. Ez a példa a HTTP-művelet definíciója meghatározza a hitelesítési `type` `Raw`ként, és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékeinek lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Felügyelt identitás hitelesítése

Ha a [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) lehetőség elérhető, a logikai alkalmazás a rendszer által hozzárendelt identitás használatával hitelesíti az erőforrásokhoz való hozzáférést más Azure Active Directory-(Azure ad-) bérlők számára bejelentkezés nélkül. Az Azure kezeli ezt az identitást, és segít a hitelesítő adatok biztonságossá tételében, mert nem kell a titkokat megadnia vagy elforgatnia. További információ az Azure [AD-hitelesítés felügyelt identitásait támogató Azure-szolgáltatásokról](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Ahhoz, hogy a logikai alkalmazás használhassa a rendszer által hozzárendelt identitást, kövesse az [Azure-erőforrásokhoz való hozzáférés hitelesítése a Azure Logic apps felügyelt identitások használatával](../logic-apps/create-managed-service-identity.md)című témakör lépéseit. Ezekkel a lépésekkel engedélyezheti a felügyelt identitást a logikai alkalmazáson, és beállíthatja az identitás hozzáférését a cél Azure-erőforráshoz.

2. Mielőtt egy Azure-függvény használhatja a rendszer által hozzárendelt identitást, először [engedélyezze az Azure functions hitelesítést](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

3. Az triggerben vagy a műveletben, ahol a felügyelt identitást szeretné használni, a következő tulajdonságértékeket kell megadnia:

   | Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
   |---------------------|-----------------|----------|-------|-------------|
   | **Hitelesítés** | `type` | Igen | **Felügyelt identitás** <br>vagy <br>`ManagedServiceIdentity` | A használni kívánt hitelesítési típus |
   | **Célközönség** | `audience` | Igen | <*cél – erőforrás-azonosító*> | Az elérni kívánt cél erőforráshoz tartozó erőforrás-azonosító. <p>A `https://storage.azure.com/` például az összes Storage-fiók esetében érvényesek a hozzáférési jogkivonatok. Megadhat azonban egy gyökérszintű szolgáltatás URL-címét is, például `https://fabrikamstorageaccount.blob.core.windows.net` egy adott Storage-fiókhoz. <p>**Megjegyzés**: Előfordulhat, hogy ez a tulajdonság valamilyen Eseményindítóban vagy műveletben el van rejtve. A tulajdonság láthatóvá tételéhez az triggerben vagy a műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **célközönség**lehetőséget. <p><p>**Fontos**: Ügyeljen arra, hogy a célként megadott erőforrás-azonosító pontosan egyezzen az Azure ad által várt értékkel, beleértve a szükséges záró perjeleket is. Így az összes Azure Blob Storage-fiókhoz tartozó `https://storage.azure.com/` erőforrás-AZONOSÍTÓhoz záró perjel szükséges. Egy adott Storage-fiók erőforrás-azonosítója azonban nem igényel záró perjelet. Az erőforrás-azonosítók megkereséséhez tekintse meg az [Azure ad-t támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kifejezésekkel érheti el ezeket a paramétereket futásidőben. Ez a példa a HTTP-művelet definíciója meghatározza a hitelesítési `type` `ManagedServiceIdentity` és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékeinek lekéréséhez:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>További lépések

* [Üzembe helyezés automatizálása Azure Logic Apps](logic-apps-azure-resource-manager-templates-overview.md)  
* [Logikai alkalmazások figyelése](logic-apps-monitor-your-logic-apps.md)  
* [Logikai alkalmazások hibáinak és problémáinak diagnosztizálása](logic-apps-diagnosing-failures.md)  
* [A logikai alkalmazások telepítésének automatizálása](logic-apps-azure-resource-manager-templates-overview.md)
