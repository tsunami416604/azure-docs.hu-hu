---
title: Kiszolgáló nélküli API létrehozása az Azure Functions használatával | Microsoft Docs
description: Kiszolgáló nélküli API létrehozása az Azure Functions használatával
services: functions
author: mattchenderson
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 9a35c1205c0b564c8d0db1fbd0535d41bb9c84a0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989906"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Kiszolgáló nélküli API létrehozása az Azure Functions használatával

Az oktatóanyag bemutatja, hogyan hozhat létre hatékonyan skálázható API-kat az Azure Functions segítségével. Az Azure Functions számos beépített HTTP-eseményindítót és -kötést tartalmaz, így egyszerűen létrehozhat végpontokat különböző nyelveken (például Node.JS, C# stb.). Az oktatóanyag során egy HTTP-eseményindítót hozhat létre, hogy kezelje az API egyes műveleteit. Ezenkívül integrálhatja az API-t az Azure Functions-proxykkal, és API-utánzatokat hozhat létre, ezzel készítve elő az API később bővítését. Mindez a kiszolgáló nélküli Functions-környezetre épül, ezért nem kell aggódnia az erőforrások skálázása miatt – egyelőre koncentráljon API-logikára.

## <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Az eredményül kapott függvényt fogja használni az oktatóanyag hátralevő részében.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az Azure Portalt. Ehhez jelentkezzen be Azure-fiókjával a [https://portal.azure.com](https://portal.azure.com) oldalon.

## <a name="customize-your-http-function"></a>A HTTP-függvény testreszabása

Alapértelmezés szerint a HTTP-eseményindító által aktivált függvény minden HTTP-metódust elfogad. Az űrlap alapértelmezett URL-címe: `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Ha követte a rövid útmutatót, akkor a `<funcname>` értéke „HttpTriggerJS1” vagy ahhoz hasonló. Ebben a szakaszban módosítani fogja a függvényt, hogy csak az `/api/hello` útvonalra vonatkozó GET kérésekre válaszoljon. 

1. Az Azure Portalon keresse meg a függvényt. A bal oldali navigációs menüben válassza az **Integrálás** lehetőséget.

    ![HTTP-függvény testreszabása](./media/functions-create-serverless-api/customizing-http.png)

1. Használja a táblázatban megadott, a HTTP-eseményindítóra vonatkozó beállításokat.

    | Mező | Mintaérték | Leírás |
    |---|---|---|
    | Engedélyezett HTTP-metódusok | Kiválasztott metódusok | Meghatározza, hogy mely HTTP-metódusokkal hívható meg a függvény |
    | Kiválasztott HTTP-metódusok | GET | Csak bizonyos HTTP-metódusoknak engedélyezi a függvény meghívását |
    | Útvonalsablon | /hello | Meghatározza, hogy melyik útvonalat használja a függvény meghívására |
    | Engedélyszint | Névtelen | Opcionális: A függvényt API-kulcs nélkül is elérhetővé teszi |

    > [!NOTE] 
    > Figyelje meg, hogy az `/api` nem szerepel az útvonalsablon alapútvonal-előtagjában, mivel ezt egy globális beállítás kezeli.

1. Kattintson a **Save** (Mentés) gombra.

További információ a HTTP-függvények testreszabásáról: [Azure Functions HTTP-kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Az API tesztelése

Ezután ellenőrizze, hogy a függvény hogyan működik az új API felületén.
1. Kattintson a függvény nevére a bal oldali navigációs sávon, visszatérve ezzel a fejlesztési oldalra.
1. Kattintson a **Függvény URL-címének lekérése** elemre, és másolja ki az URL-címet. Figyelje meg, hogy az már az `/api/hello` útvonalat használja.
1. Nyissa meg az URL-címet egy új böngészőlapon vagy a választott REST-ügyfélben. A böngészők alapértelmezés szerint a GET kérést használják.
1. Adjon hozzá paramétereket az URL-címben található lekérdezési sztringhez, például: `/api/hello/?name=John`
1. Nyomja le az Enter billentyűt, hogy ellenőrizze, minden működik-e. A következő választ kell kapnia: „*Hello John*”
1. Megpróbálhatja meghívni a végpontot egy másik HTTP-metódussal, hogy megbizonyosodjon arról, hogy a függvényt így nem hajtja végre a szolgáltatás. Ehhez egy REST-ügyfélt kell használnia (például cURL, Postman vagy Fiddler).

## <a name="proxies-overview"></a>Proxyk áttekintése

A következő szakaszban az API felületének létrehozását egy proxyn keresztül fogja elvégezni. Az Azure Functions-proxyk lehetővé teszik a kérések továbbítását más erőforrásokhoz. Ugyanúgy definiálja majd a HTTP-végpontot, mint a HTTP-eseményindító esetében, viszont nem olyan kódot ír, amely végrehajtódik a végpont hívásakor, hanem megad egy URL-címet a távoli megvalósításhoz. Így több API-forrást foglalhat egyetlen API-felületbe, hogy az ügyfelek könnyebben használhassák. Ez különösen hasznos, ha az API-t mikroszolgáltatásként kívánja kiépíteni.

A proxyk bármilyen HTTP-erőforrásra mutathatnak, például:
- Azure Functions 
- API Apps alkalmazások az [Azure App Service-ben](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Docker-tárolók a [Linuxon futó App Service-ben](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Bármely más üzemeltetett API

További információ a proxykról: [Azure Functions-proxyk használata].

## <a name="create-your-first-proxy"></a>Első proxy létrehozása

Ebben a szakaszban létrehoz egy új proxyt, amely az API egészének előtereként fog szolgálni. 

### <a name="setting-up-the-frontend-environment"></a>Az előtérrendszer beállítása

Ismételje meg a [Függvényalkalmazás létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) szakasz lépéseit egy új függvényalkalmazás létrehozásához, amelyben létrehozhatja a proxyt. Az új alkalmazás URL-címe szolgál majd az API előtereként, a korábban szerkesztett függvényalkalmazás pedig a háttérrendszer lesz.

1. Keresse meg az új előtérbeli függvényalkalmazást a portálon.
1. Válassza a **Platformfunkciók**, majd az **Alkalmazásbeállítások** lehetőséget.
1. Görgessen le az **Alkalmazásbeállításokig**, ahol a kulcs-érték párok vannak tárolva, és hozzon létre egy új beállítást a „HELLO_HOST” kulccsal. Állítsa az értékét háttérbeli függvényalkalmazás gazdagépére, például `<YourBackendApp>.azurewebsites.net`. Ez azon URL-cím része, amelyet korábban kimásolt a HTTP-függvény tesztelésekor. Erre a beállításra később hivatkozni fog a konfigurációban.

    > [!NOTE] 
    > Azért érdemes alkalmazásbeállításokat használni a gazdagép konfigurálásához, hogy a proxynak ne legyen nem módosítható környezeti függősége. Az alkalmazásbeállítások használatakor lehetősége van áthelyezni a proxykonfigurációt más környezetekbe, és ekkor a környezetspecifikus alkalmazásbeállítások fognak vonatkozni rá.

1. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-a-proxy-on-the-frontend"></a>Proxy létrehozása az előtérben

1. Térjen vissza az előtérbeli függvényalkalmazáshoz a portálon.
1. A bal oldali navigációs sávon kattintson a „Proxyk” elem mellett látható plusz jelre (+).
    ![Proxy létrehozása](./media/functions-create-serverless-api/creating-proxy.png)
1. Használja a táblázatban megadott proxybeállításokat. 

    | Mező | Mintaérték | Leírás |
    |---|---|---|
    | Name (Név) | HelloProxy | Felhasználóbarát név, kizárólag kezelési célra |
    | Útvonalsablon | /api/hello | Meghatározza, hogy melyik útvonalat használja a proxy meghívására |
    | Háttér-URL | https://%HELLO_HOST%/api/hello | Meghatározza a végpontot, ahova továbbítja a kéréseket a proxyn keresztül |
    
1. Figyelje meg, hogy a proxyk nem adják hozzá az `/api` alapútvonal-előtagot, azt Önnek kell beírni az útvonalsablonba.
1. A `%HELLO_HOST%` szintaxis a korábban létrehozott alkalmazásbeállításra hivatkozik. A feloldott URL az eredeti függvényre fog mutatni.
1. Kattintson a **Create** (Létrehozás) gombra.
1. Ki is próbálhatja az új proxyt. Másolja ki a proxy URL-címét, és tesztelje le a böngészőben vagy a választott HTTP-ügyféllel.
    1. Névtelen funkció esetében ezt használja:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Engedélyezést igénylő függvény esetében ezt használja:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>API-utánzat létrehozása

Ezután egy proxyt használva hoz létre egy API-utánzatot a megoldása számára. Így folytatódhat az ügyfél fejlesztése a háttérrendszer teljes megvalósítása nélkül is. A fejlesztés egy későbbi pontján létrehozhat egy új függvényalkalmazást, amely támogatja ezt a logikát, és átirányítja ide a proxyt.

Az API-utánzat létrehozásához létrehozunk egy új proxyt, ezúttal az [App Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor) használatával. Először is keresse meg a függvényalkalmazást a portálon. Válassza a **Platformfunkciók** elemet, majd a **Fejlesztési eszközök** pontban keresse meg az **App Service Editort**. Erre kattintva megnyílik az App Service Editor egy új lapon.

Válassza ki a `proxies.json` elemet a bal oldali navigációs sávon. Ez a fájl tárolja az összes proxy konfigurációját. Ha a [funkciók üzembe helyezési metódusainak](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) egyikét használja, ezt a fájlt kell karban tartania a verziókövetéshez. A fájlról itt olvashat részletesen: [Proxyk speciális konfigurációja](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Ha követte az eddigi lépéseket, akkor a proxies.json fájlnak így kell kinéznie:

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

Most következik az API-utánzat hozzáadása. Cserélje a proxies.json fájlt a következőre:

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

Ez hozzáad egy új, „GetUserByName” nevű proxyt a backendUri tulajdonság nélkül. Egy másik erőforrás meghívása helyett módosítja a proxyk alapértelmezett válaszát, felülírva azt. A kérések és a válaszfelülírások egy háttér-URL-lel együtt is használhatók. Ez különösen akkor lehet hasznos, amikor egy régi rendszerben használ proxykat, ahol szükség lehet a fejlécek, a lekérdezési paraméterek stb. módosítására. További információk a kérésekről és a válaszfelülírásokról: [Kérések és válaszok módosítása a proxykban](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Az API-utánzat teszteléséhez hívja meg a `<YourProxyApp>.azurewebsites.net/api/users/{username}` végpontot a böngésző vagy a választott REST-ügyfél használatával. A _{username}_ értéket cserélje a felhasználónevet képviselő sztringre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan lehet kiépíteni és testreszabni egy API-t az Azure Functions szolgáltatásban. Ezenkívül elsajátította, hogyan lehet több API-t, köztük utánzatokat egyesíteni egyetlen API-felületen. Ezekkel a technikákkal bármilyen összetettségű API-t kiépíthet az Azure Functions kiszolgáló nélküli számítási modelljének használatával.

A következő referenciák hasznára lehetnek az API továbbfejlesztése során:

- [Azure Functions HTTP-kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Azure Functions-proxyk használata]
- [Azure Functions API dokumentálása (előzetes verzió)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Azure Functions-proxyk használata]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
