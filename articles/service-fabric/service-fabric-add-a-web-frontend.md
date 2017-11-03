---
title: "Egy előtér-webkiszolgáló az ASP.NET Core segítségével Azure Service Fabric-alkalmazás létrehozása |} Microsoft Docs"
description: "Teszi közzé a weben a Service Fabric-alkalmazás használatával, az ASP.NET Core projektet és a szolgáltatások közötti kommunikációs szolgáltatás távoli eljáráshívás keresztül."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>A szolgáltatás előtér-webkiszolgáló az ASP.NET Core segítségével alkalmazás létrehozása
Alapértelmezés szerint Azure Service Fabric-szolgáltatások nem biztosítanak egy nyilvános webes felületet. Teszi közzé a HTTP-ügyfelek számára az alkalmazás működését, akkor belépési pontként személyekről, és onnan az egyes szolgáltatások majd kommunikálni webes projekt létrehozása.

Az oktatóanyag azt átvételéhez ahol azt korábban befejezte a a [az első alkalmazás létrehozásának a Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) oktatóanyagot, és adja hozzá az ASP.NET Core webszolgáltatás előtt az állapot-nyilvántartó teljesítményszámláló szolgáltatást. Ha még nem tette meg, lépjen vissza, és, hogy az oktatóanyag első lépéseit.

## <a name="set-up-your-environment-for-aspnet-core"></a>Állítsa be a környezetet az ASP.NET Core

