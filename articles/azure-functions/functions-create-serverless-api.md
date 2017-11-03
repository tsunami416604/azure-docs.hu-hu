---
title: "Az Azure Functions használatával egy kiszolgáló nélküli API létrehozása |} Microsoft Docs"
description: "Az Azure Functions használatával egy kiszolgáló nélküli API létrehozása"
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: e4fe86b80d8a786da15cdea37619e54e55102e3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Az Azure Functions használatával egy kiszolgáló nélküli API létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan Azure Functions lehetővé teszi a magas szinten méretezhető API-k létrehozásához. Az Azure Functions számos beépített HTTP eseményindítók és kötések, amely megkönnyíti a különböző nyelveken, beleértve a Node.JS, a C#, és több végpont szerzői gyűjteménye. Ebben az oktatóanyagban testre fogja szabni a konkrét műveletek az API kialakításában kezelni egy HTTP-eseményindítóval. Is előkészítheti a növekvő az API integrálása az Azure Functions proxyk és utánzatait API-k beállítása. Mindez érhető el, a funkciók kiszolgáló nélküli számítási környezet felett, nem kell foglalkoznia az erőforrások skálázás – a API logika csak összpontosíthat.

## <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Az eredményül kapott funkció ebben az oktatóanyagban a többi fog történni.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az Azure-portálon. Ehhez jelentkezzen be [https://portal.azure.com](https://portal.azure.com) fel fiókjával.

## <a name="customize-your-http-function"></a>A HTTP-funkció testreszabása

