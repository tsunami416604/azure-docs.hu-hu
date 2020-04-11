---
title: Particionálási szolgáltatás fabric szolgáltatások
description: A Service Fabric állapotalapú szolgáltatások particionálása ismerteti. A partíciók lehetővé teszik az adatok tárolását a helyi gépeken, így az adatok és a számítási adatok együtt méretezhetők.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 4edfaa74fe109c688cad733d16031e87fff1e46f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115167"
---
# <a name="partition-service-fabric-reliable-services"></a>A Service Fabric Reliable Services particionálása
Ez a cikk az Azure Service Fabric megbízható szolgáltatások particionálásának alapvető fogalmait ismerteti. A cikkben használt forráskód a [GitHubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)is elérhető.

## <a name="partitioning"></a>Particionálás
Particionálás nem egyedi a Service Fabric. Tény, hogy ez egy alapvető minta épület skálázható szolgáltatások. Tágabb értelemben úgy gondolkodhatunk a particionálásról, mint az állapot (adatok) felosztásának koncepciójára, és kisebb, hozzáférhető egységekre való számításként számíthatunk a méretezhetőség és a teljesítmény javítása érdekében. A particionálás egy jól ismert formája az [adatparticionálás][wikipartition], más néven a szilánkok.

### <a name="partition-service-fabric-stateless-services"></a>Partition Service Fabric állapotmentes szolgáltatások
Állapotnélküli szolgáltatások esetén azt gondolhatja, hogy egy partíció egy logikai egység, amely egy szolgáltatás egy vagy több példányát tartalmazza. 1. ábra egy állapotmentes szolgáltatást mutat be, amely öt példányt oszt el egy fürtön egy partíció használatával.

