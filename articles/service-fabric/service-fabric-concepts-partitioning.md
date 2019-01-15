---
title: Service Fabric-szolgáltatások particionálása |} A Microsoft Docs
description: Ismerteti, hogyan lehet particionálni a Service Fabric állapotalapú szolgáltatások. Partíciók lehetővé teszi, hogy a helyi gépek való adattárolás, adatokat és a számítási együtt skálázhatók.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: msfussell
ms.openlocfilehash: 70305468ca20c48bdc26e7e000a0e5edb63508cd
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261570"
---
# <a name="partition-service-fabric-reliable-services"></a>A Service Fabric reliable services particionálása
Ez a cikk mutatja be az Azure Service Fabric reliable services particionálása alapvető fogalmait. A cikkben használt forráskódja is elérhető a [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Particionálás
A particionálás nem egyedi a Service Fabric. Ez valójában egy alapvető szerkezet méretezhető szolgáltatások. Tágabb értelemben azt gondolja át a particionálás egy osztani állapota (adatok) fogalmát és számítási méretezhetőséget és a teljesítmény javítása érdekében kisebb elérhető egységekbe. Egy jól ismert űrlap particionálás [adatparticionálás][wikipartition], más néven horizontális skálázás.

### <a name="partition-service-fabric-stateless-services"></a>A Service Fabric állapotmentes szolgáltatások partíció
Az állapotmentes szolgáltatások esetében is gondolja folyamatban van egy logikai egységet, a szolgáltatás egy vagy több példányt tartalmazó partíció. 1. ábra állapotmentes szolgáltatás öt példányban elosztva a több partíciót egy fürtöt mutat be.

![Az állapotmentes szolgáltatás](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Valójában két típusa van állapotmentes szolgáltatás megoldások. Az első utótagcímkéjét szolgáltatása továbbra is fennáll állapotában kívülről, például egy Azure SQL Database (például egy webhely, amely tárolja a munkamenet-információk és adatok). A második érték, csak számítási szolgáltatások (például Számológép- vagy képfájl thumbnailing), amely nem minden olyan állandó állapot kezelése.

A vagy esetben állapotmentes szolgáltatások particionálása nagyon ritkán fordul elő ez a forgatókönyv--méretezhetőség és rendelkezésre állását általában további példányok hozzáadásával érhető el. A csak érdemes több partíciót az állapotmentes szolgáltatás példányainak, amikor csak szüksége lehet speciális útválasztási kérelmek.

Tegyük fel fontolja meg egy esetet, ahol bizonyos számos azonosítókkal rendelkező felhasználókat kell csak kiszolgálása az internetszolgáltatójuk által egy adott szolgáltatáspéldány. Ha sikerült particionálása állapotmentes szolgáltatás egy másik példa, amikor valóban particionált háttérrendszernek (például a szilánkos SQL database) rendelkezik, és azt szeretné beállítani, hogy mely szolgáltatáspéldányban kell írni az adatbázis többi – vagy más belül előkészítő feladatok végrehajtására a állapotmentes szolgáltatás, amely használatban van a háttérrendszerhez van szükség ugyanahhoz a particionálási adatok. Ezeket a feldolgozástípusokat forgatókönyveket is különböző módon kell megoldani, és nem feltétlenül szükséges szolgáltatások particionálását.

Ez az útmutató további része az állapotalapú szolgáltatások összpontosít.

### <a name="partition-service-fabric-stateful-services"></a>A Service Fabric stateful services particionálása
A Service Fabric megkönnyíti a skálázható állapotalapú szolgáltatások fejlesztése egy első osztályú módon partíció állapota (adatok). Elméleti szinten is úgy gondolja, hogy az állapotalapú szolgáltatások eloszlása kapcsolatban, amely révén nagyon megbízható skálázási egységként [replikák](service-fabric-availability-services.md) , amely az elosztott és a fürt csomópontjai között.

A folyamat, amely meghatározza, hogy egy adott szolgáltatás partíció felelős a szolgáltatás teljes állapota egy része a Service Fabric állapotalapú szolgáltatások keretében particionálás hivatkozik. (Ahogy korábban említettük, egy partíciót egy olyan [replikák](service-fabric-availability-services.md)). Tudnivalók a Service Fabric egy nagyszerű dolog, hogy a partíciók különböző csomópontokon helyezi. Ez lehetővé teszi számukra, hogy a csomópont erőforráskorlátot. Az adatok igények nő, partíciók nő, és a Service Fabric partíciók-csomópontokon keresztüli újra egyensúlyba hozza. Ez biztosítja, hogy a hardver-erőforrások folyamatos hatékony felhasználása.

Hogy egy példa, tegyük fel, hogy először a egy 5 csomópontos fürt és a egy szolgáltatás, amely 10 partíciók és a egy cél összesen három replikapéldány van konfigurálva. Ebben az esetben a Service Fabric elosztása és a replikák szét a fürt és lenne kialakított két elsődleges [replikák](service-fabric-availability-services.md) csomópontonkénti.
Ha most szeretné a 10 csomópont-fürt horizontálisan, a Service Fabric lenne újraegyensúlyozására az elsődleges [replikák](service-fabric-availability-services.md) minden 10 csomópont között. Hasonlóan ha vissza az 5 csomópont van ellátva, a Service Fabric lenne újraegyensúlyozására minden replika 5 csomópontjai között.  

2. ábra 10 partíciók előtt és után a fürt méretezése elosztását mutatja.

![Állapotalapú szolgáltatás](./media/service-fabric-concepts-partitioning/partitions.png)

Ennek eredményeképpen a kibővített érhető el, mivel az ügyfelektől érkező kérelmek vannak elosztva a számítógépek, az alkalmazás általános teljesítménye javult, és csökkenti a versengést az adattömböket az adatok elérését.

## <a name="plan-for-partitioning"></a>A particionálás tervezése
Egy szolgáltatás-k megvalósítása előtt minden esetben fontolja meg a szükséges horizontális particionálási stratégia. Különböző módja van, de ezek mindegyike összpontosítson az alkalmazásnak kell érhet el. Ez a cikk a környezetnek vegyünk néhány fontosabb szempontjait.

Egy jó módszer, gondolja át az állapot, amely lehet particionálni első lépéseként meg kell a struktúra.

Vessünk egy egyszerű példa. Ha egy szolgáltatás countywide lekérdezési hozhat létre, létrehozhat egy partíció mindegyik városhoz a megyét. Ezután minden egyes személy számára a szavazatok tárolhatja az a partíció, amely megfelel a várost az városa. 3. ábra azt szemlélteti, személyek és a város, amelyben található.

![Egyszerű partíció](./media/service-fabric-concepts-partitioning/cities.png)

A cities, population érték nagy eltéréseket mutat, mivel bizonyos mennyiségű adatot (pl. budapest) tartalmazó partíciókat és a többi partíción nagyon kevés állapota (pl. Kirkland), előfordulhat, hogy megtörténhet. Tehát Mi az a partíció állapota egyenetlen mennyiségű hatását?

Ha úgy gondolja, hogy a példában kapcsolatos újra, könnyen látható, hogy a partíció, amely tartalmazza a szavazatok Seattle fog kapni, mint a Kirkland egy nagyobb forgalmat. Alapértelmezés szerint a Service Fabric gondoskodik arról, hogy minden egyes csomóponton elsődleges és másodlagos replikák azonos számú kapcsolatban. Ezért fordulhatnak elő, amelyek rendelkeznek a replikákat, amely nagyobb forgalmat és mások, amelyek kevesebb forgalmat csomóponttal. Lehetőleg szeretne elkerülése érdekében a gyakran és ritkán használt kritikus pontok elkerülése érdekében ilyen egy fürtben.

Annak érdekében, hogy ennek elkerülése érdekében a particionálási szempontjából két dolgot kell tennie:

* Próbálja meg partíció állapotát, hogy egyenletesen legyen elosztva az összes partíciót.
* A szolgáltatás a replikák mindegyike a jelentés betöltése. (További információkért tekintse meg ebben a cikkben a [metrikák és a terhelés](service-fabric-cluster-resource-manager-metrics.md)). A Service Fabric lehetővé teszi, hogy a jelentés betöltési használja fel szolgáltatásokat, például memória mennyisége vagy a rekordok száma. A jelentett mérőszámok alapján a Service Fabric észleli, hogy az egyes partíciók szolgálnak ki, mint a többi magasabb terhelés, és a fürt rebalances a megfelelő csomópontok replikák helyezi át, hogy a teljes nem csomópont túl van terhelve.

Egyes esetekben nem tudja, mennyi adatot egy adott partíció lesz. Így egy általános ajánlás az, hogy mindkét – először particionálási stratégia bevezetésével, amely az adatok egyenletesen a partíciók és a második, által terjed jelentéskészítési betöltése  Az első módszer megakadályozza, hogy olyan helyzetekben, míg a második segítségével zökkenőmentes ideiglenes fennálló különbségek hozzáférést és a terhelés idővel a szavazási példában bemutatott módon.

A partíció tervezés szerepet játszó másik tényező, hogy először válassza ki a megfelelő számú partíciót.
A Service Fabric-perspektívát nincs semmi, amely megakadályozza, hogy ismerkednek a partíciók száma, a forgatókönyv a vártnál.
Feltéve, hogy a partíciók maximális száma valójában egy érvényes megközelítés.

Bizonyos ritkán előforduló esetekben, előfordulhat, hogy végül kellene a kezdetben kiválasztott számánál több partíciót. A partíciók száma után az a tény nem módosítható, mivel néhány speciális partíció megoldások, például egy új szolgáltatáspéldány azonos típusú szolgáltatás létrehozása a alkalmazni kell. Is kell bizonyos ügyféloldali logikát, amely a megfelelő szolgáltatáspéldány, az Ügyfélkód kell karbantartani az ügyféloldali ismeretek alapján irányítja a kérelmeket.

A particionálás tervezési egy másik szempont, a rendelkezésre álló számítógép erőforrásai. Az állapot elérése és tárolt igényekhez, vannak kötve kövesse:

* Hálózati sávszélesség korlátja
* Rendszer memóriakorlátokat
* Tárolási korlátok

Tehát mi történik, ha egy futó fürt erőforrás-korlátozások? A válasz az, hogy egyszerűen horizontális felskálázása az új követelményeknek megfelelően a fürtöt.

[A kapacitástervezési útmutató](service-fabric-capacity-planning.md) miként állapítható meg, hány csomóponttal, a fürt szükséges útmutatást kínál.

## <a name="get-started-with-partitioning"></a>A particionálás használatának első lépései
Ez a szakasz ismerteti a particionálást a szolgáltatás használatának első lépései.

A Service Fabric kínálja három partíciós séma közül választhat:

* Előre particionálás (más néven UniformInt64Partition).
* Neve a particionálást. Ez a modell általában használó alkalmazások is lehet bucketed, a körülhatárolt belül adatainak kell. Datová Pole elnevezett partíciókulcsok használt néhány gyakori példa régiók, postai kódok, felhasználói csoportok vagy egyéb üzleti határok lenne.
* Egyszeres particionálás. A szolgáltatás nem igényel további útválasztási egyszeres partíciók általában használják. Például állapotmentes szolgáltatások alapértelmezés szerint használják ennek a particionálási sémának.

Nevű és egypéldányos particionálási sémákat előre partíciók speciális formája. Alapértelmezés szerint a Service Fabric használatát a Visual Studio-sablonok előre particionálást, mivel a leggyakoribb és hasznos egy. Ez a cikk további része a ranged particionálási séma összpontosít.

### <a name="ranged-partitioning-scheme"></a>Előre a particionálási séma
Ennek segítségével adja meg egy egész szám tartományának (azonosított egy alacsony és magas kulcsot) és a megfelelő számú partíciót (n). Minden egyes felelős egy, az általános partíciókulcs-tartományok nem fedhetik alosztály n partíciók hoz létre. Például egy ranged particionálási sémát 0 kulccsal alacsony, magas kulcs 99-es és 4 számát hozna létre négy partíciót alább látható módon.

![Tartományba a particionálása](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Általánosan használt megközelítés, hogy az adatkészlet belül egyedi kulcs alapján kivonatot hoz létre. Néhány gyakori példa kulcsok lenne egy jármű-azonosító szám (VIN), az alkalmazott azonosítója vagy egy egyedi karakterlánccá. Az egyedi kulcs használatával, majd hoz létre a kivonatkód, modulus kulcstartományhoz, a kulcs használatára. Az engedélyezett kulcs tartomány felső és alsó meze is megadhat.

### <a name="select-a-hash-algorithm"></a>Válasszon kivonatoló algoritmust
Fontos része annak a kivonatolás kijelölése a kivonatoló algoritmus. Veszi figyelembe a cél (helye bizalmas kivonatoláshoz) – egymáshoz közel hasonló kulcsokat csoportosítására-e, vagy ha a tevékenység széles körben terjesztése összes partíciójára (kivonatoló terjesztési), amely jelenleg egyre gyakoribb.

Egy jó kivonatoló algoritmusa mutatókat, hogy könnyen kiszámítása, van néhány ütközések, és egyenlően osztja el a kulcsokat. Egy hatékony kivonatoló algoritmus jó példa a [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) kivonatoló algoritmust.

A leghasznosabb általános kivonatoló kódot algoritmus választásokhoz van a [Wikipédia-oldal kivonatoló függvényeket a](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Több partíciót az állapotalapú szolgáltatás készítése
Hozzuk létre az első megbízható állapotalapú szolgáltatás több partícióval. Ebben a példában egy nagyon egyszerű alkalmazás, hol szeretné tárolni a ugyanazon a partíción ugyanazon betűvel kezdődő összes Vezetéknév fog létrehozni.

Mielőtt bármilyen kódot írna, gondolja át a partíciók és a partíciókulcsok kell. 26 partíciók (egy az ábécé minden betű), de mi van szükség az alacsony és magas kulcsok?
Hogy szó szerint szeretné egy partíciót engedélyez betűvel, használhatjuk 0 az alsó kulcsa és 25 magas kulcsaként, mivel minden betű a saját kulcs.

> [!NOTE]
> Ez a lehetőség egy egyszerűsített forgatókönyvben a valóságban a terjesztési egyenetlen lenne. Vezetéknév kezdve az "S" vagy "M" betűk gyakoribbak, rétegében kezdve az "X" vagy "Y".
> 
> 

1. Nyissa meg **a Visual Studio** > **fájl** > **új** > **projekt**.
2. Az a **új projekt** párbeszédpanelen válassza ki a Service Fabric-alkalmazás.
3. Hívja meg a projekt "AlphabetPartitions".
4. Az a **szolgáltatás létrehozása** párbeszédpanelen válassza ki **állapotalapú** szolgáltatást, és azt "Alphabet.Processing" hívja az alábbi képen látható módon.
       ![A Visual Studio új szolgáltatás párbeszédpanelen][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. A partíciók számának megadása. Nyissa meg a projekt AlphabetPartitions ApplicationPackageRoot mappában található Applicationmanifest.xml fájlt, és frissítse a paraméter Processing_PartitionCount 26 alább látható módon.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Is szeretne az statefulservice-ből elemet az ApplicationManifest.xml, ahogy az alábbi LowKey és HighKey tulajdonságainak frissítéséhez.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. A szolgáltatás érhető el nyissa meg egy portot a végpont ServiceManifest.xml (PackageRoot mappájában található), a végpont elem felvétele a Alphabet.Processing szolgáltatás alább látható módon:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Most már a szolgáltatás a konfigurációja szerint 26 partícióval rendelkező egy belső végponthoz.
7. Ezután felül kell bírálnia az `CreateServiceReplicaListeners()` feldolgozási osztály metódusát.
   
   > [!NOTE]
   > Ebben a példában feltételezzük, hogy egy egyszerű HttpCommunicationListener használja. Kommunikáció a reliable Services további információkért lásd: [a Reliable Services modellt](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Ajánlott mintája a következő URL-CÍMÉT, amely figyeli egy replika a következő formátumban: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Így szeretné konfigurálni a megfelelő végponton, és ezzel a mintával figyelni a kommunikációs figyelőjének.
   
    Több replika, a szolgáltatás üzemeltethető ugyanazon a számítógépen, ezért ez a cím egyedinek kell lennie a replikára. Ezért az URL-cím van Partícióazonosító + másodpéldány-azonosító. Mindaddig, amíg az URL-cím előtag az egyedi HttpListener több cím ugyanazt a portot is figyelni.
   
    A felesleges GUID van-e egy speciális esetekhez, amelyen másodlagos replika is figyeljen a csak olvasási kérelmek. Ha ebben az esetben győződjön meg arról, hogy egy új egyedi címet használja, amikor a Váltás az elsődleges, másodlagos kényszerítése az ügyfelek számára, hogy újra feloldani a címet szeretné. "+" Itt címet használja, hogy a replika figyel az összes rendelkezésre álló gazdagép (IP, FQDM, localhost stb.) Az alábbi kódot egy példa látható.
   
    ```CSharp
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
   
    Emellett akkor is érdemes megjegyezni, hogy a közzétett URL-címe, kissé eltérhetnek a figyelő URL-előtagot.
    A figyelő URL-cím HttpListener elbírálása. A közzétett URL-je a Service Fabric elnevezési szolgáltatásban, a szolgáltatásészlelés használt közzétett URL-CÍMÉT. Ügyfelek ekkor megkérdezi, hogy felderítése adatszerkezeteket ehhez a címhez. A cím, amelyet az ügyfelek számára rendelkeznie kell a tényleges IP vagy FQDN-jének a csomópont ahhoz, hogy csatlakozhasson. Ki kell cserélni, "+" a csomópont IP vagy FQDN jelennek meg.
9. Az utolsó lépés, hogy a feldolgozási logika hozzáadása a szolgáltatás az alább látható módon.
   
    ```CSharp
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
   
    `ProcessInternalRequest` a lekérdezési karakterlánc paramétereként, a partíció és a hívások meghívható beolvasásával `AddUserAsync` a lastname hozzáadása a megbízható szótárban `dictionary`.
10. Adjunk hozzá egy állapotmentes szolgáltatás a projekthez, hogy tekintse meg, hogyan hívhatja egy adott partíció.
    
    Ez a szolgáltatás egy egyszerű webes felület, amely elfogadja a lekérdezési sztring paramétereként, a lastname, meghatározza a partíciókulcsot, és elküldi azokat a Alphabet.Processing szolgáltatásnak feldolgozásra funkcionál.
11. Az a **szolgáltatás létrehozása** párbeszédpanelen válassza ki **Stateless** szolgáltatást, és nevezze el "Alphabet.Web" alább látható módon.
    
    ![Az állapotmentes szolgáltatás képernyőképe](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Frissítse a végpont adatait elemet a ServiceManifest.xml a Alphabet.WebApi szolgáltatás nyisson meg egy portot a lent látható módon.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. A webes osztály ServiceInstanceListeners vissza kell. Újra választhat egy egyszerű HttpCommunicationListener megvalósításához.
    
    ```CSharp
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
14. Most kell a feldolgozása a logikát alkalmazzák. A HttpCommunicationListener hívások `ProcessInputRequest` amikor kérelem érkezik. Ezért haladjunk, és adja hozzá az alábbi kódot.
    
    ```CSharp
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
    
    Nézzük meg, lépésről lépésre. A kód beolvassa a lekérdezési karakterlánc paramétereként első betűje `lastname` be karakter lehet. Ezután meghatározza, hogy ez a levél partíciókulcsa hexadecimális értékét kivonásával `A` , az utolsó nevének első betűje hexadecimális értékét.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Ne feledje, hogy ebben a példában partíciónként egy partíciókulccsal rendelkező 26 partíciók használjuk.
    Ezt követően a szolgáltatás partíció célpontot `partition` a kulcs használatával a `ResolveAsync` metódust a `servicePartitionResolver` objektum. `servicePartitionResolver` típusúként van definiálva
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    A `ResolveAsync` metódust vesz igénybe a szolgáltatás URI-t, a partíciókulcs és a lemondás jogkivonat-paraméterekként. A szolgáltatás a feldolgozási szolgáltatás URI-ja `fabric:/AlphabetPartitions/Processing`. Ezután a végpont a partíció kapunk.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Végül a végpont URL-címe és a lekérdezési karakterlánc létrehozása, valamint a feldolgozási szolgáltatás hívásához.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Miután megtörtént a feldolgozása, hogy kiírhatja a kimenetet vissza.
15. Az utolsó lépéseként tesztelheti a szolgáltatást. A Visual Studio használ alkalmazásparamétereket a helyi és felhőbeli üzembe helyezés. A szolgáltatás helyi 26 partícióval rendelkező teszteléséhez frissítenie a `Local.xml` AlphabetPartitions projekt ApplicationParameters mappában fájl alább látható módon:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Ha befejezte a központi telepítés, ellenőrizheti a szolgáltatás és az összes, a Service Fabric Explorert a partíciók száma.
    
    ![A Service Fabric Explorer képernyőképe](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Egy böngészőben, tesztelheti a particionálási logikai megadásával `http://localhost:8081/?lastname=somename`. Láthatja, hogy ugyanazon a partíción tárolt egyes Vezetéknév ugyanazzal a betűvel kezdődik.
    
    ![Böngésző képernyőképe](./media/service-fabric-concepts-partitioning/samplerunning.png)

A teljes minta forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services és a Aktor elágaztatási magában
A Service Fabric reliable services és ezt követően a reliable actors elágaztatási magában nem támogatja. Miért nem támogatott például [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) regisztrálni egy nem támogatott részfolyamathoz nem használható, és a megszakítási tokeneket csak küldi el a regisztrált folyamatok; sokféle problémák, például eredményez frissítési hibák, amikor magában nem zárható be, a szülő folyamat megszakítási jogkivonatot kapott. 

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakról további információkért tekintse meg a következőket:

* [Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Service Fabric-szolgáltatások méretezhetősége](service-fabric-concepts-scalability.md)
* [Kapacitás megtervezése a Service Fabric-alkalmazások](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
