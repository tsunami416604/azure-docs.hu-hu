---
title: Hozzáférés- és adatvédelem
description: A bemenetekhez, kimenetekhez, kérésalapú eseményindítókhoz való biztonságos hozzáférés, az előzmények futtatása, a felügyeleti feladatok és az Azure Logic Apps egyéb erőforrásaihoz való hozzáférés
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657163"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Biztonságos hozzáférés és adatok az Azure Logic-alkalmazásokban

Az Azure Logic Apps hozzáférésének szabályozásához és az adatok védelméhez az alábbi területeken állíthatja be a biztonságot:

* [Hozzáférés a kérelemalapú eseményindítókhoz](#secure-triggers)
* [Hozzáférés a logikai alkalmazás műveleteihez](#secure-operations)
* [Hozzáférés az előzménybemenetek és -kimenetek futtatásához](#secure-run-history)
* [Hozzáférés a paraméterbemenetekhez](#secure-action-parameters)
* [Hozzáférés a logikai alkalmazásokból hívott szolgáltatásokhoz és rendszerekhez](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Hozzáférés a kérelemalapú eseményindítókhoz

Ha a logikai alkalmazás egy kérelem-alapú eseményindítót használ, amely bejövő hívásokat vagy kéréseket fogad, például a [Kérés](../connectors/connectors-native-reqres.md) vagy a [Webhook](../connectors/connectors-native-webhook.md) eseményindítót, korlátozhatja a hozzáférést, így csak a jogosult ügyfelek hívhatják meg a logikai alkalmazást. A logikai alkalmazás által fogadott összes kérelem titkosított és biztonságos a Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL), protokoll.

Az alábbiakban olyan lehetőségeket talál, amelyek segítségével biztonságos hozzáférést biztosíthat ehhez az eseményindítótípushoz:

* [Megosztott hozzáférési aláírások létrehozása](#sas)
* [Bejövő IP-címek korlátozása](#restrict-inbound-ip-addresses)
* [Azure Active Directory OAuth vagy más biztonság hozzáadása](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Megosztott hozzáférési aláírások (SAS) létrehozása

A logikai alkalmazások minden kérelemvégpontja rendelkezik egy [megosztott hozzáférésű aláírással (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) a végpont URL-címében, amely a következő formátumot követi:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Minden URL `sp`tartalmazza `sv`a `sig` , , és a lekérdezés paramétert a táblázatban leírtak szerint:

| Lekérdezési paraméter | Leírás |
|-----------------|-------------|
| `sp` | Megadja a használható HTTP-metódusok engedélyeit. |
| `sv` | Megadja az aláírás létrehozásához használandó SAS-verziót. |
| `sig` | Megadja az eseményindítóhoz való hozzáférés hitelesítéséhez használandó aláírást. Ezt az aláírást az SHA256 algoritmus generálja, amely az ÖSSZES URL-útvonalon és -tulajdonságon titkos hozzáférési kulccsal rendelkezik. Soha nem elérhető vagy közzétéve, ez a kulcs titkosítva marad, és a logikai alkalmazással együtt tárolódik. A logikai alkalmazás csak azokat az eseményindítókat engedélyezi, amelyek a titkos kulccsal létrehozott érvényes aláírást tartalmaznak. |
|||

A Hozzáférés SAS-sel való védelméről a jelen témakör alábbi szakaszaiban olvashat bővebben:

* [Hozzáférési kulcsok újragenerálása](#access-keys)
* [Lejáró visszahívási URL-címek létrehozása](#expiring-urls)
* [URL-címek létrehozása elsődleges vagy másodlagos kulccsal](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

Ha bármikor új biztonsági hozzáférési kulcsot szeretne létrehozni, használja az Azure REST API-t vagy az Azure Portalt. A régi kulcsot használó összes korábban létrehozott URL-cím érvénytelenné válik, és már nincs engedélye a logikai alkalmazás aktiválásához. A regenerálás után beolvasott URL-címek az új hozzáférési kulccsal vannak aláírva.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást, amely rendelkezik a regenerálni kívánt kulccsal.

1. A logikai alkalmazás menüjének **Beállítások**területén válassza a **Hozzáférési kulcsok lehetőséget.**

1. Jelölje ki a újragenerálni kívánt kulcsot, és fejezze be a folyamatot.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Lejáró visszahívási URL-címek létrehozása

Ha megosztja a végpont URL-címét egy kérelemalapú eseményindítómás féllel, létrehozhat visszahívási URL-eket, amelyek meghatározott kulcsokat használnak, és lejárati dátummal rendelkeznek. Így zökkenőmentesen görgetheti a kulcsokat, vagy korlátozhatja a logikai alkalmazás egy adott időtartomány alapján történő aktiválásához való hozzáférést. Url-cím lejárati dátumának megadásához használja a [Logic Apps REST API-t,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)például:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja `NotAfter`meg a tulajdonságot egy JSON dátumkarakterlánc használatával. Ez a tulajdonság olyan visszahívási URL-címet `NotAfter` ad vissza, amely csak a dátumés időpont végéig érvényes.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>URL-címek létrehozása elsődleges vagy másodlagos titkos kulccsal

Amikor visszahívási URL-címeket hoz létre vagy sorol fel egy kérelemalapú eseményindítóhoz, megadhatja az URL aláírásához használandó kulcsot. Egy adott kulcs által aláírt URL létrehozásához használja a [Logic Apps REST API-t,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)például:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A testben adja `KeyType` meg `Primary` az `Secondary`ingatlant vagy a . Ez a tulajdonság a megadott biztonsági kulcs által aláírt URL-címet adja vissza.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Bejövő IP-címek korlátozása

A megosztott hozzáférésű aláírás (SAS) mellett érdemes lehet kifejezetten korlátozni a logikai alkalmazást hívó ügyfeleket. Ha például az Azure API Management használatával kezeli a kérelemvégpontot, korlátozhatja a logikai alkalmazást, hogy csak az API Management-példány IP-címéről érkező kéréseket fogadja el.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Bejövő IP-tartományok korlátozása az Azure Portalon

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások**csoportban válassza a **Munkafolyamat-beállítások lehetőséget.**

1. A **Hozzáférés-vezérlés konfigurációJa** > **Engedélyezett bejövő IP-címek csoportban**válassza a Speciális **IP-tartományok lehetőséget.**

1. Az **eseményindítók IP-tartományai csoportban**adja meg azokat az IP-címtartományokat, amelyeket az eseményindító elfogad.

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

Ha azt szeretné, hogy a logikai alkalmazás csak beágyazott logikai alkalmazásként aktiválódjon, az **Engedélyezett bejövő IP-címek** listából válassza **a Csak más logikai alkalmazások**lehetőséget. Ez a beállítás egy üres tömböt ír a logikai alkalmazás erőforrásba. Így csak a Logic Apps szolgáltatásból (szülő logikai alkalmazások) érkező hívások aktiválhatják a beágyazott logikai alkalmazást.

> [!NOTE]
> Az IP-címtől függetlenül továbbra is futtathat egy olyan `/triggers/<trigger-name>/run` logikai alkalmazást, amely az Azure REST API-n keresztül vagy az API Management en keresztül rendelkezik egy kérelemalapú eseményindítóval. Ez a forgatókönyv azonban továbbra is hitelesítést igényel az Azure REST API-val. Minden esemény megjelenik az Azure-naplóban. Győződjön meg arról, hogy a hozzáférés-vezérlési házirendeket ennek megfelelően állította be.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Bejövő IP-tartományok korlátozása az Azure Resource Manager-sablonban

Ha [erőforrás-kezelői sablonok használatával automatizálja a logikai alkalmazások központi telepítését,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)megadhatja az IP-tartományokat a `accessControl` szakasz használatával a `triggers` logikai alkalmazás erőforrás-definíciójának szakaszával, például:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory OAuth vagy más biztonság hozzáadása

További engedélyezési protokollok hozzáadása a logikai alkalmazáshoz, fontolja meg az [Azure API Management](../api-management/api-management-key-concepts.md) szolgáltatás használatát. Ez a szolgáltatás segít a logikai alkalmazás API-ként való elérhetővé, és gazdag figyelés, biztonság, szabályzat és dokumentáció bármely végponthoz kínál. Az API Management nyilvános vagy privát végpontot tehet közzé a logikai alkalmazáshoz. A végponthoz való hozzáférés engedélyezéséhez használhatja [az Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication) [ügyféltanúsítvány](#client-certificate-authentication)vagy más biztonsági szabványok at az adott végponthoz való hozzáférés engedélyezéséhez. Amikor az API Management kérést kap, a szolgáltatás elküldi a kérelmet a logikai alkalmazásnak, és a szükséges átalakításokat vagy korlátozásokat is elvégezve az út mentén. Ha azt szeretné, hogy csak az API Management indítsa el a logikai alkalmazást, használhatja a logikai alkalmazás bejövő IP-tartomány beállításait.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Hozzáférés a logikai alkalmazás műveleteihez

Csak bizonyos felhasználók nak vagy csoportoknak engedélyezheti bizonyos feladatok futtatását, például a logikai alkalmazások kezelését, szerkesztését és megtekintését. Az engedélyek szabályozásához használja az [Azure szerepköralapú hozzáférés-vezérlést (RBAC),](../role-based-access-control/role-assignments-portal.md) hogy személyre szabott vagy beépített szerepköröket rendelhetaz Azure-előfizetés tagjaihoz:

* [Logic App Közreműködő:](../role-based-access-control/built-in-roles.md#logic-app-contributor)Lehetővé teszi a logikai alkalmazások kezelését, de nem módosíthatja a hozzáférést.

* [Logic App Operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti vagy frissítheti őket.

Ha meg szeretné akadályozni, hogy mások módosítsák vagy kiirtsák a logikai alkalmazást, használhatja az [Azure Resource Lock](../azure-resource-manager/management/lock-resources.md)szolgáltatást. Ez a funkció megakadályozza, hogy mások módosítsák vagy kiirtsák a termelési erőforrásokat.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Hozzáférés az előzményadatok futtatásához

A logikai alkalmazások futtatása során az összes adat [titkosítva](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) lesz az átvitel során a Transport Layer Security (TLS) használatával és [inaktív állapotban.](../security/fundamentals/encryption-atrest.md) Amikor a logikai alkalmazás futása befejeződik, megtekintheti az előzményeket, hogy a futtatás, beleértve a lépéseket, amelyek futottak együtt az állapot, időtartam, bemenetek és kimenetek az egyes műveletek. Ez a részletes részlet betekintést nyújt a logikai alkalmazás működésébe, és abba, hogy hol kezdheti el a felmerülő problémák elhárítását.

Amikor megtekinti a logikai alkalmazás futtatási előzményeit, a Logic Apps hitelesíti a hozzáférést, majd hivatkozásokat biztosít az egyes futtatásokhoz szükséges kérelmek és válaszok bemeneteihez és kimeneteihez. A jelszavakat, titkos kulcsokat, kulcsokat vagy más bizalmas adatokat kezelő műveletek esetében azonban meg szeretné akadályozni, hogy mások megtekintsék és hozzáférjenek ezekhez az adatokhoz. Ha például a logikai alkalmazás lekéri az [Azure Key Vault](../key-vault/key-vault-overview.md) egy HTTP-művelet hitelesítése kor használható titkos kulcsot, el szeretné rejteni ezt a titkot a nézetből.

A logikai alkalmazás futtatási előzményeiben lévő bemenetekhez és kimenetekhez való hozzáférés szabályozásához a következő lehetőségek közül választhat:

* [Hozzáférés korlátozása IP-címtartomány szerint](#restrict-ip).

  Ezzel a beállítással biztonságossá teszi az előzmények futtatásához való hozzáférést egy adott IP-címtartományból érkező kérelmek alapján.

* [Az adatok elrejtése a futtatási előzményekelől az elbúvó](#obfuscate)használatával.

  Számos eseményindítók és műveletek, elrejtheti a bemenetek, kimenetek, vagy mindkettő egy logikai alkalmazás futtatási előzmények.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Hozzáférés korlátozása IP-címtartomány szerint

Korlátozhatja a hozzáférést a bemenetek és kimenetek a logikai alkalmazás futtatási előzmények, hogy csak a kérelmek adott IP-címtartományok megtekinthetik az adatokat. Ha például azt szeretné, hogy bárki hozzáférjen a bemenetekhez `0.0.0.0-0.0.0.0`és kimenetekhez, adjon meg egy IP-címtartományt, például . Csak rendszergazdai engedélyekkel rendelkező személy távolíthatja el ezt a korlátozást, amely lehetővé teszi a logikai alkalmazás adataihoz való "just-in-time" hozzáférést. Megadhatja az IP-tartományok korlátozásához vagy az Azure Portalon, vagy egy Azure Resource Manager sablon, amely a logikai alkalmazások üzembe helyezéséhez használt.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>IP-tartományok korlátozása az Azure Portalon

1. Az Azure Portalon nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások**csoportban válassza a **Munkafolyamat-beállítások lehetőséget.**

1. A **Hozzáférés-vezérlés konfigurációJa** > **Engedélyezett bejövő IP-címek csoportban**válassza a Speciális **IP-tartományok lehetőséget.**

1. A **tartalom IP-tartományai**csoportban adja meg azokat az IP-címtartományokat, amelyek bemenetekből és kimenetekből származó tartalomhoz férhetnek hozzá. 

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>IP-tartományok korlátozása az Azure Resource Manager-sablonban

Ha [erőforrás-kezelői sablonok használatával automatizálja a logikai alkalmazások központi telepítését,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)megadhatja az IP-tartományokat a `accessControl` szakasz használatával a `contents` logikai alkalmazás erőforrás-definíciójának szakaszával, például:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Adatok elrejtése a futtatási előzményekből az eldugulás használatával

Számos eseményindító és művelet rendelkezik beállításokkal a bemenetek, kimenetek vagy mindkettő elrejtésére egy logikai alkalmazás futtatási előzményeiből. Az alábbiakban néhány [szempontot kell áttekintenie,](#obfuscation-considerations) amikor ezeket a beállításokat használja az adatok védelméhez.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Bemenetek és kimenetek elrejtése a tervezőben

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

   ![Logikai alkalmazás megnyitása a Logic App Designer ben](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Az eseményindítón vagy műveleten, ahol el szeretné rejteni a bizalmas adatokat, jelölje ki a három pont (**...**) gombot, majd a **Beállítások lehetőséget.**

   ![Eseményindító- vagy műveletbeállítások megnyitása](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Kapcsolja be a **Biztonságos bemenetek,** **a Biztonságos kimenetek**vagy mindkettő t. Ha elkészült, válassza a **Kész** lehetőséget.

   ![A "Biztonságos bemenetek" vagy a "Biztonságos kimenetek" bekapcsolása](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A művelet vagy az eseményindító most egy lakat ikont jelenít meg a címsorban.

   ![A művelet vagy az eseményindító címsora a zárolás ikonját jeleníti meg](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   A korábbi műveletek védett kimeneteit képviselő tokenek zárolási ikonokat is tartalmaznak. Ha például kiválaszt egy ilyen kimenetet a dinamikus tartalomlistából, amelyet egy műveletben szeretne használni, az a token egy lakat ikont jelenít meg.

   ![Token kiválasztása a védett kimenethez](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. A logikai alkalmazás futtatása után megtekintheti az adott futtatás előzményeit.

   1. A logikai alkalmazás **áttekintő** ablaktábláján válassza ki a megtekinteni kívánt futtatást.

   1. A **Logikai alkalmazás futtatása** ablaktáblán bontsa ki az áttekinteni kívánt műveleteket.

      Ha úgy döntött, hogy eltakarja a bemeneteket és a kimeneteket is, ezek az értékek rejtettnek tűnnek.

      ![Rejtett bemenetek és kimenetek a futtatási előzményekben](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Bemenetek és kimenetek elrejtése kódnézetben

Az alapul szolgáló eseményindítóban vagy `runtimeConfiguration.secureData.properties` műveletdefinícióban adja hozzá vagy frissítse a tömböt a következő értékekkel:

* `"inputs"`: Biztosítja a bemeneteket a futtatási előzményekben.
* `"outputs"`: Biztosítja a kimeneteket a futtatási előzményekben.

Az alábbiakban néhány [szempontot kell áttekintenie,](#obfuscation-considerations) amikor ezeket a beállításokat használja az adatok védelméhez.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Szempontok a bemenetek és kimenetek elrejtésekén

* Ha elfedi a bemenetek vagy kimenetek egy eseményindító vagy művelet, logic apps nem küldi el a biztonságos adatokat az Azure Log Analytics. Emellett nem adhat [hozzá nyomon követett tulajdonságokat](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) az eseményindítóhoz vagy a figyeléshez.

* A [Logic Apps API a munkafolyamat-előzmények kezeléséhez](https://docs.microsoft.com/rest/api/logic/) nem ad vissza biztonságos kimeneteket.

* Ha el szeretné rejteni a kimeneteket egy olyan műveletből, amely eltakarja a bemeneteket vagy kifejezetten eltakarja a kimeneteket, manuálisan kapcsolja be a **biztonságos kimeneteket** a műveletben.

* Győződjön meg arról, hogy bekapcsolja a **biztonságos bemenetek** vagy **a biztonságos kimenetek** az alsóbb rétegbeli műveletek, ahol várhatóan a futtatási előzmények eltakarja az adatokat.

  **Biztonságos kimenetek beállítás**

  Ha manuálisan kapcsolja be a **biztonságos kimenetek** egy eseményindító vagy művelet, Logic Apps biztosítja ezeket a kimeneteket a futtatási előzményekben. Ha egy alsóbb rétegbeli művelet explicit módon használja ezeket a biztonságos kimeneteket bemenetként, a Logic Apps elrejti a művelet bemeneteit a futtatási előzményekben, de *nem engedélyezi* a művelet **biztonságos bemenetek** beállítását.

  ![Biztonságos kimenetek bemenetként és a legtöbb intézkedésre gyakorolt későbbi hatások](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  A Compose, Parse JSON és Response műveletek csak a **biztonságos bemenetek** beállítás. Ha be van kapcsolva, a beállítás elrejti a műveletek kimeneteit is. Ha ezek a műveletek kifejezetten a felső streamelt védett kimeneteket használják bemenetként, a Logic Apps elrejti a műveletek bemeneteit és kimeneteit, de *nem engedélyezi* ezeket a műveleteket a **Biztonságos bemenetek** beállítás. Ha egy alsóbb rétegbeli művelet explicit módon használja a rejtett kimeneteket a Compose, Parse JSON vagy Válasz műveletek bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemenetek vagy kimenetek.*

  ![Biztonságos kimenetek bemenetként, és a későbbi ekre gyakorolt hatás konkrét intézkedésekre](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Biztonságos bemenetek beállítás**

  Ha manuálisan kapcsolja be a **biztonságos bemeneteket** egy eseményindítóban vagy műveletben, a Logic Apps biztosítja ezeket a bemeneteket a futtatási előzményekben. Ha egy alsóbb rétegbeli művelet explicit módon használja a látható kimeneteket, hogy az eseményindító vagy művelet bemenetként, Logic Apps elrejti az alsóbb rétegbeli művelet bemenetek a futtatási előzményekben, de *nem engedélyezi* a **biztonságos bemenetek** ebben a műveletben, és nem rejti el a művelet kimenetek.

  ![Biztonságos bemenetek és a következő lépések hatása a legtöbb intézkedésre](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Ha a Compose, A JSON-parse és a Válasz műveletek kifejezetten használja a látható kimeneteket az eseményindító vagy a művelet, amely rendelkezik a biztonságos bemenetek, Logic Apps elrejti ezeket a műveletek bemenetek és kimenetek, de *nem engedélyezi* ezeket a **műveleteket a biztonságos bemenetek** beállítás. Ha egy alsóbb rétegbeli művelet explicit módon használja a rejtett kimeneteket a Compose, Parse JSON vagy Válasz műveletek bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemenetek vagy kimenetek.*

  ![Biztonságos bemenetek és a későbbi hatások az egyes intézkedésekre](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Hozzáférés a paraméterbemenetekhez

Ha különböző környezetekben telepíti a számítógépet, fontolja meg a munkafolyamat-definícióban szereplő, az adott környezettől függően változó értékek paraméterezését. Így elkerülheti a kódolt adatokat egy [Azure Resource Manager-sablon](../azure-resource-manager/templates/overview.md) használatával a logikai alkalmazás üzembe helyezéséhez, a bizalmas adatok védelméhez a biztonságos paraméterek meghatározásával, és az adatok külön bemenetként való átvitele a [sablon paraméterein](../azure-resource-manager/templates/template-parameters.md) keresztül egy [paraméterfájl](../azure-resource-manager/templates/parameter-files.md)használatával.

Ha például http-műveleteket hitelesít az [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)szolgáltatással, megadhatja és eltakarhatja azokat a paramétereket, amelyek elfogadják a hitelesítéshez használt ügyfélazonosítót és ügyféltitkos kulcsot. Ezek a paraméterek definiálásához a `parameters` logikai alkalmazásban használja a logic app munkafolyamat-definíciójának és az Erőforrás-kezelő sablon központi telepítésének szakaszát. Ha el szeretné rejteni azokat a paraméterértékeket, amelyeket nem szeretne megjeleníteni `securestring` a `secureobject` logikai alkalmazás szerkesztésekor vagy a futtatási előzmények megtekintésekor, határozza meg a paramétereket a vagy a típus használatával, és szükség szerint használja a kódolást. Az ilyen típusú paraméterek nem adják vissza az erőforrás-definícióval, és nem érhetők el az erőforrás központi telepítés után való megtekintésekor. Ha futásidőben szeretné elérni ezeket `@parameters('<parameter-name>')` a paraméterértékeket, használja a munkafolyamat-definíción belüli kifejezést. Ezt a kifejezést a program csak futásidőben értékeli ki, és a [Munkafolyamat-definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md)írja le.

> [!NOTE]
> Ha egy paramétert használ egy kérelem fejlécében vagy törzsében, akkor ez a paraméter látható lehet a logikai alkalmazás futtatási előzményeinek és a kimenő HTTP-kérelemnek a megtekintésekor. Győződjön meg arról, hogy a tartalom-hozzáférési szabályzatokat is ennek megfelelően állította be. Az eldugott adatok és kimenetek [elrejtéséhez](#obfuscate) is használhatja a bemeneteket és kimeneteket. Az engedélyezési fejlécek soha nem láthatók bemeneteken vagy kimeneteken keresztül. Tehát, ha egy titkot használnak ott, az a titok nem visszakereshető.

További információt a témakör alábbi szakaszaiban talál:

* [Biztonságos paraméterek a munkafolyamat-definíciókban](#secure-parameters-workflow)
* [Adatok elrejtése a futtatási előzményekből az eldugulás használatával](#obfuscate)

Ha [a logikai alkalmazások központi telepítését erőforrás-kezelő sablonok használatával automatizálja,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)megadhatja a biztonságos `securestring` `secureobject` [sablonparamétereket](../azure-resource-manager/templates/template-parameters.md), amelyeket a központi telepítéskor kiértékelnek a és a típusok használatával. Sablonparaméterek meghatározásához használja a sablon legfelső `parameters` szintű szakaszát, amely elkülönül és `parameters` eltér a munkafolyamat-definíció szakaszától. A sablonparaméterek értékeinek megadásához használjon külön [paraméterfájlt](../azure-resource-manager/templates/parameter-files.md).

Például ha titkos kulcsokat használ, definiálhat és használhat biztonságos sablonparamétereket, amelyek lekérik ezeket a titkokat az [Azure Key Vaultból](../key-vault/key-vault-overview.md) a központi telepítéssorán. Ezután hivatkozhat a key vault és a titkos a paraméterfájlban. További információt az alábbi témakörökben talál:

* [Bizalmas értékek áthárítása üzembe helyezéskor az Azure Key Vault használatával](../azure-resource-manager/templates/key-vault-parameter.md)
* [Biztonságos paraméterek az Azure Resource Manager-sablonokban](#secure-parameters-deployment-template) a témakör későbbi részében

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Biztonságos paraméterek a munkafolyamat-definíciókban

A logikai alkalmazás munkafolyamat-definíciójában a bizalmas adatok védelméhez használjon biztonságos paramétereket, hogy ezek az adatok ne legyenek láthatók a logikai alkalmazás mentése után. Tegyük fel például, hogy egy HTTP-művelethez egyszerű hitelesítés szükséges, amely felhasználónevet és jelszót használ. A munkafolyamat-definícióban `parameters` a szakasz `basicAuthPasswordParam` `basicAuthUsernameParam` a típus használatával `securestring` határozza meg a paramétereket és a paramétereket. A műveletdefiníció ezután hivatkozik `authentication` ezekre a paraméterekre a szakaszban.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Biztonságos paraméterek az Azure Resource Manager-sablonokban

Egy logikai alkalmazás [Erőforrás-kezelő sablonja](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) több `parameters` szakaszt is rendelkezik. A jelszavak, kulcsok, titkos kulcsok és egyéb bizalmas adatok védelme érdekében a sablon `securestring` szintjén `secureobject` és a munkafolyamat-definíció szintjén definiáljon biztonságos paramétereket a vagy a típus használatával. Ezután tárolhatja ezeket az értékeket az [Azure Key Vaultban,](../key-vault/key-vault-overview.md) és a [paraméterfájl](../azure-resource-manager/templates/parameter-files.md) segítségével hivatkozhat a key vault és a titkos kulcs. A sablon ezután lekéri ezeket az információkat a központi telepítés során. További információ: [Bizalmas értékek áthárítása üzembe helyezéskor az Azure Key Vault használatával.](../azure-resource-manager/templates/key-vault-parameter.md)

Az alábbi `parameters` szakaszokról további információkat talál:

* A sablon legfelső szintjén egy `parameters` szakasz határozza meg a sablon által a *telepítéskor*használt értékek paramétereit. Ezek az értékek például tartalmazhatnak kapcsolati karakterláncokat egy adott központi telepítési környezethez. Ezután ezeket az értékeket egy külön [paraméterfájlban](../azure-resource-manager/templates/parameter-files.md)tárolhatja, ami megkönnyíti az értékek módosítását.

* A logikai alkalmazás erőforrás-definícióján belül, de `parameters` a munkafolyamat-definíción kívül egy szakasz határozza meg a munkafolyamat-definíció paramétereinek értékeit. Ebben a szakaszban ezeket az értékeket sablonkifejezések használatával rendelheti hozzá, amelyek a sablon paramétereire hivatkoznak. Ezeket a kifejezéseket a rendszer kiértékeli a központi telepítéskor.

* A munkafolyamat-definíción `parameters` belül egy szakasz határozza meg a logikai alkalmazás által futásidőben használt paramétereket. Ezután hivatkozhat ezekre a paraméterekre a logikai alkalmazás munkafolyamatában a munkafolyamat-definíciós kifejezések használatával, amelyek et futásidőben értékeli ki a rendszer.

Ez a példasablon, amely több biztonságos `securestring` paraméterdefinícióval rendelkezik, amelyek a következő típust használják:

| Paraméter neve | Leírás |
|----------------|-------------|
| `TemplatePasswordParam` | Sablonparaméter, amely elfogadja a munkafolyamat-definíció paraméterének `basicAuthPasswordParam` átadott jelszót |
| `TemplateUsernameParam` | Olyan sablonparaméter, amely elfogadja a munkafolyamat-definíció paraméterének `basicAuthUserNameParam` átadott felhasználónevet |
| `basicAuthPasswordParam` | Munkafolyamat-definíciós paraméter, amely elfogadja az alapszintű hitelesítéshez szükséges jelszót egy HTTP-műveletben |
| `basicAuthUserNameParam` | Munkafolyamat-definíciós paraméter, amely elfogadja a felhasználónevet az alapszintű hitelesítéshez egy HTTP-műveletben |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Hozzáférés a logikai alkalmazásokból hívott szolgáltatásokhoz és rendszerekhez

Íme néhány módszer, amely segíthet a logikai alkalmazásból hívásokat vagy kéréseket fogadó végpontok biztonságossá tétele érdekében:

* Adjon hozzá hitelesítést a kimenő kérelmekhez.

  Amikor olyan HTTP-alapú eseményindítóval vagy művelettel dolgozik, amely kimenő hívásokat köt, például HTTP, HTTP + Swagger vagy Webhook, hozzáadhat hitelesítést a logikai alkalmazás által küldött kérelemhez. A következő hitelesítési típusokat használhatja például:

  * [Alapfokú hitelesítés](#basic-authentication)

  * [Ügyféltanúsítvány-hitelesítés](#client-certificate-authentication)

  * [Active Directory OAuth-hitelesítés](#azure-active-directory-oauth-authentication)

  * [Felügyelt identitás hitelesítése](#managed-identity-authentication)
  
  További információ: [Hitelesítés hozzáadása a kimenő hívásokhoz](#add-authentication-outbound) a témakör későbbi részében.

* Hozzáférés korlátozása a logikai alkalmazás IP-címeiből.

  A logikai alkalmazások végpontjainak minden hívása meghatározott, a logikai alkalmazások régióin alapuló kijelölt IP-címekről származik. Hozzáadhat olyan szűrést, amely csak az adott IP-címekről érkező kéréseket fogadja el. Ezek az IP-címek beolvassa: [Korlátok és konfiguráció az Azure Logic Apps.](logic-apps-limits-and-config.md#configuration)

* A helyszíni rendszerekkel létesített kapcsolatok biztonságának javítása.

  Az Azure Logic Apps integrációt biztosít ezekkel a szolgáltatásokkal, hogy biztonságosabb és megbízhatóbb helyszíni kommunikációt biztosítson.

  * Helyszíni adatátjáró

    Az Azure Logic Apps számos felügyelt összekötője megkönnyíti a helyszíni rendszerekkel, például a Fájlrendszer, az SQL, a SharePoint és a DB2 biztonságos kapcsolatával való biztonságos kapcsolatot. Az átjáró adatokat küld a helyszíni forrásokból a titkosított csatornákon keresztül az Azure Service Bus. Minden forgalom az átjáróügynök biztonságos kimenő forgalmaként származik. Ismerje [meg, hogyan működik a helyszíni adatátjáró.](logic-apps-gateway-install.md#gateway-cloud-service)

  * Csatlakozás az Azure API Management en keresztül

    [Az Azure API Management](../api-management/api-management-key-concepts.md) helyszíni csatlakozási lehetőségeket biztosít, például a helyek közötti virtuális magánhálózatot és az ExpressRoute-integrációt a biztonságos proxy és a helyszíni rendszerekkel való kommunikáció érdekében. A logikai alkalmazás munkafolyamataaiból a Logic App Designerben kiválaszthatja az API Management által elérhetővé tett API-t, amely gyors hozzáférést biztosít a helyszíni rendszerekhez.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Hitelesítés hozzáadása kimenő hívásokhoz

A HTTP- és HTTPS-végpontok különböző típusú hitelesítést támogatnak. Az eseményindító vagy művelet alapján, amely et használ a kimenő hívások vagy kérelmek, amelyek ezek a végpontok, a hitelesítési típusok különböző tartományai közül választhat. Győződjön meg arról, hogy a logikai alkalmazás által kezelt bizalmas adatok védelme érdekében használjon biztonságos paramétereket, és szükség szerint kódolja az adatokat. A paraméterek használatáról és védelméről az [Access to parameter inputs című témakörben](#secure-action-parameters)talál további információt.

> [!NOTE]
> A Logic App Designer, a **hitelesítési** tulajdonság lehet rejtve néhány eseményindítók és műveletek, ahol megadhatja a hitelesítési típus. Ha azt szeretné, hogy a tulajdonság ezekben az esetekben megjelenjen, nyissa meg az **Új paraméter hozzáadása** listát az eseményindítón vagy műveleten, és válassza a **Hitelesítés**lehetőséget. További információ: [Access with managed identity](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Hitelesítés típusa | Által támogatott |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API-kezelés, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Ügyféltanúsítvány](#client-certificate-authentication) | Azure API-kezelés, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API-kezelés, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Nyers](#raw-authentication) | Azure API-kezelés, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Felügyelt identitás](#managed-identity-authentication) | Azure API-kezelés, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Alapszintű hitelesítés

Ha az [Alap beállítás](../active-directory-b2c/secure-rest-api.md) elérhető, adja meg a következő tulajdonságértékeket:

| Ingatlan (tervező) | Ingatlan (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | Basic | A használandó hitelesítési típus |
| **Felhasználónév** | `username` | Igen | <*felhasználónév*>| A célszolgáltatás végpontjához való hozzáférés hitelesítéséhez szolgáló felhasználónév |
| **Jelszó** | `password` | Igen | <*Jelszó*> | A célszolgáltatás végpontjához való hozzáférés hitelesítéséhez szolgáló jelszó |
||||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager-sablonban a központi telepítés automatizálásához,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kifejezésekkel érheti el ezeket a paraméterértékeket futásidőben. Ez a példa HTTP-műveletdefiníció a hitelesítést `type` adja meg, `Basic` és a [parameters() függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterértékek lehívásához:

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

### <a name="client-certificate-authentication"></a>Ügyféltanúsítvány hitelesítése

Ha az [Ügyféltanúsítvány](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) beállítás elérhető, adja meg a következő tulajdonságértékeket:

| Ingatlan (tervező) | Ingatlan (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | **Ügyféltanúsítvány** <br>vagy <br>`ClientCertificate` | A TLS/SSL ügyféltanúsítványokhoz használandó hitelesítési típus. Bár az önaláírt tanúsítványok támogatottak, a TLS/SSL önaláírt tanúsítványai nem támogatottak. |
| **Pfx** | `pfx` | Igen | <*kódolt-pfx-fájl-tartalom*> | A személyes adatcsere (PFX) fájlból származó base64 kódolású tartalom <p><p>A PFX-fájl base64 kódolású formátumra való konvertálásához a PowerShell t az alábbi lépésekkel használhatja: <p>1. Mentse a tanúsítvány tartalmát egy változóba: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Konvertálja a tanúsítvány `ToBase64String()` tartalmát a funkció használatával, és mentse a tartalmat egy szöveges fájlba: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Jelszó** | `password`| Nem | <*jelszó-for-pfx-fájl*> | A PFX-fájl elérésének jelszava |
|||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager-sablonban a központi telepítés automatizálásához,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kifejezésekkel érheti el ezeket a paraméterértékeket futásidőben. Ez a példa HTTP-műveletdefiníció a hitelesítést `type` adja meg, `ClientCertificate` és a [parameters() függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterértékek lehívásához:

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

A szolgáltatások ügyféltanúsítvány-hitelesítéssel történő védelméről az alábbi témakörökben olvashat bővebben:

* [Az API-k biztonságának növelése az Azure API-kezelés ben az ügyféltanúsítvány-hitelesítés használatával](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [A háttérszolgáltatások biztonságának növelése az Azure API Management ügyféltanúsítvány-hitelesítésével](../api-management/api-management-howto-mutual-certificates.md)
* [A RESTfuL szolgáltatás biztonságának növelése ügyféltanúsítványok használatával](../active-directory-b2c/secure-rest-api.md)
* [Tanúsítványhitelesítő adatok az alkalmazás hitelesítéséhez](../active-directory/develop/active-directory-certificate-credentials.md)
* [TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth-hitelesítés

Ha az [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) beállítás elérhető, adja meg a következő tulajdonságértékeket:

| Ingatlan (tervező) | Ingatlan (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | **Active Directory OAuth** <br>vagy <br>`ActiveDirectoryOAuth` | A használandó hitelesítési típus. A Logic Apps jelenleg az [OAuth 2.0 protokollt](../active-directory/develop/v2-overview.md)követi. |
| **Authority** | `authority` | Nem | <*URL-hatóság-token-kibocsátó*> | A hitelesítési jogkivonatot szolgáltató hatóság URL-címe. Alapértelmezés szerint ez `https://login.windows.net`az érték . |
| **Bérlő** | `tenant` | Igen | <*bérlő-azonosító*> | Az Azure AD-bérlő bérlőazonosítója |
| **Célközönség** | `audience` | Igen | <*erőforrás-engedélyezés*> | Az engedélyezéshez használni kívánt erőforrás, például`https://management.core.windows.net/` |
| **Ügyfélazonosító** | `clientId` | Igen | <*ügyfél-Azonosító*> | Az engedélyezést kérő alkalmazás ügyfélazonosítója |
| **Hitelesítő adatok típusa** | `credentialType` | Igen | Tanúsítvány <br>vagy <br>Titkos | Az ügyfél által az engedélyezés kérelmezéséhez használt hitelesítő adatok típusa. Ez a tulajdonság és érték nem jelenik meg a logikai alkalmazás alapjául szolgáló definíciójában, hanem meghatározza a kijelölt hitelesítő adatok típusához megjelenő tulajdonságokat. |
| **Titkos** | `secret` | Igen, de csak a "Titkos" hitelesítő adatok típusához | <*ügyféltitok*> | Az engedély kérésének ügyféltitektikel |
| **Pfx** | `pfx` | Igen, de csak a "Tanúsítvány" hitelesítő adatok típusához | <*kódolt-pfx-fájl-tartalom*> | A személyes adatcsere (PFX) fájlból származó base64 kódolású tartalom |
| **Jelszó** | `password` | Igen, de csak a "Tanúsítvány" hitelesítő adatok típusához | <*jelszó-for-pfx-fájl*> | A PFX-fájl elérésének jelszava |
|||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager-sablonban a központi telepítés automatizálásához,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kifejezésekkel érheti el ezeket a paraméterértékeket futásidőben. Ez a példa `type` HTTP-műveletdefiníció `ActiveDirectoryOAuth`a hitelesítést `Secret`a , a hitelesítő adattípusként adja meg, és a [parameters() függvény t](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterértékek beírásához:

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

Ha a **Nyers** lehetőség elérhető, akkor ezt a hitelesítési típust akkor is használhatja, ha olyan [hitelesítési sémákat kell használnia,](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) amelyek nem követik az [OAuth 2.0 protokollt.](https://oauth.net/2/) Ezzel a típussal manuálisan hozza létre a kimenő kérelemmel küldött engedélyezési fejléc értéket, és megadhatja a fejléc értékét az eseményindítóban vagy műveletben.

Az [OAuth 1.0 protokollt](https://tools.ietf.org/html/rfc5849)követő HTTPS-kérelem mintafejléce például:

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

A nyers hitelesítést támogató eseményindítóban vagy műveletben adja meg a következő tulajdonságértékeket:

| Ingatlan (tervező) | Ingatlan (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Igen | Nyers | A használandó hitelesítési típus |
| **Érték** | `value` | Igen | <*engedélyezés-fejléc-érték*> | A hitelesítéshez használandó engedélyezési fejléc értéke |
||||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager-sablonban a központi telepítés automatizálásához,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kifejezésekkel érheti el ezeket a paraméterértékeket futásidőben. Ez a példa HTTP-műveletdefiníció `Raw`a hitelesítést `type` adja meg , és a [parameters() függvény t](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterértékek bekéseléséhez:

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

Ha a [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) beállítás elérhető, a logikai alkalmazás használhatja a rendszer által hozzárendelt identitást, vagy *egyetlen* manuálisan létrehozott felhasználó által hozzárendelt identitást az erőforrásokhoz való hozzáférés hitelesítéséhez más Azure Active Directory (Azure AD) bérlők bejelentkezés nélkül. Az Azure kezeli ezt az identitást az Ön számára, és segít a hitelesítő adatok biztonságossá tétele, mert nem kell megadnia vagy elforgatnia a titkos kulcsokat. További információ az [Azure AD-hitelesítéshez felügyelt identitásokat támogató Azure-szolgáltatásokról.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Mielőtt a logikai alkalmazás használhatna egy felügyelt identitást, kövesse az [Azure-erőforrásokhoz való hozzáférés hitelesítése az Azure-logikai alkalmazások felügyelt identitások használatával című lépéseit.](../logic-apps/create-managed-service-identity.md) Ezek a lépések lehetővé teszik a felügyelt identitást a logikai alkalmazásban, és beállíthatja, hogy az identitás a cél Azure-erőforráshoz való hozzáférését.

1. Mielőtt egy Azure-függvény használhatna felügyelt identitást, először [engedélyezze a hitelesítést az Azure-függvényekhez.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

1. Abban az eseményindítóban vagy műveletben, ahol a felügyelt identitást szeretné használni, adja meg a következő tulajdonságértékeket:

   | Ingatlan (tervező) | Ingatlan (JSON) | Kötelező | Érték | Leírás |
   |---------------------|-----------------|----------|-------|-------------|
   | **Hitelesítés** | `type` | Igen | **Felügyelt identitás** <br>vagy <br>`ManagedServiceIdentity` | A használandó hitelesítési típus |
   | **Felügyelt identitás** | `identity` | Igen | * **Rendszerhez rendelt felügyelt identitás** <br>vagy <br>`SystemAssigned` <p><p>* <*felhasználó által kijelölt-identitás-név*> | A használandó felügyelt identitás |
   | **Célközönség** | `audience` | Igen | <*cél-erőforrás-azonosító*> | Az elérni kívánt célerőforrás erőforrásazonosítója. <p>Például `https://storage.azure.com/` a hitelesítéshez szükséges hozzáférési jogkivonatokat az összes tárfiókra érvényessé teszi. Azonban megadhat egy gyökérszolgáltatás URL-címét `https://fabrikamstorageaccount.blob.core.windows.net` is, például egy adott tárfiókot. <p>**Megjegyzés:** Előfordulhat, hogy **a Célközönség** tulajdonság el van rejtve néhány eseményindítóban vagy műveletben. Ha láthatóvá szeretné tenni ezt a tulajdonságot, nyissa meg az **Új paraméter hozzáadása** listát az eseményindítóban vagy műveletben, és válassza a **Célközönség**lehetőséget. <p><p>**Fontos:** Győződjön meg arról, hogy a célerőforrás-azonosító *pontosan megegyezik* az Azure AD által várt értékkel, beleértve a szükséges követő perjeleket is. Így az `https://storage.azure.com/` összes Azure Blob Storage-fiók erőforrás-azonosítója egy követő perjelet igényel. Azonban az erőforrás-azonosító egy adott tárfiókhoz nem igényel követő perjelet. Ezek az erőforrás-azonosítók, lásd: [Azure-szolgáltatások, amelyek támogatják az Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   |||||

   Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager-sablonban a központi telepítés automatizálásához,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kifejezésekkel érheti el ezeket a paraméterértékeket futásidőben. Ez a példa HTTP-műveletdefiníció a hitelesítést `type` adja meg, `ManagedServiceIdentity` és a [parameters() függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterértékek lehívásához:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>További lépések

* [Az Azure Logic Apps üzembe helyezésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Logikai alkalmazások figyelése](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Logikai alkalmazáshibák és -problémák diagnosztizálása](../logic-apps/logic-apps-diagnosing-failures.md)  
* [A logikai alkalmazás üzemelő példányának automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
