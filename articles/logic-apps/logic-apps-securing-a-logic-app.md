---
title: Biztonságos hozzáférés az Azure Logic Apps |} A Microsoft Docs
description: Biztonság hozzáadása az Azure Logic Apps, beleértve az eseményindítók, bemenetek és kimenetek, paraméterek és egyéb szolgáltatások
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 31b18e2dfbad1f572b5c9b0ae59f5e076db7cde2
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649288"
---
# <a name="secure-access-in-azure-logic-apps"></a>Biztonságos hozzáférés az Azure Logic Appsben

A logikai alkalmazásban, ahol hozzáférés gondoskodhat az alábbiakban az elemek:

* [Kérelem vagy Webhook eseményindítók](#secure-triggers)
* [Műveletek, például a kezelése, szerkesztése vagy megtekintés](#secure-operations) a logikai alkalmazás
* [Bemenetek és kimenetek](#secure-run-history) a logikai alkalmazás futtatási előzmények
* [Műveleti paraméterek és a bemenetek](#secure-action-parameters)
* [Szolgáltatások, amelyek a get kérelmeket](#secure-requests) a logikai alkalmazás

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Biztonságos hozzáférés kéréséhez eseményindítók

Ha a logikai alkalmazás használja a HTTP-kérelem-alapú trigger, például a [kérelem](../connectors/connectors-native-reqres.md) vagy [Webhook](../connectors/connectors-native-webhook.md) eseményindító, korlátozhatja a hozzáférést, csak az arra jogosult ügyfelek a logikai alkalmazás elindításához. Logikai alkalmazás által fogadott összes kérelem titkosítva és biztosított, a Secure Sockets Layer (SSL) protokollt. Az alábbiakban különböző módokon biztosíthatja a hozzáférést a trigger típusa:

* [Közös hozzáférésű jogosultságkódok létrehozása](#sas)
* [Bejövő IP-címek korlátozása](#restrict-incoming-ip-addresses)
* [Azure Active Directory, OAuth vagy más biztonsági hozzáadása](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok létrehozása

A logikai alkalmazás minden kérés végpont tartalmaz egy [közös hozzáférésű Jogosultságkód (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) a végponti URL-címben. Minden egyes URL-cím tartalmaz egy `sp`, `sv`, és `sig` lekérdezési paraméter:

* `sp` Adja meg az engedélyekkel, amelyek a HTTP-metódusok engedélyezett használatra való leképezéséhez.
* `sv` Adja meg az aláírás generálásához használt verzió.
* `sig` az eseményindító való hozzáférés hitelesítésére szolgál.

Az aláírás jön létre az SHA256 algoritmust használ a szereplő URL-cím és a Tulajdonságok titkos hozzáférési kulccsal. A titkos kulcs soha nem közzétett vagy közzétett, és megőrzi a titkosított és a logikai alkalmazás tárolt. A logikai alkalmazás csak a titkos kulccsal létrehozott érvényes aláírással tartalmazó eseményindítók engedélyezi. 

A következő további információ a közös hozzáférésű Jogosultságkód való hozzáférés biztonságossá tétele:

* [Elérési kulcsok újragenerálása](#access-keys)
* [Lejáró visszahívási URL-címek létrehozása](#expiring-urls)
* [Hozzon létre elsődleges vagy másodlagos kulcsot URL-címek](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Elérési kulcsok újragenerálása

Hívóbetű újbóli egy új biztonságos bármikor, használja az Azure REST API vagy az Azure Portalon. Az összes korábban létrehozott URL-címeket, a régi kulcs érvénytelenné válnak, és már nem használja a logikai alkalmazás indítására jogosult. Az URL-címek lekérése után újragenerálása jelentkezett, új hozzáférési kulcsára.

1. Az Azure Portalon nyissa meg a logikai alkalmazás, amely rendelkezik a újragenerálja a kulcsot.

1. A logikai alkalmazás menüjében alatt **beállítások**válassza **Tárelérési kulcsok**.

1. Válassza ki a kulcs újragenerálása, és a folyamat befejezéséhez.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Hozzon létre visszahívási URL-címek lejárati dátuma

Ha megoszt egy kérelem-alapú eseményindító végponti URL-cím más feleknek, visszahívási URL-címet, a konkrét kulcsokat és a lejárati dátumok szükség szerint hozhatja létre. Ezután problémamentesen kulcsokat állítja, vagy korlátozza a hozzáférést egy adott időtartam, a logikai alkalmazás indítására. Az URL-cím a lejárat dátumát is megadhat a [Logic Apps – REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), például:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

A szövegtörzsben, például a `NotAfter`a JSON használatával tulajdonság dátum karakterlánc. Ez a tulajdonság adja vissza egy visszahívási URL-címet, amely csak érvényes a `NotAfter` dátuma és időpontja.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>URL-címek létrehozása elsődleges vagy másodlagos titkos kulccsal

Ha hoz létre, vagy lista visszahívási URL-kérelem-alapú eseményindítók, az URL-CÍMÉT az aláíráshoz használt kulcs is megadhat. Létrehozhat egy URL-címet, amely által egy adott kulcs használatával legyen aláírva a [Logic Apps – REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), például:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

A szövegtörzsben, például a `KeyType` tulajdonságot, mert `Primary` vagy `Secondary`. Ez a tulajdonság egy URL-címet, a megadott biztonságos kulcs aláírva adja vissza.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Bejövő IP-címek korlátozása

Közös hozzáférésű Jogosultságkód, és előfordulhat, hogy szeretné korlátozni a logikai alkalmazás meghívhat adott ügyfelekre.  
Például ha Ön kezeli az Azure API Management a kérelem-végponthoz, korlátozhatja a logikai alkalmazás csak az API Management-példány IP-címről érkező kérelmek fogadására. 

#### <a name="set-ip-ranges---azure-portal"></a>IP-tartományok beállítása – Azure portal

Ez a korlátozás az Azure Portalon beállításához, nyissa meg a logikai alkalmazás beállításai: 

1. Az Azure Portalon nyissa meg a logikai alkalmazás a Logic App Designerben. 

1. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**.

1. A **hozzáférés-vezérlési konfiguráció** > 
**engedélyezett bejövő IP-címek**válassza **konkrét IP-címtartományok**.

1. A **triggerek IP-címtartományai**, adja meg az IP-címtartományok, amely az eseményindító. Egy érvényes IP-címtartományt használja, ezek a formátumok: *x.x.x.x/x* vagy *x.x.x.x-x.x.x.x* 

Ha azt szeretné, hogy a logikai alkalmazás tűznek csak egy beágyazott logikai alkalmazást, mint a **engedélyezett bejövő IP-címek** listáról válassza ki **csak más logikai alkalmazások**. Ezt a beállítást, a Logic Apps szolgáltatást (szülő a logic apps) csak hívásait is aktiválhatja a logikai alkalmazások beágyazott logikai ír a logikai alkalmazás-erőforrást, egy üres tömb.

> [!NOTE]
> IP-cím, függetlenül továbbra is futtathatja egy logikai alkalmazást, amely rendelkezik egy kérelem-alapú eseményindító használatával `/triggers/{triggerName}/run` az Azure REST API-n keresztül vagy az API Management szolgáltatáson keresztül. Azonban ez a forgatókönyv továbbra is szükséges az Azure REST API hitelesítésére, és az összes esemény jelenik meg az Azure-Auditnapló. Ellenőrizze, hogy ennek megfelelően beállított hozzáférés-vezérlési házirendeket.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>IP-tartományok beállítása – logikai alkalmazás központi telepítési sablont

Ha Ön automatizálása a logic app központi telepítések segítségével egy [Azure Resource Manager üzembe helyezési sablon](../logic-apps/logic-apps-create-deploy-template.md), beállíthatja az IP-címtartományok például, hogy a sablonban:

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth vagy más biztonsági hozzáadása

További engedélyezési protokollok hozzáadása a logikai alkalmazást, érdemes lehet [Azure API Management](https://azure.microsoft.com/services/api-management/). Ez a szolgáltatás kínál hatékony monitoring, biztonsági házirend és bármely végpont dokumentációját, és lehetővé teszi az API-ként a logikai alkalmazás elérhetővé. Az API Management tehetők közzé egy nyilvános vagy privát végpontot a logikai alkalmazást, amely ezután használhatja az Azure Active Directory, az OAuth, a tanúsítvány vagy a más biztonsági előírásoknak. Az API Management-kérést kap, ha a szolgáltatás a kérést küld a logikai alkalmazást, még így bármilyen szükséges átalakítás vagy követhető korlátozások. Ahhoz, hogy csak a logikai alkalmazás elindításához az API Management, használhatja a logikai alkalmazás bejövő IP-címtartomány beállításokat. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Biztonságos hozzáférés a logic app-műveletek

Ahhoz, hogy csak adott felhasználók vagy csoportok, a logikai alkalmazás a műveletek futtatásához, korlátozhatja a hozzáférést a feladatokat, köztük a kezelése, megjelenítése és szerkesztése. A Logic Apps támogatja [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md), amelyet testre vagy hozzárendelése beépített szerepkörök tagjai az előfizetésében, például:

* [A logikai alkalmazás Közreműködője](../role-based-access-control/built-in-roles.md#logic-app-contributor): Felhasználók megtekintése, szerkesztése és a logikai alkalmazás frissítése. 

* [Logikai alkalmazás operátora](../role-based-access-control/built-in-roles.md#logic-app-operator): Felhasználók a logikai alkalmazás és a futtatási előzmények megtekintése és engedélyezheti vagy letilthatja a logikai alkalmazást. Ez a szerepkör szerkesztéséhez vagy a logikai alkalmazás frissítése.

Megakadályozásához módosítása vagy törlése a logikai alkalmazás, használhatja a [Azure erőforrás-zárolás](../azure-resource-manager/resource-group-lock-resources.md). Ez a képesség segít mindebben másokat megakadályozzon módosítása vagy törlése az éles erőforrásait.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Biztonságos hozzáférés a logikai alkalmazás futtatási előzmények

Az előző logikaialkalmazás-futtatások átadása pedig a bemeneti vagy kimeneti tartalom védelme érdekében adott IP-címtartományok hozzáférést korlátozhatja. Ez a funkció további hozzáférés-vezérlést biztosít. Egy logikai alkalmazást futtató összes adatot titkosított átvitel során, míg az inaktív. Amikor egy logikai alkalmazás futtatási előzmények igényel, Logic Apps hitelesíti a kérelmet és a bemenetek mutató hivatkozásokat tartalmaz, és kiírja a kérelmek és válaszok a logikai alkalmazás munkafolyamat. Így egy adott IP-címről csak nagybetűkkel adja vissza a tartalom védheti meg ezeket a hivatkozásokat. Például akkor is meg IP-cím például `0.0.0.0-0.0.0.0` , nem férhetnek bemeneteit és kimeneteit. Csak rendszergazdai jogosultságokkal rendelkező személy eltávolíthatja ezt a korlátozást "just-in-time" hozzáférést, a logikai alkalmazás tartalmát a lehetőségét biztosító.

### <a name="set-ip-ranges---azure-portal"></a>IP-tartományok beállítása – Azure portal

Ez a korlátozás az Azure Portalon beállításához, nyissa meg a logikai alkalmazás beállításai:

1. Az Azure Portalon nyissa meg a logikai alkalmazás a Logic App Designerben. 

1. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**.

1. A **hozzáférés-vezérlési konfiguráció** > 
    **engedélyezett bejövő IP-címek**válassza **konkrét IP-címtartományok**.

1. A **tartalom IP-címtartományai**, adja meg az IP-címtartományok, amely a bemeneti és kimeneti keresztül elérhető tartalmat. 
   Egy érvényes IP-címtartományt használja, ezek a formátumok: *x.x.x.x/x* vagy *x.x.x.x-x.x.x.x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>IP-tartományok beállítása – logikai alkalmazás központi telepítési sablont

Ha Ön automatizálása a logic app központi telepítések segítségével a [Azure Resource Manager üzembe helyezési sablon](../logic-apps/logic-apps-create-deploy-template.md), beállíthatja az IP-címtartományok például, hogy a sablonban:

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Biztonságos műveleti paraméterek és a bemenetek

Különböző környezetek között helyezésekor érdemes paraméterezni a logikai alkalmazás munkafolyamat-definícióban meghatározott elemek. Ezzel a módszerrel megadhatja bemenet alapján a környezetek használata és a bizalmas információk védelme érdekében. Például, ha, hitelesítéséhez használt HTTP-műveleteket a [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), határozza meg, és a paraméterek, fogadja el az ügyfél-Azonosítót és a hitelesítéshez használt titkos ügyfélkulcs biztonságos. Ezeket a paramétereket a logikai alkalmazás definíciójának rendelkezik a saját `parameters` szakaszban.
Paraméterértékek eléréséhez futásidőben, használhatja a `@parameters('parameterName')` kifejezés, amely biztosítja a [munkafolyamat-definíciós nyelv](https://aka.ms/logicappsdocs). 

Paraméterek és értékek ne jelenjenek meg a logikai alkalmazásban vagy a Futtatás megtekintése előzmények szerkesztésekor védelme érdekében meghatározhat paramétereket a `securestring` írja be, és szükség szerint kódolást. Ez a típus rendelkező paraméterek nem adott vissza az erőforrás-definícióval, és üzembe helyezést követően az erőforrás megtekintésekor nem érhetők el.

> [!NOTE]
> Ha a kérelem fejlécében vagy törzsében egy paramétert használja, arra a paraméterre is láthatják a logikai alkalmazás futtatási előzmények és kimenő HTTP-kérelem elérésekor. Győződjön meg arról is meg a tartalom-hozzáférési szabályzatok ennek megfelelően.
> Engedélyezési fejléceket keresztül bemeneti vagy kimeneti soha nem láthatók el. Tehát ha van egy titkos kulcsot használja, a titkos kulcs nem lekérdezhető.

Paramétereket a logikai alkalmazás definícióiról védelmével kapcsolatos további információkért lásd: [biztonságos paramétereket a logikai alkalmazás definícióiról](#secure-parameters-workflow) később ezen a lapon.

Ha központi telepítéseknél, Ön automatizálása [Azure Resource Manager üzembe helyezési sablonjait](../azure-resource-manager/resource-group-authoring-templates.md#parameters), biztonságos paramétereket is használhatja ezeket a sablonokat az. Például paramétereket is használhat a KeyVault titkos első, a logikai alkalmazás létrehozásakor. A központi telepítési sablon definíciójában a saját `parameters` szakaszban, a logikai alkalmazás külön `parameters` szakaszban. Paramétereket a központi telepítési sablonok védelmével kapcsolatos további információkért lásd: [paramétereket a központi telepítési sablonok biztonságos](#secure-parameters-deployment-template) később ezen a lapon.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Biztonságos paramétereket a logikai alkalmazás definícióiról

A logikai alkalmazás munkafolyamat-definíció a bizalmas információk védelme érdekében használ biztonságos paraméterek, így ezek az információk nem látható, a logikai alkalmazás mentése után. Tegyük fel például, hogy használ `Basic` egy HTTP-művelet definíciója a hitelesítést. Ebben a példában egy `parameters` szakaszt, amely meghatározza a művelet meghatározása és a egy `authentication` szakaszt, amely elfogadja `username` és `password` paraméterértékeket. Ezek a paraméterek értékének megadására, fájlt is használhat egy külön paraméterek, például:

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
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
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

Ha titkos kulcsokat használ, beszerezheti ezeket a titkos kódok üzembe helyezéskor használatával [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Az Azure Resource Manager üzembe helyezési sablonjait biztonságos paraméterek

Ez a példa bemutatja egy Resource Manager központi telepítési sablont, amely egynél több modul paraméterrel használja a `securestring` típusa:

* `armTemplatePasswordParam`, amely a logikai alkalmazás definíciójában bevitel `logicAppWfParam` paraméter

* `logicAppWfParam`, amely alapszintű hitelesítést használ, a HTTP-művelet bevitel

Ebben a példában magában foglalja egy belső `parameters` rész, amely a logikai alkalmazás munkafolyamat-definíció és a egy külső `parameters` rész, amely a központi telepítési sablont tartozik. A környezet értékek paraméterek megadásához használhatja egy külön paramétereket tartalmazó fájlt. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
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
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
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
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
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
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Ha titkos kulcsokat használ, beszerezheti ezeket a titkos kódok üzembe helyezéskor használatával [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Kérések fogadása szolgáltatások elérésének védelme

Íme néhány módszer tehet a bármely végpont, ahol a logikai alkalmazás hozzá kell férnie és kéréseket küld.

### <a name="add-authentication-on-outbound-requests"></a>Hitelesítés hozzáadása a kimenő kérelmek

Ha egy HTTP, a HTTP + Swagger (Open API) vagy Webhook művelettel dolgozik, a logikai alkalmazás által küldött kérelem hitelesítés is hozzáadhat. Használhatja például az alapszintű hitelesítés, tanúsítvány alapú hitelesítést vagy az Azure Active Directory-hitelesítést. További információkért lásd: [eseményindítók és műveletek hitelesítése](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Logikai alkalmazás IP-címek való hozzáférés korlátozása

A logic apps összes hívásait régió alapján meghatározott megadott IP-címekről származnak. Szűrés csak ezen IP-címekről érkező kérelmeket fogadó adhat hozzá. Ezen IP-címek, lásd: [korlátozások és konfiguráció az Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Biztonságos helyszíni kapcsolatok

Az Azure Logic Apps integrációs ezekkel a szolgáltatásokkal, biztonságos és megbízható helyszíni kommunikációt biztosít.

#### <a name="on-premises-data-gateway"></a>Helyi adatátjáró

Az Azure Logic Apps számos felügyelt összekötő adja meg a biztonságos kapcsolatok a helyszíni rendszerekre, például a fájlrendszer, SQL, SharePoint, DB2 és mások. Az átjáró a helyszíni forrásokból, titkosított csatornákon keresztül az Azure Service Bus elküldi az adatokat. Az összes forgalom származik, az átjáró ügynök biztonságos kimenő forgalmát. Ismerje meg, [a helyszíni adatátjáró működése](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Az Azure API Management](https://azure.microsoft.com/services/api-management/) biztosít a helyszíni kapcsolati beállítások, például a helyek közötti virtuális magánhálózat és az ExpressRoute-integráció biztonságos proxy és a helyszíni rendszerekkel való kommunikációt. A Logic App Designerben válassza ki a helyszíni rendszerekkel való gyors hozzáférés biztosítása a logikai alkalmazás munkafolyamat, az API Management által közzétett API.

## <a name="next-steps"></a>További lépések

* [Üzembehelyezési sablon létrehozása](logic-apps-create-deploy-template.md)  
* [Kivételkezelés](logic-apps-exception-handling.md)  
* [Logikai alkalmazások figyelése](logic-apps-monitor-your-logic-apps.md)  
* [Logikai alkalmazások hibáinak és a problémák diagnosztizálása](logic-apps-diagnosing-failures.md)  
