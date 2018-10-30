---
title: Biztonságos hozzáférés az Azure Logic Apps |} A Microsoft Docs
description: Azure Logic Apps eseményindítók, bemenetek és kimenetek, műveleti paraméterek és a munkafolyamatokban szolgáltatások elérésének védelme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 11/22/2016
ms.openlocfilehash: 0fe35b67a424caedcea2c71885d1757943ace9d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232596"
---
# <a name="secure-access-in-azure-logic-apps"></a>Biztonságos hozzáférés az Azure Logic Appsben

Itt módon, hogy különböző összetevőihez hozzáféréssel biztosíthatja a logikai alkalmazásban:

* Biztonságos hozzáférés a logikai alkalmazás munkafolyamatának indítására, a HTTP-kérés eseményindító.
* Biztonságos hozzáférés a kezelése, szerkesztési és olvasó egy logikai alkalmazást.
* Tegye biztonságossá a logikai alkalmazás futtatását a tartalmának bemeneteit és kimeneteit belül való hozzáférést.
* Biztonságos paraméterek vagy a logikai alkalmazás munkafolyamatának a műveletek bemenetei között.
* Biztonságos hozzáférés a szolgáltatások, amelyek a logikai alkalmazás munkafolyamatának kéréseket fogadni.

## <a name="secure-access-to-trigger"></a>Biztonságos hozzáférés a-trigger

Használata esetén egy logikai alkalmazást, amely akkor aktiválódik a HTTP-kérés ([kérelem](../connectors/connectors-native-reqres.md) vagy [Webhook](../connectors/connectors-native-webhook.md)), korlátozhatja a hozzáférést, hogy csak az arra jogosult ügyfelek is aktiválódik a logikai alkalmazást. Egy logikai alkalmazást az összes kérelem vannak titkosítva, és SSL-en keresztül biztosított.

### <a name="shared-access-signature"></a>Közös hozzáférésű Jogosultságkód