Alapértelmezés szerint a HTTP-eseményindítóval aktivált függvény HTTP-metódus elfogadására van konfigurálva. Emellett van alapértelmezett URL-cím a `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Ha a gyors üzembe helyezés, majd követi `<funcname>` valószínűleg valahogy "HttpTriggerJS1". Ebben a szakaszban módosítani fogja a függvény csak a GET kérelmek elleni megválaszolásához `/api/hello` útvonal helyett. 

1. Nyissa meg a függvény az Azure portálon. Válassza ki **integráció** a bal oldali navigációs.

    ![Egy HTTP-funkció testreszabása](./media/functions-create-serverless-api/customizing-http.png)

1. A HTTP-eseményindító beállításokat használják a táblázatban megadott.

    | Mező | Mintaérték | Leírás |
    |---|---|---|
    | Engedélyezett HTTP-metódusok | A kijelölt metódusok | Meghatározza, hogy mely HTTP-metódus is használható a függvény meghívása |
    | A kijelölt HTTP-metódusok | GET | Lehetővé teszi, hogy csak a kijelölt HTTP metódusok használandó, a függvény meghívása |
    | Útvonal-sablon | hello | Meghatározza, hogy milyen útvonalat használ a függvény meghívása |
    | Jogosultsági szint | Névtelen | Választható lehetőség: Elérhetővé válnak a függvény egy API-kulcs nélkül |

    > [!NOTE] 
    > Vegye figyelembe, hogy Ön nem foglal a `/api` kiinduló az útvonalsablonhoz elérési előtagot, mivel ez egy globális beállítás kezeli.

1. Kattintson a **Save** (Mentés) gombra.

A HTTP-funkciók testreszabása többet is megtudhat [Azure Functions HTTP és a webhook kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Az API-teszt

Ezt követően tesztelje meg szeretné tekinteni, az új API felület használata a függvénynek.
1. Lépjen vissza a fejlesztési lapon kattintson a bal oldali navigációs a függvény nevét.
1. Kattintson a **függvény URL-cím beszerzése** és másolja az URL-címet. Láthatja, hogy használja a `/api/hello` címre.
1. Másolja az URL-címet egy új böngészőlapon vagy a kívánt REST-ügyfél. Böngészők alapértelmezés szerint fogja használni a GET.
1. A funkció futtatásához, és győződjön meg arról, hogy működik. Adja meg a "név" paraméternek a gyors üzembe helyezés kód kielégítéséhez lekérdezési karakterláncként szeretne.
1. A végpont meghívása a megerősítéséhez, hogy a függvény nem hajtotta végre egy másik HTTP-metódussal is próbálkozhat. Ehhez szüksége lesz egy REST-ügyfél, például a cURL, a Postman vagy a Fiddler használata.

## <a name="proxies-overview"></a>Proxyk áttekintése

A következő szakaszban az API-proxyn keresztül fog surface. Az Azure Functions proxyk, amely lehetővé teszi a kérelmek továbbítása más erőforrások előzetes verziójú funkciók. Ugyanúgy, mint a HTTP-eseményindítóval, de kódot mikor hajthat végre ahelyett, hogy a végpont neve HTTP végpont meghatározása, megadja a távoli megvalósításának URL-CÍMÉT. Ez lehetővé teszi, hogy több API-forrás írja be az ügyfelek használhatnak könnyen egyetlen API felülete. Ez különösen fontos, ha az API-t, mikroszolgáltatások létrehozására.

A proxy, mint a HTTP-erőforrás mutathat:
- Azure Functions 
- Az API-alkalmazások [az Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- A docker-tároló [Linux App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Más üzemeltetett API-k

Proxyk kapcsolatos további információkért lásd: [használata az Azure Functions proxyk (előzetes verzió)].

## <a name="create-your-first-proxy"></a>Az első proxy létrehozása

Ebben a szakaszban egy új proxy, amely az általános API-nak időtúllépést hoz létre. 

### <a name="setting-up-the-frontend-environment"></a>Az előtér-környezet létrehozása

Ismételje meg a [függvény-alkalmazás létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) függvény a proxy hoz létre új alkalmazás létrehozása. Az új alkalmazás URL-címe erre a célra a frontend az API-hoz, és a korábban Szerkesztés függvény alkalmazás erre a célra egy háttér.

1. Nyissa meg az új előtér függvény alkalmazás a portálon.
1. Válassza ki **beállítások**. Majd átváltása **engedélyezése Azure Functions proxyk (előzetes verzió)** "A" számára.
1. Válassza ki **Platform beállítások** válassza **Alkalmazásbeállítások**.
1. Görgessen le a **Alkalmazásbeállítások** , és hozzon létre egy új beállítás "HELLO_HOST" kulcs. Állítsa be az értékét, mint a háttéralkalmazás függvény alkalmazáshoz, a gazdagép `<YourBackendApp>.azurewebsites.net`. Ez a korábban kimásolt a HTTP-függvény tesztelése során URL-cím része. Ez a beállítás később a konfigurációban kell hivatkozik.

    > [!NOTE] 
    > Alkalmazásbeállítások a gazdagép konfigurációja a proxy-sablonja környezet függőség megelőzése érdekében ajánlott. Alkalmazásbeállítások használata azt jelenti, hogy a proxy konfigurációs áthelyezheti különböző környezetek között, a környezet-specifikus alkalmazás beállítások lesznek alkalmazva.

1. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-a-proxy-on-the-frontend"></a>Az előtér egy proxy létrehozása

1. Lépjen vissza az előtér-függvény alkalmazás a portálon.
1. A bal oldali navigációs, kattintson a plusz jelre "+" "Proxyk (előzetes verzió)" mellett.
    ![A proxy létrehozása](./media/functions-create-serverless-api/creating-proxy.png)
1. A táblázatban megadott proxybeállításainak használata. 

    | Mező | Mintaérték | Leírás |
    |---|---|---|
    | Név | HelloProxy | Rövid név csak olyan felügyeleti |
    | Útvonal-sablon | / api/hello | Meghatározza, hogy milyen útvonalat használ a proxy meghívása |
    | Háttérkiszolgáló URL-címe | https://%HELLO_HOST%/API/hello | Megadja a végpontot, amelyhez a kérés küldése a proxyn keresztül kell lennie |
    
1. Vegye figyelembe, hogy proxyk nem biztosít a `/api` alap elérési útja előtag, és ez az útvonalsablonhoz szerepelnie kell.
1. A `%HELLO_HOST%` szintaxis használatával a korábban létrehozott Alkalmazásbeállítás hivatkozik. A feloldott URL-címet az eredeti funkciókat mutasson.
1. Kattintson a **Create** (Létrehozás) gombra.
1. Az új proxy kipróbálhatja a proxykiszolgáló URL-cím másolásával és a vizsgálja, hogy a böngészőben, vagy a kedvenc HTTP-ügyféllel.
    1. Egy névtelen függvény felhasználásra:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Egy függvény engedélyezési használatával:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Egy utánzatait API létrehozása

A következő szüksége lesz egy proxy utánzatait API a megoldás létrehozásához. Ez lehetővé teszi, hogy folyamatban van, az ügyféloldali fejlesztés anélkül, hogy a háttér teljes végrehajtását. Később a fejlesztési hozzon létre egy új függvény alkalmazást, amely támogatja a logikai és a proxy átirányítása sikerült.

Az utánzatait API létrehozásához létrehozunk egy új proxyt a használatával a [App Service-szerkesztő](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Először keresse meg a függvény app a portálon. Válassza ki **Platform funkciói** és található **App Service-szerkesztő**. A hivatkozásra kattintva az megnyílik az App Service-szerkesztő új ablakban.

Válassza ki `proxies.json` a bal oldali navigációs. Ez az a fájlt, amely az összes a proxyk konfigurációs tárolja. Ha valamelyikét használja a [működik a központi telepítési módszerekkel](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), a fájl megmaradjanak a verziókövetési rendszerrel. Ez a fájl kapcsolatos további információkért lásd: [proxyk speciális konfigurációs](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Ha végrehajtotta eddig, a proxies.json kell a következőhöz hasonló:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Ezután a utánzatait API fogja hozzáadni. Cserélje le a proxies.json fájl a következő:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Ezzel hozzáad egy új proxy, "GetUserByName", a backendUri tulajdonság nélkül. Helyett egy másik erőforrás, módosítja egy válasz felülbírálás használatával proxyk alapértelmezett válaszát. Kérelem és válasz felülbírálások használhatók a URL-t együtt. Ez akkor különösen hasznos, ha a proxy használatát az ügynökökön egy örökölt rendszerre, ahol előfordulhat, hogy módosítania kell a fejlécek, lekérdezési paraméterek stb. Kérelem és válasz felülbírálások kapcsolatos további információkért lásd: [kérelmeit és válaszait a proxyk módosítása](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Tesztelje a utánzatait API hívása a `/api/users/{username}` végpont egy böngésző vagy a kedvenc REST-ügyfél használatával. Ügyeljen arra, hogy a csere _{username}_ egy karakterláncértéket, amely egy felhasználónévvel rendelkező.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan állíthatja össze és testreszabása az Azure Functions egy API-t. Is megtanulta, hogyan kell az több API-k, mocks, beleértve a együtt, egyetlen egyesített API felület. Ezek a technológiák használatával ki bármely összetettségi API-k létrehozása minden, a kiszolgáló nélküli számítási modellt a futtatása közben az Azure Functions által biztosított.

Az API-további fejleszt, hasznos lehet a következő hivatkozásokat:

- [Az Azure Functions HTTP és a webhook kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [használata az Azure Functions proxyk (előzetes verzió)]
- [Az Azure Functions API-k (előzetes verzió) dokumentálása](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[használata az Azure Functions proxyk (előzetes verzió)]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
