---
title: Biztonságos hozzáférés a Azure Logic Apps |} Microsoft Docs
description: Adja hozzá a biztonsági védelmének eseményindítók, bemeneti és kimeneti, művelet paramétereinek és a munkafolyamatok az Azure Logic Apps szolgáltatás elérésére.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 2052e58dab7241836409fb013778f9702004021c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299909"
---
# <a name="secure-access-to-your-logic-apps"></a>Biztonságos hozzáférés a logic Apps alkalmazásait

Nincsenek elérhető segítségével biztosíthatja a Logic Apps alkalmazást sok eszköz.

* Logikai alkalmazás (HTTP kérelem eseményindító) való hozzáférés biztosítása
* Kezelése, szerkesztése vagy olvassa el a logikai alkalmazás hozzáférésének biztonságossá tétele
* A Futtatás bemenetekhez és kimenetekhez tartalmát való hozzáférés biztosítása
* Paraméterek vagy a műveletek a munkafolyamat belül biztonságossá tétele
* Kéréseket fogadni a munkafolyamat-szolgáltatásokhoz való hozzáférés biztonságossá tétele

## <a name="secure-access-to-trigger"></a>Biztonságos hozzáférés a indítás

Használata esetén a logikai alkalmazás, amely akkor következik be, a HTTP-kérelem ([kérelem](../connectors/connectors-native-reqres.md) vagy [Webhook](../connectors/connectors-native-webhook.md)), korlátozhatja a hozzáférést, hogy csak az arra jogosult ügyfelek is érvényesítést a logikai alkalmazást. Logikai alkalmazás az összes kérelem titkosítja és védi SSL.

### <a name="shared-access-signature"></a>Közös hozzáférésű Jogosultságkódot

