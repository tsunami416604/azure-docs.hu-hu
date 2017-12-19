---
title: "Erőforrás-kezelő REST API-k |} Microsoft Docs"
description: "A Resource Manager REST API-k hitelesítés és használati példák áttekintése"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="resource-manager-rest-apis"></a>Erőforrás-kezelői REST API-k
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [Portál](resource-group-portal.md) 
> * [REST API](resource-manager-rest-api.md)
> 
> 

Minden hívás az Azure Resource Manager mögött minden telepített sablon mögött minden konfigurált tárfiók mögött van egy vagy több hívásokat az Azure Resource Manager RESTful API. Ez a témakör ezen API-t, és hogyan hívása őket bármely SDK minden használata nélkül használják. Ez a módszer akkor hasznos, ha azt szeretné, hogy teljes körű hozzáférést engedélyezzenek a kérelmeket az Azure-ba, vagy ha az SDK for a választott nyelv nem érhető el, vagy nem támogatja a műveleteket kell.

Ez a cikk nem lép az Azure-ban van közzétéve, de ahelyett, hogy használja az egyes műveletek példa arra, hogyan csatlakozhat őket minden API-n keresztül. Az alapok elsajátítása után áttekintheti a [Azure Resource Manager REST API-referencia](https://docs.microsoft.com/rest/api/resources/) a rest API-k használatával kapcsolatos részletes információk.

## <a name="authentication"></a>Authentication
Hitelesítés az erőforrás-kezelő által Azure Active Directory (AD) kell kezelni. Ha csatlakozni szeretne API-k, először egy hitelesítési jogkivonatot, amely lehet átadni kérelmek fogadásához az Azure AD szolgáltatással való hitelesítésre. Vannak leíró, azt egy tiszta hívása közvetlenül a REST API-k, mivel feltételezzük, hogy nem kívánja elvégezni a hitelesítést meg kell adni egy felhasználónevet és jelszót. Azt is feltételezzük, hogy nem használ két többtényezős hitelesítési mechanizmus. Ezért létrehozhatunk úgynevezett az Azure AD-alkalmazások és a szolgáltatás egyszerű használt a bejelentkezéshez. De ne feledje, hogy az Azure AD támogatja a több hitelesítési eljárás, és ezek a További kérelmeknél API szükséges hitelesítési jogkivonat beolvasása használható.
Hajtsa végre a [létrehozása Azure AD-alkalmazást és egy egyszerű szolgáltatást](resource-group-create-service-principal-portal.md) részletes útmutatást.

### <a name="generating-an-access-token"></a>Egy hozzáférési jogkivonat létrehozása
Hitelesítés engedélyezésébe az Azure AD végezhető el az Azure AD létesítő login.microsoftonline.com helyen. Hitelesítést végezni, akkor rendelkeznie kell a következő információkat:

* Az Azure AD bérlő azonosítója (használatát a bejelentkezéshez, gyakran ugyanaz, mint a vállalat, de nem szükséges, hogy az Azure AD neve)
* (Az Azure AD-alkalmazás létrehozása lépés során végrehajtott) alkalmazás azonosítója
* Jelszó (az Azure AD-alkalmazás létrehozása során kiválasztott)

A következő HTTP-kérelmek végezze el az "Azure AD bérlő azonosítója", "Alkalmazásazonosító" és "Password" cserélje le a megfelelő értékeket.

**Általános HTTP-kérelem:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... (Ha a hitelesítés sikeres) lesz az eredmény a következő válasz hasonlít:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Az előző válaszban access_token rendelkezik lett csonkolva olvashatóság)

**Létrehozása a Bash használatával:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Létrehozása a PowerShell használatával:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

A válasz egy hozzáférési jogkivonatot, információ arról, hogy mennyi ideig, hogy a jogkivonat érvényes, és milyen erőforrás is használhatja, hogy a jogkivonat kapcsolatos információkat tartalmaz.
A kapott hozzáférési jogkivonat segítségével az előző HTTP-hívásban kell átadnia, az összes kérelem a Resource Manager API-val. A fejléc értékeként "Tulajdonosi YOUR_ACCESS_TOKEN" értékű "Engedélyezés" nevű adja át. Figyelje meg a "Tulajdonos" és a hozzáférési token közötti távolságot.

Ahogy a fenti HTTP eredményt látja, a lexikális elem érvénytelen egy adott időn időintervalluma, gyorsítótárba, és használja fel, hogy ugyanezt a tokent. Akkor is, ha az is lehet az Azure AD egyes API-hívások hitelesítése, nagyon hatékony lenne.