![Állapot nélküli szolgáltatás](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Valóban kétféle állapotmentes szolgáltatási megoldások. Az első egy olyan szolgáltatás, amely külsőleg is megmarad, például egy Azure SQL-adatbázisban (például egy olyan webhelyen, amely a munkamenet adatait és adatait tárolja). A második a számítási szolgáltatások (például egy számológép vagy kép miniatűrök), amelyek nem kezelik az állandó állapot.

Mindkét esetben egy állapotmentes szolgáltatás particionálása egy nagyon ritka forgatókönyv - a méretezhetőség és a rendelkezésre állás általában további példányok hozzáadásával érhető el. Az egyetlen alkalom, amikor azt szeretné, hogy fontolja meg több partíciót állapotmentes szolgáltatáspéldányok, amikor speciális útválasztási kérelmek teljesítéséhez szükséges.

Például olyan esetet, amikor egy adott tartományban lévő azonosítóval rendelkező felhasználókat csak egy adott szolgáltatáspéldány nak kell kiszolgálnia. Egy másik példa arra, hogy mikor particionálhat egy állapotmentes szolgáltatást, amikor egy valóban particionált háttérrendszer (pl. egy szilánkos SQL-adatbázis) rendelkezik, és szeretné szabályozni, hogy melyik szolgáltatáspéldánynak kell írnia az adatbázis-szegmensbe – vagy más előkészítési munkát kell végeznie az állapotmentes szolgáltatáson belül, amely ugyanazt a particionálási információt igényli, mint a háttérrendszerben. Az ilyen típusú forgatókönyvek is megoldhatók különböző módon, és nem feltétlenül igényel szolgáltatás particionálás.

A forgatókönyv további célja az állapotalapú szolgáltatások.

### <a name="partition-service-fabric-stateful-services"></a>Partition Service Fabric állapotalapú szolgáltatások
A Service Fabric megkönnyíti a skálázható állapotalapú szolgáltatások fejlesztését azáltal, hogy első osztályú módot kínál az állapot (adatok) particionálására. Fogalmilag egy állapotalapú szolgáltatás partícióját egy skálázási egységként is átgondolhatja, amely rendkívül megbízható a fürt csomópontjai között elosztott és kiegyensúlyozott [replikákon](service-fabric-availability-services.md) keresztül.

Particionálás a Service Fabric állapotalapú szolgáltatások környezetében utal, hogy a folyamat annak megállapítására, hogy egy adott szolgáltatáspartíció felelős a szolgáltatás teljes állapotának egy részét. (Mint már említettük, a partíció egy sor [replikák](service-fabric-availability-services.md)). Egy nagyszerű dolog a Service Fabric, hogy a partíciókat különböző csomópontokon helyezi el. Ez lehetővé teszi számukra, hogy egy csomópont erőforrás-korlátra nőjön. Az adatigények növekedésével a partíciók nőnek, és a Service Fabric újraegyensúlyozza a partíciókat a csomópontok között. Ez biztosítja a hardvererőforrások folyamatos hatékony felhasználását.

Egy példa, tegyük fel, hogy egy 5 csomós fürtés egy szolgáltatás, amely 10 partíciók és a cél három replikák. Ebben az esetben a Service Fabric egyensúlyba hozná és elosztaná a replikákat a fürtön keresztül – és csomópontonként két elsődleges [replikát](service-fabric-availability-services.md) kapna.
Ha most kell bővíteni a fürt 10 csomópontra, a Service Fabric újraegyensúlyozza az elsődleges [replikák](service-fabric-availability-services.md) között mind a 10 csomópont. Hasonlóképpen, ha 5 csomópontra skálázódik vissza, a Service Fabric újraegyensúlyozná az összes replikát az 5 csomóponton keresztül.  

2. ábra a fürt méretezése előtti és utáni 10 partíció eloszlását mutatja be.

![Állapotalapú szolgáltatás](./media/service-fabric-concepts-partitioning/partitions.png)

Ennek eredményeképpen a horizontális felskálázás érhető el, mivel az ügyfelek től érkező kérelmek számítógépek között vannak elosztva, az alkalmazás általános teljesítménye javul, és az adattömbökhöz való hozzáférésre vonatkozó versengés csökken.

## <a name="plan-for-partitioning"></a>Particionálás megtervezése
A szolgáltatás megvalósítása előtt mindig vegye figyelembe a particionálási stratégiát, amely szükséges horizontális felskálázás. Vannak különböző ways, de mindegyik összpontosít-ra mi az alkalmazás kell elérni. A cikk kontextusában, nézzük meg néhány, a fontosabb szempontokat.

Egy jó megközelítés, hogy gondoljon a szerkezet az állam, hogy meg kell particionálni, mint az első lépés.

Vegyünk egy egyszerű példát. Ha úgy lenne, hogy egy szolgáltatást egy megyei szintű szavazás, akkor létrehozhat egy partíciót minden város a megyében. Ezután a város minden lakosának szavazatait a városnak megfelelő partíción tárolhatja. Ábra 3 illusztrálja egy sor ember és a város, ahol tartózkodnak.

![Egyszerű partíció](./media/service-fabric-concepts-partitioning/cities.png)

Mivel a lakosság a városok széles körben változik, akkor a végén néhány partíciót tartalmazó sok adat (pl. Seattle) és más partíciók nagyon kevés állam (pl. Kirkland). Tehát mi a hatása, amelyek partíciók egyenetlen mennyiségű állam?

Ha úgy gondolja, a példa újra, akkor könnyen látható, hogy a partíció, amely rendelkezik a szavazatok Seattle lesz nagyobb forgalmat, mint a Kirkland egy. Alapértelmezés szerint a Service Fabric gondoskodik arról, hogy körülbelül azonos számú elsődleges és másodlagos replikák minden csomóponton. Így előfordulhat, hogy a végén a csomópontok, amelyek replikák, amelyek nagyobb forgalmat szolgálnak ki, és mások, amelyek kevesebb forgalmat szolgálnak ki. Ön lehetőleg szeretné elkerülni a hideg és meleg foltok, mint ez a klaszter.

Ennek elkerülése érdekében két dolgot kell tennie, particionálási szempontból:

* Próbálja meg particionálni az állapotot úgy, hogy egyenletesen legyen elosztva az összes partíció között.
* Jelentés betöltése a szolgáltatás egyes replikáiból. (További információ arról, hogyan, nézd meg ezt a cikket a [metrikák és a terhelés](service-fabric-cluster-resource-manager-metrics.md)). A Service Fabric lehetővé teszi a szolgáltatások által felhasznált terhelés, például a memória vagy a rekordok száma jelentésére. A jelentett metrikák alapján a Service Fabric észleli, hogy egyes partíciók nagyobb terhelést szolgálnak ki, mint mások, és újraegyensúlyozza a fürt replikák áthelyezése több megfelelő csomópontok, hogy összességében egyetlen csomópont túlterhelt.

Néha nem lehet tudni, hogy mennyi adat lesz egy adott partíción. Így egy általános javaslat az, hogy mindkettőt tegye meg – először is, egy particionálási stratégia elfogadásával, amely egyenletesen osztja el az adatokat a partíciók között, másodszor pedig a terhelés jelentésével.  Az első módszer megakadályozza a szavazási példában leírt helyzeteket, míg a második segít elsimítani a hozzáférés vagy a betöltés átmeneti különbségeit az idő múlásával.

A partíciótervezés másik aspektusa a partíciók megfelelő számának kiválasztása.
A Service Fabric szempontjából semmi, amely megakadályozza, hogy elindulanak a forgatókönyv várhatónál nagyobb számú partíciók.
Valójában, feltételezve, hogy a partíciók maximális száma érvényes megközelítés.

Ritka esetekben előfordulhat, hogy a végén több partícióra van szüksége, mint amennyit eredetileg választott. Mivel a partíciók száma nem módosítható a tény után, akkor néhány speciális partíciós megközelítést kell alkalmaznia, például egy azonos szolgáltatástípusú új szolgáltatáspéldány létrehozását. Emellett olyan ügyféloldali logikát is be kell vezetnie, amely a kéréseket a megfelelő szolgáltatáspéldányhoz irányítja, az ügyfélkód által karban tartandó ügyféloldali ismeretek alapján.

A particionálás tervezésének másik szempontja a rendelkezésre álló számítógép-erőforrások. Mivel az állapotot hozzá kell férni és tárolni kell, a következőket kell követnie:

* Hálózati sávszélesség korlátai
* Rendszermemória korlátai
* Lemeztárolási korlátok

Mi történik, ha egy futó fürterőforrás-megkötések befutnak? A válasz az, hogy egyszerűen horizontális felskálázhatja a fürtaz új követelményeknek való igazodás.

[A kapacitástervezési útmutató](service-fabric-capacity-planning.md) útmutatást nyújt a fürt igényeinek meghatározásához.

## <a name="get-started-with-partitioning"></a>A particionálás első lépései
Ez a szakasz a szolgáltatás particionálásának első lépéseit ismerteti.

A Service Fabric három partícióséma közül választhat:

* Tartományú particionálás (más néven UniformInt64Partition).
* Elnevezett particionálás. Az ebben a modellben használó alkalmazások általában olyan adatokkal rendelkeznek, amelyek egy kötött halmazon belül gyűjtőbe vihetők. Az elnevezett partíciókulcsokként használt adatmezők reprezentatiói régiók, irányítószámok, vevőcsoportok vagy más üzleti határok közé tartozik néhány gyakori példa.
* Singleton particionálás. Singleton partíciók általában akkor használatosak, ha a szolgáltatás nem igényel további útválasztást. Például az állapotmentes szolgáltatások alapértelmezés szerint ezt a particionálási sémát használják.

A named és singleton particionálási sémák a tartománytávolságú partíciók speciális formái. Alapértelmezés szerint a Visual Studio-sablonok Service Fabric használata tartományú particionálás, mivel ez a leggyakoribb és hasznos. A cikk további célja a tartományban lévő particionálási séma.

### <a name="ranged-partitioning-scheme"></a>Tartományú particionálási séma
Ez egy egész tartomány (alacsony és magas billentyűvel azonosított) és több partíció (n) megadására szolgál. N partíciókat hoz létre, amelyek mindegyike felelős a teljes partíciókulcs-tartomány nem átfedő altartományáért. Például egy tartománytávolságú particionálási séma egy alacsony kulcs 0, a magas kulcs 99, és a 4-es szám hozna létre négy partíciót, az alábbiak szerint.

![Tartományparticionált](./media/service-fabric-concepts-partitioning/range-partitioning.png)

A közös megközelítés az, hogy hozzon létre egy kivonatot az adatkészleten belüli egyedi kulcs alapján. A kulcsok ra vonatkozó gyakori példák a járműazonosító szám (VIN), az alkalmazottazonosító vagy az egyedi karakterlánc. Ezzel az egyedi kulccsal, akkor majd létrehoz egy kivonatoló kódot, modulus a kulcs tartomány, használni, mint a kulcs. Megadhatja az engedélyezett kulcstartomány felső és alsó határait.

### <a name="select-a-hash-algorithm"></a>Kivonatoló algoritmus kiválasztása
A kivonatolás fontos része a kivonatoló algoritmus kiválasztása. A megfontolás az, hogy a cél a hasonló kulcsok csoportosítása egymás hoz (helyérzékeny kivonatolás)-- vagy ha a tevékenység kell osztani széles körben az összes partíciót (terjesztési kivonatolás), ami gyakoribb.

A jó terjesztési kivonatoló algoritmus jellemzői az, hogy könnyen kiszámítható, kevés ütközése van, és egyenletesen osztja el a kulcsokat. Egy jó példa a hatékony kivonatoló algoritmus az [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) kivonatoló algoritmus.

Egy jó forrás az általános hash kód algoritmus választás a [Wikipedia oldal hash funkciókat](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Állapotalapú szolgáltatás létrehozása több partícióval
Hozzuk létre az első megbízható állapotalapú szolgáltatás több partícióval. Ebben a példában egy nagyon egyszerű alkalmazást hoz létre, ahol az összes olyan vezetéknevet tárolni szeretné, amely ugyanazzal a betűvel kezdődik ugyanabban a partícióban.

Mielőtt bármilyen kódot írna, át kell gondolnia a partíciókat és a partíciókulcsokat. Szüksége van 26 partíciók (egy-egy betű az ábécé), de mi a helyzet az alacsony és magas kulcsok?
Mivel szó szerint azt szeretnénk, hogy egy partíciót egy levelet, tudjuk használni 0, mint az alacsony kulcs és 25, mint a magas kulcs, mivel minden betű a saját kulcs.

> [!NOTE]
> Ez egy egyszerűsített forgatókönyv, mivel a valóságban az elosztás egyenetlen lenne. Az "S" vagy "M" betűkkel kezdődő vezetéknevek gyakoribbak, mint az "X" vagy "Y" betűvel kezdődőek.
> 
> 

1. Nyissa **meg a Visual Studio** > **Új** > **projekt fájlját****File** > .
2. Az **Új projekt** párbeszédpanelen válassza a Service Fabric alkalmazást.
3. Hívja a projekt "AlphabetPartitions".
4. A **Szolgáltatás létrehozása** párbeszédpanelen válassza az **Állapotalapú** szolgáltatás lehetőséget, és hívja "Alphabet.Processing" névre.
5. Állítsa be a partíciók számát. Nyissa meg az AlphabetPartitions projekt ApplicationPackageRoot mappájában található Applicationmanifest.xml fájlt, és frissítse a Processing_PartitionCount paramétert 26-ra az alábbiak szerint.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Az ApplicationManifest.xml fájlban a StatefulService elem LowKey és HighKey tulajdonságainak frissítését is frissítenie kell az alábbi módon.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Ahhoz, hogy a szolgáltatás elérhető legyen, nyisson meg egy végpontot egy porton a ServiceManifest.xml végpontelemének hozzáadásával (amely a PackageRoot mappában található) az Alphabet.Processing szolgáltatáshoz az alábbi módon:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Most a szolgáltatás úgy van konfigurálva, hogy 26 partícióval rendelkező belső végpontot figyeljen.
7. Ezután felül kell írnia a `CreateServiceReplicaListeners()` Processing osztály metódusát.
   
   > [!NOTE]
   > Ehhez a mintához feltételezzük, hogy egy egyszerű HttpCommunicationListener-t használ. A megbízható szolgáltatáskommunikációról a [Megbízható szolgáltatás kommunikációs modelljében](service-fabric-reliable-services-communication.md)talál további információt.
   > 
   > 
8. A replika által figyelt URL-cím ajánlott mintája a következő formátum: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Ezért azt szeretné, hogy konfigurálja a kommunikációs figyelő figyelni a megfelelő végpontok és ezzel a mintával.
   
    A szolgáltatás több replikája is üzemeltethető ugyanazon a számítógépen, ezért ennek a címnek egyedinek kell lennie a kópiában. Ez az oka annak, hogy a partícióazonosító + replikaazonosító szerepel az URL-címben. A HttpListener több címet is figyelhet ugyanazon a porton, feltéve, hogy az URL-előtag egyedi.
   
    Az extra GUID egy speciális eset, ahol a másodlagos replikák is figyeli az írásvédett kérelmeket. Ebben az esetben győződjön meg arról, hogy egy új egyedi címet használ, amikor az elsődlegesről másodlagosra vált, és kényszeríti az ügyfeleket a cím újbóli feloldására. A "+" itt használt cím, így a replika az összes elérhető állomáson (IP, FQDN, localhost stb.) figyel. Az alábbi kód egy példát mutat.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Azt is érdemes megjegyezni, hogy a közzétett URL kissé eltér a figyelő URL-előtagtól.
    A figyelő URL-t a HttpListener kapja meg. A közzétett URL-cím a service fabric-névi/névi szolgáltatás, amely a szolgáltatás felderítésére használt URL-címet. Az ügyfelek ezt a címet a felderítési szolgáltatáson keresztül fogják kérni. A cím, amelyet az ügyfelek kap kell, hogy a tényleges IP-cím vagy teljes tartománynév a csomópont a csatlakozáshoz. Tehát ki kell cserélnie a "+" -ot a csomópont IP-címére vagy teljes tartománynára, ahogy fent látható.
9. Az utolsó lépés a feldolgozási logika hozzáadása a szolgáltatáshoz az alábbiak szerint.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`beolvassa a partíció hívásához használt lekérdezési `AddUserAsync` karakterlánc paraméter értékeit, és `dictionary`felhívja a vezetéknév hozzáadását a megbízható szótárhoz.
10. Adjunk hozzá egy állapotmentes szolgáltatást a projekthez, hogy lássuk, hogyan hívhat meg egy adott partíciót.
    
    Ez a szolgáltatás egyszerű webes felületként szolgál, amely elfogadja a vezetéknevet lekérdezési karakterlánc-paraméterként, meghatározza a partíciókulcsot, és elküldi az Alphabet.Processing szolgáltatásnak feldolgozásra.
11. A **Szolgáltatás létrehozása** párbeszédpanelen válassza az **Állapotmentes** szolgáltatás lehetőséget, és hívja "Alphabet.Web" néven az alábbi módon.
    
    ![Állapotmentes szolgáltatás képernyőképe](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Frissítse az Alphabet.WebApi szolgáltatás ServiceManifest.xml fájljában található végpontadatokat egy port megnyitásához az alábbiak szerint.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Vissza kell adnia a ServiceInstanceListeners gyűjteményét az osztálywebben. Ismét, akkor választhat, hogy végre egy egyszerű HttpCommunicationListener.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Most végre kell hajtania a feldolgozási logikát. A HttpCommunicationListener `ProcessInputRequest` akkor hív, ha kérés érkezik. Tehát menjünk előre, és adjuk hozzá az alábbi kódot.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Lépésről lépésre haladjunk át rajta. A kód a lekérdezési karakterlánc `lastname` paraméter első betűjét egy karakterbe olvassa be. Ezután úgy határozza meg a betű partíciókulcsát, hogy kivonja a `A` vezetéknév első betűjének hexadecimális értékét.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Ne feledje, hogy ebben a példában 26 partíciót használunk partíciónként egy partíciós kulccsal.
    Ezután beszerezzük `partition` a szolgáltatás partíciót `ResolveAsync` a `servicePartitionResolver` kulcs az objektummetódus használatával. `servicePartitionResolver`definíció szerint
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    A `ResolveAsync` metódus a szolgáltatás URI-ját, a partíciókulcsot és a megszakítási jogkivonatot paraméterként veszi fel. A feldolgozási szolgáltatás szolgáltatásURI-ja a. `fabric:/AlphabetPartitions/Processing` Ezután a partíció végpontját kapjuk meg.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Végül létrehozunk a végpont URL-címét, valamint a lekérdezési karakterláncot, és meghívjuk a feldolgozási szolgáltatást.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Miután a feldolgozás befejeződött, visszaírjuk a kimenetet.
15. Az utolsó lépés a szolgáltatás tesztelése. A Visual Studio alkalmazásparamétereket használ a helyi és a felhőalapú telepítéshez. Ha a szolgáltatást 26 partícióval szeretné helyileg `Local.xml` tesztelni, frissítenie kell a fájlt az AlphabetPartitions projekt ApplicationParameters mappájában az alábbi módon:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Miután befejezte a telepítést, ellenőrizheti a szolgáltatást és annak összes partícióját a Service Fabric Explorerben.
    
    ![Szolgáltatás fabric-kezelőképernyője](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. A böngészőben a particionálási `http://localhost:8081/?lastname=somename`logikát a beírásával tesztelheti. Látni fogja, hogy minden azonos betűvel kezdődő vezetéknév ugyanabban a partícióban van tárolva.
    
    ![Böngésző képernyőképe](./media/service-fabric-concepts-partitioning/samplerunning.png)

A minta teljes forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmairól az alábbi témakörökben talál további információt:

* [A Service Fabric-szolgáltatások elérhetősége](service-fabric-availability-services.md)
* [A Service Fabric-szolgáltatások méretezhetősége](service-fabric-concepts-scalability.md)
* [A Service Fabric-alkalmazások kapacitástervezése](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