A Visual Studio 2017 együtt az oktatóanyag követéséhez van szüksége. Bármely edition fog tenni. 

 - [Telepítse a Visual Studio 2017](https://www.visualstudio.com/)

Az ASP.NET Core Service Fabric-alkalmazások fejlesztésével, rendelkeznie kell az alábbi munkaterhelések telepítve:
 - **Azure fejlesztési** (alatt *webes & felhő*)
 - **Az ASP.NET és a webes fejlesztési** (alatt *webes & felhő*)
 - **A .NET core platformfüggetlen fejlesztésekhez** (alatt *egyéb Toolsets*)

>[!Note] 
>A .NET Core eszközöket a Visual Studio 2015 már nem frissítés alatt álló, ha a Visual Studio 2015 még használja, meg kell azonban [.NET Core VS 2015 tooling eszköz Preview 2](https://www.microsoft.com/net/download/core) telepítve.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Az ASP.NET Core szolgáltatás hozzá az alkalmazáshoz
Az ASP.NET Core egy egyszerűsített, platformfüggetlen webes fejlesztési keretrendszer, amely a webes API-k és modern webes felhasználói felület létrehozása segítségével. 

Adjuk hozzá az ASP.NET Web API-projektet a meglévő alkalmazáshoz.

1. A Megoldáskezelőben kattintson a jobb gombbal **szolgáltatások** az alkalmazásban le, és válassza a **Hozzáadás > új Service Fabric-szolgáltatás**.
   
    ![Egy új szolgáltatás hozzáadása meglévő alkalmazáshoz][vs-add-new-service]
2. Az a **szolgáltatás létrehozása** lapon, válassza ki **ASP.NET Core** és adjon neki egy nevet.
   
    ![ASP.NET webszolgáltatás kiválasztása az új service párbeszédpanelen][vs-new-service-dialog]

3. A következő oldalon biztosít az ASP.NET Core projektsablonjai. Ne feledje, hogy ezek a azonos lehetőségek, hogy mutatunk be Ha az ASP.NET Core projektek kívül a Service Fabric-alkalmazás, a szolgáltatás regisztrálására a Service Fabric-futtatókörnyezet kód kis mennyiségű hozott létre. A jelen oktatóanyag esetében válassza ki a **Web API**. Azonban koncepciók is alkalmazhat a teljes webalkalmazás létrehozása.
   
    ![ASP.NET-projekt kiválasztása][vs-new-aspnet-project-dialog]
   
    A Web API-projekt létrehozása után az alkalmazás rendelkeznie kell két szolgáltatást. Továbbra is építenie az alkalmazást, mert a további szolgáltatások pontosan azonos módon is hozzáadhat. Minden egyes lehet függetlenül rendszerverzióval ellátott és frissített.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Ahhoz, hogy mi azt régebben már kötöttek egyfajta, most az új alkalmazás központi telepítése, és tekintse meg az alapértelmezett viselkedést, amely az ASP.NET Core webes API-sablon.

1. Nyomja le az F5 billentyűt az alkalmazás hibakeresése a Visual Studio.
2. Központi telepítés befejeződése után a Visual Studio elindítja egy böngészőt, és az ASP.NET Web API-szolgáltatás a legfelső szintű. Az ASP.NET Core Web API nem biztosít alapértelmezett viselkedése a gyökér, 404 hibaüzenetet a böngészőben megjelenik.
3. Adja hozzá `/api/values` a böngészőben a helyre. Ez elindítja a `Get` a webes API-sablonban ValuesController metódust. A sablon – két karakterláncokat tartalmazó JSON-tömb által biztosított alapértelmezett válasz adja vissza:
   
    ![Az ASP.NET Core Web API sablonból visszaadott alapértelmezett értékek][browser-aspnet-template-values]
   
    Az oktatóanyag végén ezen az oldalon jelennek meg a legutóbbi számláló értéke az alapértelmezett karakterlánc helyett az állapotalapú szolgáltatásból.

## <a name="connect-the-services"></a>Csatlakozás a szolgáltatások
A Service Fabric hogyan kommunikáljanak megbízható szolgáltatások teljes rugalmasságot biztosít. Egyetlen alkalmazásban lehetséges, hogy szolgáltatásokat, amelyek a TCP, más szolgáltatásokkal, hogy elérhető-e egy HTTP REST API-n keresztül, és továbbra is más szolgáltatások, amelyek elérhetők a webes szoftvercsatornák keresztül érhető el. A rendelkezésre álló lehetőségeket, és a mellékhatásokkal jár a háttérben, lásd: [szolgáltatások folytatott kommunikáció](service-fabric-connect-and-communicate-with-services.md). Ebben az oktatóanyagban használjuk [Service Fabric szolgáltatás távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md), az SDK-ban megadott.

A szolgáltatás távoli eljáráshívási megközelítéssel (tapasztalatait távoli eljáráshívások vagy RPC-k) adja meg a szolgáltatás a nyilvános szerződés nevében járhasson el illesztőfelület. Ezt követően használja a kapcsolat a proxy osztály a szolgáltatással az létrehozásához.

### <a name="create-the-remoting-interface"></a>A távelérési kapcsolat létrehozása
Először hozzon létre a kapcsolat működjön, és a szerződés az állapotalapú szolgáltatásból és egyéb szolgáltatások között, ebben az esetben az ASP.NET Core webes projekt. Ez az interfész által hívásokat RPC, így a saját Class Library projektben létrehozunk használó összes szolgáltatások kell osztani.

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza a **Hozzáadás** > **új projekt**.

2. Válassza ki a **Visual C#** bejegyzést a bal oldali navigációs panelen, és válassza ki a **Class Library** sablont. Győződjön meg arról, hogy a .NET-keretrendszer verziója van-e állítva **4.5.2-es**.
   
    ![Az állapotalapú szolgáltatás felület projekt létrehozása][vs-add-class-library-project]

3. Telepítse a **Microsoft.ServiceFabric.Services.Remoting** NuGet-csomagot. Keresse meg **Microsoft.ServiceFabric.Services.Remoting** a nuget csomag manager, és telepítse az összes projektet, melyekben a megoldásban, amelyek a távelérés szolgáltatás többek között:
   - A Class Library projekt, amely tartalmazza a szolgáltatási felület
   - Az állapotalapú szolgáltatási projektet
   - Az ASP.NET Core webes projekt
   
    ![A szolgáltatások a NuGet-csomag hozzáadása][vs-services-nuget-package]

4. A osztály könyvtárban hozzon létre egy felület egy módszert, `GetCountAsync`, és megnöveli a felületének a `Microsoft.ServiceFabric.Services.Remoting.IService`. A távoli eljáráshívás felületen Ez az interfész azt jelzi, hogy ez a szolgáltatás távoli eljáráshívási felületet kell származnia.
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>A felület megvalósítása az állapot-nyilvántartó szolgáltatásban
Most, hogy definiáltuk a felületet, igazolnia kell megvalósítani, az állapotalapú szolgáltatásból.

1. Az állapot-nyilvántartó szolgáltatásban a, amely tartalmazza a felület hordozhatóosztálytár-projektjének mutató hivatkozás hozzáadása.
   
    ![Az állapot-nyilvántartó szolgáltatásban a hordozhatóosztálytár-projektjének mutató hivatkozás hozzáadása][vs-add-class-library-reference]
2. Keresse meg a osztályból származó `StatefulService`, például a `MyStatefulService`, és végrehajtásához kiterjesztése a `ICounter` felületet.
   
    ```c#
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Most valósítja meg az egyetlen definiált a `ICounter` felületet, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Az állapotalapú szolgáltatás használata a távoli eljáráshívási szolgáltatás figyelő indításához
Az a `ICounter` megvalósítva, a végső lépés objektumfelület. a szolgáltatás távoli eljáráshívási kommunikációs csatorna megnyitását. Állapotalapú szolgáltatások esetén a Service Fabric nevű felülbírálható módszert kínál `CreateServiceReplicaListeners`. Ezzel a módszerrel megadhatja egy vagy több kommunikációs figyelőket, a kommunikációt, amelyre később engedélyezni kívánja a szolgáltatás típusa alapján.

> [!NOTE]
> Az állapotmentes szolgáltatások kommunikációs csatorna megnyitásakor egyenértékű módszer neve `CreateServiceInstanceListeners`.

Ebben az esetben azt lecserélheti a meglévő `CreateServiceReplicaListeners` metódust, és adjon meg egy példánya `ServiceRemotingListener`, ami, amely létrehoz egy RPC-végpont metódus akkor hívható meg az ügyfelek a `ServiceProxy`.  

A `CreateServiceRemotingListener` bővítmény metódust a `IService` felület lehetővé teszi, hogy könnyen létrehozhat egy `ServiceRemotingListener` alapértelmezett beállításokkal. A bővítmény módszer használatához, ellenőrizze, hogy a `Microsoft.ServiceFabric.Services.Remoting.Runtime` importált névtér. 

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>A szolgáltatás együttműködhet az ServiceProxy osztály használatával
Az állapotalapú szolgáltatás más szolgáltatások forgalom fogadására RPC-n keresztül készen áll. Így továbbra is kommunikálni az ASP.NET-webszolgáltatás a kód hozzáadásával.

1. Az ASP.NET projektben adja hozzá egy hivatkozást a osztály, amely tartalmazza a `ICounter` felületet.

2. Korábban hozzáadott a **Microsoft.ServiceFabric.Services.Remoting** NuGet-csomagot kell az ASP.NET-projekt. Ez a csomag biztosítja a `ServiceProxy` osztályt, amely RPC végrehajtásához használhatja az állapotalapú szolgáltatásból hívások. Győződjön meg arról, a NuGet csomag telepítve van az ASP.NET Core web service projektben.

4. Az a **tartományvezérlők** mappa, nyissa meg a `ValuesController` osztály. Vegye figyelembe, hogy a `Get` metódus jelenleg csak a "érték1" és "érték2" – amely megfelel a Mi a böngészőben korábban látott kódolt karakterlánc tömbje ad vissza. Ez a megvalósítás cserélje le a következő kódot:
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    Az első kódsort, a kulcs egy. Az állapotalapú szolgáltatásból a ICounter proxy létrehozásához meg kell adnia kétféle információt: a Partícióazonosító és a szolgáltatás nevét.
   
    Használhatja a particionálás méretezési állapotalapú szolgáltatások által a különböző gyűjtők állapotukra összeállításának, egy definiálhat, például a felhasználói azonosító vagy irányítószámot kulcs alapján. Trivial alkalmazásban. az állapotalapú szolgáltatásból csak rendelkezik egy partíciót, így a kulcs nem számít. Bármely Ön által biztosított kulcs partícióra irányítja. Szolgáltatások partícionálásra vonatkozó további tudnivalókért lásd: [particionálása Service Fabric Reliable Services](service-fabric-concepts-partitioning.md).
   
    A szolgáltatásnév megadása egy URI-címe, az űrlap fabric: /&lt;$application_name&gt;/&lt;service_name&gt;.
   
    Két adatot, a Service Fabric egyedileg azonosíthatja a gépet, a kérelmek kell küldeni. A `ServiceProxy` osztály zökkenőmentesen is kezeli az esetben a számítógép, amelyen az állapotalapú szolgáltatási partíció nem sikerül, és egy másik gépen kell előléptetni a sor. Ezt az absztrakciót, lehetővé teszi az ügyfél programozás jelentősen egyszerűbb más szolgáltatásokkal foglalkozik.
   
    A proxy van, ha azt egyszerűen meghívása a `GetCountAsync` metódus és az eredményt adja vissza.

5. Nyomja le az F5 újra a módosított alkalmazás futtatásához. Mivel előtt, a Visual Studio automatikusan elindítja a böngésző számára a webes projekt gyökerében. A "API-t és-értékek" elérési út, és az aktuális számlálóérték vissza kell megjelennie.
   
    ![Az állapot-nyilvántartó számlálóérték jelenik meg a böngészőben][browser-aspnet-counter-value]
   
    Frissítse a böngészőablakot, hogy rendszeresen tekintse meg a számláló értéke frissítése.

## <a name="kestrel-and-weblistener"></a>Vércse és WebListener

Az alapértelmezett ASP.NET Core webkiszolgáló, vércse, úgynevezett [jelenleg nem támogatott a közvetlen internet-forgalom kezelésére](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel). Ennek eredményeképpen a Service Fabric ASP.NET Core állapotmentes szolgáltatások sablonját használja [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) alapértelmezés szerint. 

További információt a vércse és WebListener a Service Fabric-szolgáltatások, tekintse meg [ASP.NET Core a Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="connecting-to-a-reliable-actor-service"></a>Egy megbízható szereplő csatlakozik
Ez az oktatóanyag összpontosított hozzáadása egy előtér-webkiszolgáló, amely egy állapotalapú szolgáltatással továbbítani. Egy nagyon hasonló modell felvegye a gyakrabban is végrehajthatja. Visual Studio megbízható szereplő projekt létrehozása, automatikusan létrehoz egy illesztőfelület-projekt meg. Az aktor proxy létrehozni a szereplő folytatott kommunikációhoz a webes projekt használhatja, hogy a illesztő. A kommunikációs csatornát automatikusan valósul meg. Így nem kell tennie semmit, egyenértékű létrehozó egy `ServiceRemotingListener` például ebben az oktatóanyagban az állapotalapú szolgáltatásból esetében tette.

## <a name="how-web-services-work-on-your-local-cluster"></a>A helyi fürtön lévő webes szolgáltatások működése
Általában pontosan az azonos a Service Fabric alkalmazást telepíti egy többgépes fürtben, a helyi fürtön lévő telepített, és lehet magas benne, hogy az elvárt módon működik. Ez azért, mert a helyi fürt egyszerűen olyan öt csomópontból konfigurációt, amely egyetlen számítógépen van-e csukva.

A webszolgáltatások ismét, azonban nincs egy kulcs nuance. Ha a fürt egy terheléselosztó mögött helyezkedik el, mint az Azure-ban, győződjön meg arról, hogy a webes szolgáltatások telepítve vannak minden egyes számítógépen a terheléselosztó óta egyszerűen ciklikus multiplexelések a gépek közötti forgalmat. Ehhez úgy, hogy a `InstanceCount` a szolgáltatás számára a speciális érték-1".

Ezzel szemben ha Ön egy webszolgáltatás futtatását helyileg, gondoskodnia kell arról, hogy csak egy példánya fut. a szolgáltatás. Ellenkező esetben tapasztal ütközések a több elérési út és a port által figyelt folyamat. Ennek eredményeképpen a webes szolgáltatás példányok száma kell megadni a helyi központi telepítések elvégzéséhez "1".

Különböző környezet különböző értékeket állíthat be, lásd: [alkalmazás paramétereinek több környezet kezelése](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Következő lépések
Most, hogy a webalkalmazás első set végül az ASP.NET Core az alkalmazáshoz, további információ [ASP.NET Core a Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) az ASP.NET Core működése a Service Fabric alapos ismeretét.

Ezt követően [tudhat meg többet a szolgáltatások folytatott kommunikáció](service-fabric-connect-and-communicate-with-services.md) általában lekérni a teljes kép hogyan szolgáltatás a kommunikációt a Service Fabric működik.

Ha elvégezte a szolgáltatások közötti kommunikáció működésével, alaposan megismernie [fürt létrehozása az Azure-ban, és a felhőben az alkalmazás telepítése](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