## <a name="calling-resource-manager-rest-apis"></a>Hívása Resource Manager REST API-k
Ez a témakör csak néhány API-kat használ a többi műveletek alapvető használati ismertetik. A műveletekkel kapcsolatos információkért lásd: [Azure Resource Manager REST API-k](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Minden előfizetés felsorolása
A legegyszerűbb elvégezhető műveletek egyike az elérhető előfizetésekkel, hogy van-e hozzáférési listát. A következő kérés láthatja, hogyan a hozzáférési jogkivonat érték az átadott fejléc:

(Cserélje YOUR_ACCESS_TOKEN a tényleges hozzáférési jogkivonat.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... és emiatt kap, hogy a szolgáltatás egyszerű hozzáférés előfizetések listája

(Előfizetés-azonosítók rendelkezik lettek rövidítve olvashatóság érdekében)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Egy adott előfizetés összes erőforráscsoportok felsorolása
A Resource Manager API-khoz elérhető összes erőforrás egy erőforráscsoportban beágyazott. Meglévő erőforráscsoport erőforrás-kezelő lekérheti az előfizetéshez a következő HTTP GET kérelem használatával. Figyelje meg, hogyan az előfizetés-azonosító érték az átadott az URL-címben megadott idő.

(YOUR_ACCESS_TOKEN és lecserélése ELŐFIZETÉS_AZONOSÍTÓJA a tényleges access token és előfizetés-azonosító)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

A kapott válasz attól függ, hogy van-e olyan meghatározott erőforráscsoportokat, és ha igen, hogy hány.

(Előfizetés-azonosítók rendelkezik lettek rövidítve olvashatóság érdekében)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az eddigi jelenleg már csak lett lekérdezése a Resource Manager API-k információt. A Microsoft bizonyos erőforrások létrehozza, és először a legegyszerűbb azok összes, egy erőforráscsoportot is. A következő HTTP-kérelem hoz létre egy erőforráscsoportot a régió/hely az Ön által választott, és egy címkét ad hozzá.

(Cserélje YOUR_ACCESS_TOKEN, ELŐFIZETÉS_AZONOSÍTÓJA, RESOURCE_GROUP_NAME a tényleges hozzáférési jogkivonat, előfizetés-azonosító és szeretne létrehozni az erőforráscsoport neve)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Ha sikeres, a következő válasz hasonló választ kap:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Sikeresen létrehozott egy erőforráscsoportot az Azure-ban. Gratulálunk!

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>A Resource Manager sablonnal erőforráscsoporthoz erőforrások telepítése
A Resource Manager telepítheti az erőforrásokat sablonok használatával. A sablon meghatározza a több erőforrás és függőségi viszonyaikat. Az ebben a szakaszban feltételezzük, hogy ismeri a Resource Manager-sablonok, és csak megmutatjuk, hogyan végezheti el az API-hívás központi telepítésének elindítása. Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

Központi telepítési sablon nem térnek el egymástól sokkal hogyan más API-k hívására-e. Egy fontos eleme a központi telepítési sablon elég hosszú időt vehet igénybe. Az API-hívással most adja vissza, és Önnek, fejlesztő lekérdezze a központi telepítés állapotának megállapítása az üzembe helyezés. További információkért lásd: [nyomon követheti a aszinkron Azure műveleteket](resource-manager-async-operations.md).

A jelen példában használjuk elérhető egy nyilvánosan elérhetővé sablon [GitHub](https://github.com/Azure/azure-quickstart-templates). A sablon használjuk az USA nyugati régiója régió Linux virtuális gép telepítését. Annak ellenére, hogy ebben a példában egy nyilvános tárházban, például a Githubon elérhető sablont használ, ehelyett átadhatók teljes sablonjával a kérelem részeként. Vegye figyelembe, hogy azt adja meg a paraméter a kérésben használt értékek a telepített sablon belül.

(Csere ELŐFIZETÉS_AZONOSÍTÓJA, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD és DNS_NAME_FOR_PUBLIC_IP a kérést a megfelelő értékeket)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

A hosszú JSON-választ a kérelemhez tartozó kimaradt a jelen dokumentáció olvashatóság javítása érdekében. A válasz, amelyet hozott létre sablonalapú üzembe helyezésével kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>Következő lépések

- Aszinkron REST műveleteinek kezelésére vonatkozó további tudnivalókért lásd: [nyomon követheti a aszinkron Azure műveleteket](resource-manager-async-operations.md).