Minden logikai alkalmazás kérés végpontja tartalmaz egy [közös hozzáférésű Jogosultságkód (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) az URL-cím részeként. Minden egyes URL-cím tartalmaz egy `sp`, `sv`, és `sig` lekérdezési paraméter. Engedélyek vannak megadva `sp`, és engedélyezett, HTTP-metódus meg `sv` létrehozandó használt verzió és `sig` való hozzáférés hitelesítéséhez használt. Az aláírás generálta az SHA256 algoritmust az URL-cím elérési út és a tulajdonságok a titkos kulccsal. A titkos kulcs soha nem közzétett és közzé, és a titkosított és a logikai alkalmazás tárolt. A Logic Apps alkalmazást csak engedélyezi a titkos kulccsal létrehozott érvényes aláírást tartalmazó eseményindítók.

#### <a name="regenerate-access-keys"></a>Tárelérési kulcsok újragenerálása

Új biztonsági kulcsot következő a REST API-t vagy az Azure portálon keresztül bármikor újragenerálás. Az aktuális URL-címet a régi kulccsal korábban létrehozott érvénytelenítve, és nem jogosult az érvényesítést a logikai alkalmazást.

1. Nyissa meg a logikai alkalmazást újragenerálja a kulcsot az Azure-portálon
1. Kattintson a **hívóbetűk** menüpont alatt **beállítások**
1. Válassza ki a kulcs újbóli létrehozása és a folyamat befejezéséhez

URL-címek lekérése után az új hozzáférési kulcs újragenerálása aláírt esetén.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>A lejárati dátum visszahívási URL-címek létrehozása

Ha az URL-címet oszt meg más felek, a URL-címeket adott kulcsokkal és a lejárati dátumok igény szerint is létrehozhat. Ezután zökkenőmentesen kulcsok visszaállítani, vagy győződjön meg arról, bizonyos timespan hozzáférés az alkalmazás érvényesítést korlátozódik. Adott URL esetén keresztül is megadhat a lejárati dátumot a [REST API-t a logic apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

A törzsben szereplő tartalmazza a tulajdonságot `NotAfter` karakterláncként JSON dátum, amely adja vissza egy visszahívási URL-címet, amely csak akkor érvényes, amíg a `NotAfter` dátuma és időpontja.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Elsődleges vagy másodlagos titkos kulccsal URL-címek létrehozása

Létrehozni, vagy a visszahívási kérelem-alapú eseményindítók URL-listában, mely kulcs használatával írják alá az URL-cím is megadható.  Létrehozhat egy URL-cím, egy adott kulcs használatával írja alá a [REST API-t a logic apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers) az alábbiak szerint:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

A törzsben szereplő tartalmazza a tulajdonságot `KeyType` , vagyis `Primary` vagy `Secondary`.  Ez visszaadja a megadott biztonságos kulcs által aláírt URL-címet.

### <a name="restrict-incoming-ip-addresses"></a>Bejövő IP-címek korlátozása

A közös hozzáférésű Jogosultságkód mellett Kezdésként korlátozhatja a logikai alkalmazás hívása csak az adott ügyfelektől.  Például ha Ön kezeli a végpont Azure API Management keresztül, korlátozhatja a logikai alkalmazás csak a kérés elfogadása, ha a kérelem érkezik, az API Management példány IP-címről.

Ez a beállítás konfigurálható a logic app beállításokat:

1. Az Azure-portálon nyissa meg a logikai alkalmazást szeretne hozzáadni az IP-címkorlátozások
1. Kattintson a **munkafolyamat-beállításokat** menüpont alatt **beállítások**
1. Adja meg az eseményindító fogadja el az IP-címtartományok listáját

Egy érvényes IP-címtartomány veszi a formátum `192.168.1.1/255`. Ha azt szeretné, hogy a logikai alkalmazás csak indul el egy beágyazott logikai alkalmazást, válassza a **csak más logic apps** lehetőséget. Ez a beállítás ír üres tömb az erőforrás értelmében csak a saját magát (szülő logic apps) szolgáltatás hívásait sikeresen érvényesítést.

> [!NOTE]
> Továbbra is futtatható a logikai alkalmazást az kérelem eseményindító a REST API-n keresztül / felügyeleti `/triggers/{triggerName}/run` IP függetlenül. Ebben a forgatókönyvben szükséges hitelesítés engedélyezésébe az Azure REST API, és az összes esemény jelenik meg az Azure-naplózást. Ennek megfelelően beállított hozzáférés-vezérlési házirendek.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Az erőforrás-definíció IP-címtartományok beállítása

Ha használ egy [központi telepítési sablont](logic-apps-create-deploy-template.md) a központi telepítések automatizálásához, az IP-címtartomány beállításait konfigurálhatja az erőforrás-sablont.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, az OAuth és az egyéb biztonsági

További hitelesítési protokollok fölött egy logikai alkalmazás hozzáadása [Azure API Management](https://azure.microsoft.com/services/api-management/) hatékony monitoring, a biztonsági, a házirend és a tetszőleges végpontot, és ezt teszi közzé az API-k, logikai alkalmazás dokumentációja nyújt. Az Azure API Management is elérhetővé teheti a logikai alkalmazást, hogy az Azure Active Directory, a tanúsítványt, az OAuth vagy a más biztonsági szabványok használ a saját vagy nyilvános végpontja. Amikor egy kérelem érkezik, a Azure API Management továbbítja a kérést a logikai alkalmazás (hajt végre semmilyen szükséges átalakítások vagy üzenetsoroktól korlátozások). Az a logikai alkalmazást a bejövő IP-címtartomány beállítások segítségével engedélyezése csak a logikai alkalmazást az API Management aktiválására.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Biztonságos hozzáférés kezeléséhez vagy a logic apps szerkesztése

Hozzáférés a logikai alkalmazás felügyeleti műveleteihez korlátozhatja, hogy csak bizonyos felhasználók vagy csoportok képesek az erőforrás-műveletek végrehajtásához. A Logic apps használata az Azure [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) a beállítást, és testre szabható eszközök.  Az előfizetés tagjai, valamint rendelje hozzá néhány beépített szerepkörök állnak rendelkezésre:

* **Logic App közreműködői** -megtekintése, szerkesztése és frissítse a logikai alkalmazás hozzáférést biztosít.  Nem lehet eltávolítani az erőforrás, vagy felügyeleti műveleteket.
* **Logic App operátor** – megtekintheti a logikai alkalmazást és futtatási előzményei, és engedélyezését vagy letiltását.  Nem szerkeszthetők, vagy a definíció frissítése.

Is [Azure erőforrás-zárolás](../azure-resource-manager/resource-group-lock-resources.md) módosítása vagy törlése a logic apps megelőzése érdekében. Ez a lehetőség akkor hasznos, ha a termelési erőforrások a módosítások és a törlések megakadályozása.

## <a name="secure-access-to-contents-of-the-run-history"></a>Biztonságos hozzáférés a tartalmát a futtatási előzményei

Hozzáférés az előző fut az adott IP-címtartományokhoz korlátozhatja bemeneti vagy kimeneti tartalmát.  

Egy munkafolyamat sorozaton belül összes adata titkosításra kerül, az átvitel során, és inaktív. Előzmények futtatásához a rendszer telefonhívást indít, amikor a szolgáltatás hitelesíti a kérelmet, és a kérelem-válasz bemenetekhez és kimenetekhez mutató hivatkozásokat tartalmaz. Ez a hivatkozás csak kérések megtekintése a kijelölt IP-címtartomány tartalmat ad vissza a tartalom védelme. Ez a funkció további hozzáférés-vezérléshez is használhatja. Akkor kell megadni egy IP-címet, például `0.0.0.0` , nem sikerült elérni a bemenetek/kimenetek. Csak a személy rendszergazdai jogosultságokkal tudta eltávolítani ezt a korlátozást, lehetőséget biztosít munkafolyamat tartalma "just-in-time" elérésére.

Ezt a beállítást az erőforrás-beállítások, az Azure portál belül kell megadni:

1. Az Azure-portálon nyissa meg a logikai alkalmazást szeretne hozzáadni az IP-címkorlátozások
2. Kattintson a **hozzáférés-vezérlési konfiguráció** menüpont alatt **beállítások**
3. A tartalmakhoz való hozzáférést az IP-címtartományok listájának megadása

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Az erőforrás-definíció IP-címtartományok beállítása

Ha használ egy [központi telepítési sablont](logic-apps-create-deploy-template.md) a központi telepítések automatizálásához, az IP-címtartomány beállításait konfigurálhatja az erőforrás-sablont.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Biztonságos paraméterek és a bemenetek egy munkafolyamaton belül

Előfordulhat, hogy szeretné a telepítési munkafolyamat definícióját egyes funkcióit parametrizálja környezetek között. Egyes paraméterek előfordulhat, hogy is biztonságos paraméterek, akkor nem jelenik meg a munkafolyamat, például egy ügyfél-azonosító és a titkos ügyfélkulcs szerkesztésekor [Azure Active Directory hitelesítési](../connectors/connectors-native-http.md#authentication) egy HTTP-művelet.

### <a name="using-parameters-and-secure-parameters"></a>Paraméterek és biztonságos paraméterek

Alkalmazása futásidőben egy erőforrás paraméter értékének eléréséhez a [munkafolyamatdefiníciós nyelve](http://aka.ms/logicappsdocs) biztosít egy `@parameters()` műveletet. Akkor is, [adja meg a paraméterek az erőforrás központi telepítési sablont a](../azure-resource-manager/resource-group-authoring-templates.md#parameters). De ha a paraméter típusa, adja meg `securestring`, a paraméter és a többi az erőforrás-definíció nem adható vissza, és nem sikerült az erőforrás-telepítést követően elérhető lesz.

> [!NOTE]
> Ha a paraméter a fejléc vagy a kérelem törzse paraméter is láthatják őket a futtatási előzményei, majd kimenő HTTP-kérelem. Ügyeljen arra, hogy a tartalom-hozzáférési szabályzatok beállítását ennek megfelelően.
> Engedélyezési fejléceket keresztül bemeneti vagy kimeneti soha nem láthatók el. Ezért ha a titkos kulcs van használatban van, a titkos kulcs nincs lekérhető.

#### <a name="resource-deployment-template-with-secrets"></a>Erőforrás központi telepítési sablont a titkos kulcsok

A következő példa bemutatja a központi telepítés egy biztonságos paramétere hivatkozó `secret` futásidőben. Egy külön paraméterek fájlban kell megadni a környezet értéke a `secret`, vagy használjon [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) beolvasni a titkos kulcsok telepítése idő.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "secretDeploymentParam": {
         "type": "securestring"
      }
   },
   "variables": {},
   "resources": [ {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
         "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
         "definition": {
            "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
            "actions": {
               "Call_External_API": {
                  "type": "Http",
                  "inputs": {
                     "headers": {
                        "Authorization": "@parameters('secret')"
                     },
                     "body": "This is the request"
                  },
                  "runAfter": {}
               }
            },
            "parameters": {
               "secret": {
                  "type": "SecureString"
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
            "secret": {
               "value": "[parameters('secretDeploymentParam')]"
            }
         }
      }
   } ],
   "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Biztonságos hozzáférés a munkafolyamat érkező kérelmek fogadására szolgáltatások

Számos módon biztonságossá a logic app hozzáférésre van szüksége a tetszőleges végpontot.

### <a name="using-authentication-on-outbound-requests"></a>A kimenő kérelmek-hitelesítéssel

Az egy HTTP-, HTTP + Swagger (nyílt API-t), vagy a Webhook művelet használatakor a küldési kérelem hitelesítési is hozzáadhat. Alapszintű hitelesítés, tanúsítvány vagy Azure Active Directory-hitelesítés tartalmazhatnak. Ez a hitelesítés konfigurálásával kapcsolatos részletek megtalálhatók [ebben a cikkben](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Logic app IP-címek való hozzáférés korlátozása

A logic Apps alkalmazásokból összes hívások régiónként eltérő IP-címek meghatározott készletének határozza meg. Hozzáadhat további szűrés csak az adott kijelölt IP-címekről érkező kérelmek fogadására. Egy adott IP-címek listáját lásd: [logic app korlátozásai és konfigurációja](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Helyszíni kapcsolatok

A Logic apps a biztonságos és megbízható több szolgáltatásokkal való integrációt a helyszíni kommunikációt biztosítanak.

#### <a name="on-premises-data-gateway"></a>Helyi adatátjáró

Sok felügyelt összekötők logic apps a helyszíni rendszerekre, például File System, a SQL, a SharePoint, a DB2 és egyéb biztonságos kapcsolatot biztosít. Az átjáró továbbítja a titkosított csatornákon keresztül az Azure Service Bus helyszíni forrásból származó adatokat. Az összes forgalom származik, az átjáró ügynök biztonságos kimenő forgalmát. További információ [az átjáró működése](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Az Azure API Management](https://azure.microsoft.com/services/api-management/) rendelkezik a helyszíni kapcsolati lehetőségek, többek között pont-pont VPN- és ExpressRoute integráció biztonságos proxy és a helyszíni rendszerekre történő kommunikációhoz. A Logic App Designer az API-k felfedni a Azure API Management egy munkafolyamaton belül a helyszíni rendszerekben gyors elérését biztosító gyorsan kiválaszthatja.

## <a name="next-steps"></a>További lépések
[Központi telepítési sablon létrehozása](logic-apps-create-deploy-template.md)  
[Kivételkezelés](logic-apps-exception-handling.md)  
[Logikai alkalmazások figyelése](logic-apps-monitor-your-logic-apps.md)  
[Logic app hibák és problémák diagnosztizálása](logic-apps-diagnosing-failures.md)  
