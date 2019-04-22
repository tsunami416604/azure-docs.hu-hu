---
title: A Java és a VS Code használatával több függő szolgáltatás fut.
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
manager: yuvalm
ms.openlocfilehash: 9f371be384214447c9d98f6223dd830f7c240326
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357059"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Az Azure fejlesztési tárolóhelyek több szolgáltatásos fejlesztői

Ebben az oktatóanyagban megismerheti, hogyan szóközzel Azure fejlesztői, a fejlesztői, szóközök biztosító előnyökkel némelyike együtt több szolgáltatást alkalmazások fejlesztéséhez kell.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban egy második, `mywebapi` nevű szolgáltatást fog létrehozni, és a `webfrontend` használatával fogja hívni azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![Több tároló](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. Tudni fogja, ha hasonló üzenetet készen áll az alábbi jelenik meg a hibakeresési konzolt:

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. A végpont URL-címe valahogy így fog kinézni: `http://localhost:<portnumber>`. **Tipp: A VS Code állapotsor kattintható URL-cím jelenik meg.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli Dev Spaces-terünkben fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
1. Ha a `mywebapi` elkészült, nyissa meg a böngészőben a localhost címét.
1. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé.
1. Váltson a `webfrontend` VS Code-ablakára.
1. *Adja hozzá* a következő `import`-utasításokat a `package` utasítás alatt:

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Cserélje le* az üdvözlő metódushoz tartozó kódot:

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, hogy ez miként segíti a csapatot az együttműködésen alapuló fejlesztésben.

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot a `webapi` projekt `index()` metódusában.
1. A `webfrontend` projektben állítson be egy töréspontot a `try` kezdetű sorban, mielőtt a projekt GET-kérést küld a `mywebapi` felé.
1. A `webfrontend` projektben nyomja le az F5 billentyűt (vagy indítsa újra a hibakeresőt, ha már fut).
1. Hívja meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban.
1. A web app alkalmazásban a névjegy lapra a két szolgáltatás által összefűzött üzenetet jelenít meg: "Hello webfrontend és Hello a mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>A HTTP-üzenetek automatikus nyomkövetés
Talán észrevette, hogy bár *webfrontend* nem tartalmaz semmilyen speciális kódot nyomtassa ki a HTTP-hívás adatbeolvasási az *mywebapi*, láthatja a HTTP-nyomkövetéseket üzenetek a kimeneti ablakban:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --ytv-> webfrontend:8080:
   GET /greeting?_=1544503627515 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --ve4-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-ve4-- mywebapi:
   HTTP/1.1 200
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-ytv-- webfrontend:8080:
   HTTP/1.1 200
   Hello from webfrontend and
   Hello from mywebapi
```
Ez az egy "ingyenes" fejlesztői, szóközök rendszerállapot kap. Összetevők, amelyek nyomon követik a HTTP-kéréseket, azok halad át a rendszer, hogy bonyolult, több szolgáltatásos hívások nyomon követheti a fejlesztés során könnyebben beszúrása azt.

### <a name="well-done"></a>Remek!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a fejlesztői, szóközök csoportos fejlesztése](team-development-java.md)