Minden logikai alkalmazás kérelem-végpont tartalmaz egy [közös hozzáférésű Jogosultságkód (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) az URL-cím részeként. Minden egyes URL-cím tartalmaz egy `sp`, `sv`, és `sig` lekérdezési paraméter. Engedélyek vannak megadva `sp`, és engedélyezve van, HTTP-metódusok meg `sv` előállításához, használt verzió és `sig` való hozzáférés hitelesítésére szolgál. Az aláírás jön létre az SHA256 algoritmust használ a szereplő URL-cím és a tulajdonságok a titkos kulccsal. A titkos kulcs soha nem közzétett és közzé, és megőrzi a titkosított és a logikai alkalmazás tárolt. A logikai alkalmazás csak engedélyezi eseményindítókat, amelyek tartalmazzák a titkos kulccsal létrehozott érvényes aláírással.

#### <a name="regenerate-access-keys"></a>Elérési kulcsok újragenerálása

Új biztonságos kulcs, a REST API-t vagy az Azure portálon keresztül bármikor újból létrehozhatja. Összes aktuális URL-címet a régi kulccsal korábban létrehozott érvénytelenítve, és már nem jogosult a logikai alkalmazás aktiválódik.

1. Az Azure Portalon nyissa meg a logikai alkalmazás újragenerálja a kulcsot
1. Kattintson a **Tárelérési kulcsok** menüpont alatt **beállításai**
1. Válassza ki a kulcs újragenerálása és a folyamat befejezéséhez

URL-címek kérheti le az újbóli létrehozása után az új hozzáférési kulccsal van bejelentkezve.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Visszahívási URL-címek lejárati dátummal létrehozása

Ha az URL-címet oszt meg más feleknek, URL-címek az egyedi kulcsok és a lejárati dátumok igény szerint hozhatja létre. Ezután zökkenőmentesen kulcsokat állítja, vagy győződjön meg arról, aktiválódik egy alkalmazást a hozzáférést bizonyos TimeSpan korlátozódik. Lejárati dátum megadása egy URL-cím között a [logic apps – REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

A szövegtörzsben, például a tulajdonság `NotAfter` JSON dátum karakterlánc, amely adja vissza egy visszahívási URL-címet, amely csak akkor érvényes, amíg a `NotAfter` dátuma és időpontja.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>URL-címek elsődleges vagy másodlagos titkos kulcs létrehozása

Amikor készítése, vagy a kérelem-alapú eseményindítók visszahívási URL-címek listája, melyik kulcsot kell bejelentkezhet az URL-cím is megadhatja.  Létrehozhat egy URL-címet, egy adott kulcs használatával írja alá a [logic apps – REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) módon:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

A szövegtörzsben, például a tulajdonság `KeyType` mert `Primary` vagy `Secondary`.  Ez visszaad egy URL-címet a biztonságos kulcs megadott írja alá.

### <a name="restrict-incoming-ip-addresses"></a>Bejövő IP-címek korlátozása

Mellett a közös hozzáférésű Jogosultságkód előfordulhat, hogy korlátozni szeretné csak bizonyos ügyfelek megismernie egy logikai alkalmazást.  Például ha Ön kezeli az Azure API Management szolgáltatáson keresztül a végponthoz, korlátozhatja a logikai alkalmazás csak a kérés elfogadásához, amikor a kérelem érkezik, az API Management példány IP-címről.

Ez a beállítás a logikai alkalmazás beállítások konfigurálhatók:

1. Az Azure Portalon nyissa meg a logikai alkalmazást szeretne hozzáadni az IP-címkorlátozások
1. Kattintson a **munkafolyamat-beállítások** menüpont alatt **beállításai**
1. Adja meg az IP-címtartományok, fogadja el az eseményindító listája

Érvényes IP-címtartománynak formátuma `192.168.1.1/32`. Ha azt szeretné, hogy a logikai alkalmazás csak akkor aktiválódjanak, mint egy beágyazott logikai alkalmazást, válassza ki a **csak más logikai alkalmazások** lehetőséget. Ez a beállítás ír egy üres tömb az erőforrás jelentése csak hívásait (szülő a logic apps) maga a szolgáltatás sikeresen aktiválódik.

> [!NOTE]
> Továbbra is futtathatja a logikai alkalmazás kérelem-eseményindítóval rendelkező a REST API-n keresztül / felügyeleti `/triggers/{triggerName}/run` IP függetlenül. Ebben a forgatókönyvben az Azure REST API-n hitelesítést igényel, és az összes esemény jelenik meg az Azure-Auditnapló. Ennek megfelelően beállított hozzáférés-vezérlési házirendek.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Az erőforrás-definíció beállítás IP-címtartományok

Ha használ egy [központi telepítési sablont](logic-apps-create-deploy-template.md) az üzembe helyezések automatizálását, az erőforrás-sablon konfigurálható az IP-tartomány beállításait.  

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

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth vagy más biztonsági hozzáadása

Egy logikai alkalmazást, felül további engedélyezési protokollok hozzáadása [Azure API Management](https://azure.microsoft.com/services/api-management/) gazdag monitorozási, biztonsági, a házirend és bármely végpont képessége lehetővé teszi közzé az API-ként logikai alkalmazás dokumentációját. Az Azure API Management tehetők közzé egy nyilvános vagy privát végpontot a logikai alkalmazást, amely az Azure Active Directory, tanúsítvány, OAuth vagy más biztonsági szabványok használatával. Amikor egy kérelem érkezik, a az Azure API Management továbbítja a kérést a logikai alkalmazás (végrehajtása bármilyen szükséges átalakítás vagy korlátozás átvitel alatt). A bejövő IP-címtartomány beállításokat használhatja a a logikai alkalmazás engedélyezése csak a logikai alkalmazás aktiválódik az API Management.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Biztonságos hozzáférés kezeléséhez vagy a logic apps szerkesztése

Hozzáférési műveletek a logikai alkalmazás korlátozhatja, hogy csak adott felhasználók vagy csoportok képesek arra, hogy műveleteket az erőforráson. A Logic apps használata az Azure [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md) funkciót, és ugyanazokat az eszközöket a testre szabható.  Az előfizetés tagjai is hozzárendelhet néhány beépített szerepkörök állnak rendelkezésre:

* **A logikai alkalmazás Közreműködője** -megtekintése, szerkesztése és frissíteni a logikai alkalmazás hozzáférést biztosít.  Nem lehet eltávolítani az erőforrást, vagy rendszergazdai műveletek végrehajtására.
* **Logikai alkalmazás operátora** – megtekintheti a logikai alkalmazás és a futtatási előzmények és engedélyezését vagy letiltását.  Nem szerkeszthetők, vagy a definíció frissítése.

Is [Azure erőforrás-zárolás](../azure-resource-manager/resource-group-lock-resources.md) módosítása vagy törlése a logic apps elkerülése érdekében. Ez a funkció akkor értékes éles erőforrásait a módosítások és törlések megelőzése érdekében.

## <a name="secure-access-to-contents-of-the-run-history"></a>Biztonságos hozzáférés a tartalmát, a futtatási előzmények

Hozzáférés meghatározott IP-címtartományokhoz a korábbi közvetítésekből származó bemeneti vagy kimeneti tartalmát korlátozhatja.  

Egy munkafolyamat-Futtatás belül minden adatot titkosít az átvitel során, míg az inaktív. A futtatási előzmények kezdeményezték, ha a szolgáltatás hitelesíti a kérelmet, és a kérések és válaszok bemeneteit és kimeneteit mutató hivatkozásokat tartalmaz. Ezt a hivatkozást védhetők, így csak a kijelölt IP-címtartományból tartalom megtekintése nagybetűkkel adja vissza a tartalmát. Ez a funkció további hozzáférés-vezérlés is használhat. Akkor is megadhatja az IP-címet, például `0.0.0.0` így senki nem tudott hozzáférni bemenetekben/kimenetekben. Csak valaki rendszergazdai jogosultságokkal rendelkezik sikerült eltávolítani a korlátozás "just-in-time" hozzáférést, a munkafolyamat tartalmát lehetőségét biztosító.

Ez a beállítás konfigurálható az erőforrás-beállítások az Azure Portal:

1. Az Azure Portalon nyissa meg a logikai alkalmazást szeretne hozzáadni az IP-címkorlátozások
2. Kattintson a **hozzáférés-vezérlési konfiguráció** menüpont alatt **beállításai**
3. Adja meg a tartalmakhoz való hozzáférést az IP-címtartományok listája

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Az erőforrás-definíció beállítás IP-címtartományok

Ha használ egy [központi telepítési sablont](logic-apps-create-deploy-template.md) az üzembe helyezések automatizálását, az erőforrás-sablon konfigurálható az IP-tartomány beállításait.  

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

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Biztonságos paraméterek és a egy munkafolyamaton belül bemenetek

Érdemes egy munkafolyamat-definíció üzembe helyezéshez bizonyos aspektusainak paraméterezni a környezetek között. Emellett néhány paraméter lehet biztonságos paraméterek nem kívánja jelennek meg, ha egy munkafolyamatot, például egy ügyfél-Azonosítót és a titkos Ügyfélkód szerkesztési [Azure Active Directory-hitelesítés](../connectors/connectors-native-http.md#authentication) egy HTTP-művelet.

### <a name="using-parameters-and-secure-parameters"></a>Paraméterek és biztonságos paraméterek

Egy erőforrás paraméter futásidőben, eléréséhez a [munkafolyamat-definíciós nyelv](https://aka.ms/logicappsdocs) biztosít egy `@parameters()` műveletet. Azt is megtehetjük, [paramétereket adja meg az erőforrás üzembe helyezési sablon](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Ha a paraméter típusa, adja meg, de `securestring`, a paraméter nem adható vissza a többi erőforrás-definícióban, és nem lesznek elérhetők üzembe helyezést követően az erőforrás megtekintésével.

> [!NOTE]
> Ha a paraméter a fejléc vagy a kérelem törzsében a paraméter lehet látható a futási előzményeket és kimenő HTTP-kérelem elérésével. Ellenőrizze, hogy ennek megfelelően a tartalom-hozzáférési házirendeket állíthat be.
> Engedélyezési fejléceket keresztül bemeneti vagy kimeneti soha nem láthatók el. Tehát ha van a titkos kulcsot használja, a titkos kulcsot, nem lekérdezhető.

#### <a name="resource-deployment-template-with-secrets"></a>Erőforrás központi telepítési sablont a titkos kulcsok

Az alábbi példa bemutatja egy biztonságos paraméterében hivatkozó központi telepítés `secret` futásidőben. Egy külön paramétereket tartalmazó fájlt, a környezet értékét megadhatja a `secret`, vagy használjon [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) beolvasni a titkos kódok üzembe helyezése.

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

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Kérések fogadása a munkafolyamat-szolgáltatásokhoz való hozzáférés biztonságossá tétele

Többféleképpen védelme érdekében bármely végpont a logikai alkalmazás való hozzáférésre van szüksége.

### <a name="using-authentication-on-outbound-requests"></a>A kimenő kérelmeket hitelesítéssel

Ha egy HTTP, a HTTP + Swagger (Open API) vagy Webhook művelettel dolgozik, a kérelmet küld a rendszer hitelesítési is hozzáadhat. Alapszintű hitelesítés, tanúsítvány alapú hitelesítést vagy az Azure Active Directory-hitelesítést lehetett közé tartozik. A hitelesítés konfigurálása a részletek megtalálhatók [ebben a cikkben](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Logikai alkalmazás IP-címek való hozzáférés korlátozása

A logic apps összes hívásait régiónként IP-címek meghatározott készletének származnak. Hozzáadhat további szűrés csak az ezen kijelölt IP-címekről érkező kérelmek fogadására. Egy adott IP-címek listáját lásd: [logikai alkalmazások korlátai és beállítása](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Helyszíni kapcsolatok

A Logic apps biztosít a biztonságos és megbízható több szolgáltatásokkal való integrációt a helyszíni kommunikációt biztosítanak.

#### <a name="on-premises-data-gateway"></a>Helyi adatátjáró

Számos, a felügyelt összekötők a logic apps adja meg a helyszíni rendszereket, beleértve a fájlrendszer, SQL, SharePoint, DB2 és további biztonságos kapcsolatot. Az átjáró a helyszíni forrásokból, titkosított csatornákon keresztül az Azure Service Bus továbbítókat. Az összes forgalom származik, az átjáró ügynök biztonságos kimenő forgalmát. Tudjon meg többet [az átjáró működése](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Az Azure API Management](https://azure.microsoft.com/services/api-management/) rendelkezik a helyszíni kapcsolódási lehetőségeket, ideértve a site-to-site VPN és ExpressRoute integráció biztonságos proxy és a helyszíni rendszerekkel való kommunikációt. A Logic App Designerben a helyszíni rendszerekkel való gyors hozzáférés biztosítása az Azure API Management egy munkafolyamaton belül elérhetővé tett API gyorsan kiválasztása.

## <a name="next-steps"></a>További lépések
[Üzembehelyezési sablon létrehozása](logic-apps-create-deploy-template.md)  
[Kivételkezelés](logic-apps-exception-handling.md)  
[Logikai alkalmazások figyelése](logic-apps-monitor-your-logic-apps.md)  
[Logikai alkalmazások hibáinak és a problémák diagnosztizálása](logic-apps-diagnosing-failures.md)  
