---
title: A Service Fabric szolgáltatások particionálás |} Microsoft Docs
description: A Service Fabric állapotalapú szolgáltatások partícióazonosító ismerteti. Partíciók lehetővé teszi, hogy a helyi gépen adattárolás, adatokat és a számítás is méretezhető együtt.
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
ms.openlocfilehash: bc6f25c7a8a779d949fbd09f9a9a9a37ec83f56a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="partition-service-fabric-reliable-services"></a>A Service Fabric megbízható szolgáltatások partíció
Ez a cikk bemutatja azokat a megbízható Azure Service Fabric-szolgáltatások particionálás alapvető fogalmait. A cikkben használt forráskódját is rendelkezésre áll, a [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Particionálás
Particionálás nincs egyedi, hogy a Service Fabric. Ez valójában egy alapvető szerkezet méretezhető szolgáltatások. Egy tágabb értelemben véve a azt gondolja át egy fogalom felosztásával állapota (adatok), particionálás és méretezhetőség és teljesítmény javítása érdekében kisebb elérhető egységekbe számítási. Egy jól ismert particionálás formátuma [adatparticionálás][wikipartition], más néven horizontális.

### <a name="partition-service-fabric-stateless-services"></a>A Service Fabric állapotmentes szolgáltatások partíció
Állapotmentes szolgáltatások esetén azt is gondolja át éppen egy logikai egységet a szolgáltatás egy vagy több példányát tartalmazó partíció. 1. ábra mutatja egy állapotmentes szolgáltatások elosztva a fürt egy partíciót öt osztályt.

![Állapotmentes szolgáltatások](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Valóban két típusa van állapotmentes szolgáltatások megoldások. Az első címtárra olyan szolgáltatás, amely továbbra is fennáll állapotában kívülről, például egy Azure SQL-adatbázis (például egy webhely, amely tárolja a munkamenet-információk és adatok). A második érték csak számítási-szolgáltatások (például egy Számológép vagy kép thumbnailing), amelyeket nem kezel olyan állandó állapotokat.

A vagy állapotmentes szolgáltatások particionálás esetben egy nagyon ritkán forgatókönyv – a méretezhetőség és rendelkezésre állási általában több példány hozzáadásával érhető el. Csak akkor érdemes megfontolni a állapotmentes szolgáltatások példányok több partíciót, amikor különleges útválasztási kérések teljesítéséhez szüksége.

Tegyük fel fontolja meg egy olyan esetben, ha egy bizonyos tartomány-azonosítóval rendelkező felhasználók csak szolgáltatható által egy adott szolgáltatáspéldány. Ha sikerült partícióazonosító állapotmentes szolgáltatások egy másik példa, amikor egy valóban particionált háttér (pl. szilánkos SQL-adatbázis) rendelkezik, és szeretne szabályozni, mely szolgáltatáspéldány kell írni a adatbázis shard – vagy más belül előkészítő feladatok végrehajtására a állapot nélküli szolgáltatás, amely a azonos particionálási információra van szüksége, a háttér használatban van. Ilyen típusú forgatókönyvek is különböző módon kell megoldani, és nem feltétlenül igényel szolgáltatás particionálást.

Ez a bemutató részében az állapotalapú szolgáltatások összpontosít.

### <a name="partition-service-fabric-stateful-services"></a>A Service Fabric állapotalapú szolgáltatások partíció
A Service Fabric megkönnyíti a partíció állapotba (adatok) első osztályú úgy felajánlásával méretezhető állapotalapú szolgáltatások fejlesztéséhez. Fogalmilag, is gondol a partíció egy állapotalapú szolgáltatás, amely nagymértékben megbízható keresztül skálázási egységként [replikák](service-fabric-availability-services.md) , amely az elosztott és a fürt csomópontjai között.

A Service Fabric állapotalapú szolgáltatások keretében particionálás határozhatja meg, hogy egy adott szolgáltatáshoz partíció feladata a teljes állapotát a szolgáltatás része hivatkozik. (Ahogy korábban említettük, a partíció egy olyan [replikák](service-fabric-availability-services.md)). A Service Fabric kiváló számítógépben, hogy másik csomópontjára helyezi a partíciókat. Ez lehetővé teszi egy erőforrás csomópontszámkorlát növekedjen őket. Az adatok igények nő, partíciók nő, és a Service Fabric partíciók-csomópontokon keresztüli újra egyensúlyba hozza. Ez biztosítja, hogy a hardver-erőforrások hatékony alkalmazását.

Így például, tegyük fel például, a kiindulási pont 5-csomópontból álló fürt és egy szolgáltatás, amely 10 partíciók és három replikák célja van konfigurálva. Ebben az esetben a Service Fabric ehhez elosztása és a replikák elosztják a fürt és meg szeretné végül két fő [replikák](service-fabric-availability-services.md) csomópontonként.
Ha most szeretné terjessze ki a fürtöt, 10 csomópontok, a Service Fabric volna egyensúlyba az elsődleges [replikák](service-fabric-availability-services.md) minden 10 csomópontra. Hasonlóképpen akkor vissza 5 csomópontok méretezhető, ha a Service Fabric volna egyensúlyba a replikák a 5 csomópontjai között.  

2. ábra 10 partíciók előtt és után a fürt skálázás eloszlását mutatja.

![Az állapotalapú szolgáltatás](./media/service-fabric-concepts-partitioning/partitions.png)

Ennek eredményeképpen a kibővített érhető el, mivel az ügyfelek kérelmeinek számítógépek különböző pontjain, az alkalmazás általános teljesítmény akkor javul, és adattömböket írnak való versengés csökken.

## <a name="plan-for-partitioning"></a>Particionálás tervezése
A szolgáltatás üzembe, mielőtt mindig vegye figyelembe a particionálási stratégia szükséges ahhoz, hogy ki. Különböző módja van, de ezek összpontosítani kell az alkalmazás eléréséhez. Ez a cikk a környezet Mérlegeljük, néhány fontosabb szempontjait.

Egy jó megoldás, az állapot, amely lehet particionálni első lépéseként meg kell a struktúra gondolniuk.

Vegyünk egy egyszerű példa. Ha a szolgáltatás egy countywide lekérdezési létrehozásához, létrehozhat egy partíció mindegyik városhoz megyét a. Ezt követően a szavazatok minden személy tárolhatja a partícióban, amely megfelel a város városban. 3. ábra azt mutatja be, személyek és a város, amelyben található.

![Egyszerű partíció](./media/service-fabric-concepts-partitioning/cities.png)

Mivel a feltöltési város széles körben változik, akkor fordulhatnak elő néhány nagy mennyiségű adatot (pl. budapest) tartalmazó partíciókat és a többi partíció csekély állapotú (pl. Kirkland). De mi hatása, hogy a partíciók állapot egyenetlen mennyiségű?

Ha úgy gondolja, hogy a példában kapcsolatos újra, könnyen láthatja, hogy a partíció, amely tárolja a szavazatok Budapest fogja kapni az egyik Kirkland-nál nagyobb forgalmat. Alapértelmezés szerint a Service Fabric teszi arról, hogy minden egyes csomóponton elsődleges és másodlagos replikák azonos számú kapcsolatban. Így használható a replikák átadott nagyobb forgalmat, míg mások kisebb forgalmat rendelkező csomópont. Lehetőleg célszerű és meleg tesztüzeméhez ilyen fürt elkerülése érdekében.

Ennek elkerülése érdekében a particionálási szempontjából két dolgot kell tenni:

* Próbálja meg a partícióazonosító állapotát, hogy az összes partíciójára egyenletesen vannak elosztva.
* A szolgáltatás a replikák mindegyike a betöltésének jelentéséhez. (Kapcsolatban tekintse meg a cikk a [metrikák és a betöltés](service-fabric-cluster-resource-manager-metrics.md)). A Service Fabric lehetővé teszi a szolgáltatások, például a memória vagy a rekordok száma által felhasznált betöltésének jelentéséhez. A jelentett mérőszámok alapján, a Service Fabric észleli, hogy az egyes partíciók többinél magasabb terhelés szolgál, és újra egyensúlyba hozza a fürt elérhetővé tétele a megfelelő csomópontok replikák azáltal, hogy a teljes nincs csomópont túl van terhelve.

Néha nem tudja, mennyi adatot lesznek az egyes partíciók eseménysorozatában. Egy általános javasoljuk, hogy szabaddá--először jó particionálási stratégia elfogadásával, amely az adatok egyenletesen legyen a partíciókat és a második, által terjed jelentéskészítési betöltése  Az első módszer megakadályozza, hogy közben a második segítségével zökkenőmentes hozzáférést vagy terheléselosztási ideiglenes különbségeit ki adott idő alatt a szavazó példában bemutatott esetekben.

Egy másik partíció tervezési célja a először válassza ki a megfelelő számú partíciót.
A Service Fabric szempontjából nincs szükség, amely megakadályozza, hogy kezdte meg az magasabb számú partíciót adott esetben a vártnál.
Feltéve, hogy a maximális számú partíciót valójában egy érvényes megközelítés.

Ritka esetekben használható kellene kezdetben kiválasztott számánál több partíciót. Bekövetkeztek a partíciók száma nem módosítható, mert néhány speciális partíció megoldások, például egy új szolgáltatás példányának létrehozásakor az azonos típusú szolgáltatás alkalmazni kellene. Meg kell valósítania néhány ügyféloldali logikát, amely a kérelmeket továbbítja a megfelelő szolgáltatáspéldány, az Ügyfélkód kell karbantartani ügyféloldali ismeretek alapján.

Meg kell vizsgálni a particionálás a tervezés, a rendelkezésre álló számítógép-erőforrásokat. Az állapot igények is elérhető, és tárolja, akkor kötött kövesse:

* Hálózati sávszélesség korlátja
* Rendszer memóriakorlátokat
* Lemez tárolási korlátai

Így mi történik, ha egy futó fürt erőforrás korlátokat futtatja? A válasz, hogy ki lehet egyszerűen terjeszteni a fürt az új követelmények teljesítése.

[A kapacitástervezési útmutató](service-fabric-capacity-planning.md) arról, hogyan határozható meg a fürt kell hány csomópontja útmutatást nyújt.

## <a name="get-started-with-partitioning"></a>Ismerkedés a particionálás
Ez a szakasz ismerteti, hogyan lásson particionálás a szolgáltatás.

A Service Fabric kiválaszthatja, hogy három partíciós séma kínálja:

* Címkiosztási particionálás (más néven UniformInt64Partition).
* Nevű particionálást. Ez a modell általában használó alkalmazások is lehet bucketed, a kötött belül adatokkal rendelkeznek. Néhány gyakori példán elnevezett partíciókulcsok használt adatmezők lenne, régiók, postai, felhasználói csoportok vagy egyéb üzleti határokat.
* Particionálás egypéldányos. A szolgáltatás nem igényel további útválasztási egypéldányos partíciók általában használják. Például a állapotmentes szolgáltatásokhoz használja ezt a particionálási sémát alapértelmezés szerint.

Nevű és egypéldányos particionálási sémák a következők: címkiosztási partíciók speciális formája. Alapértelmezés szerint a Visual Studio-sablonok a Service Fabric használatra címkiosztási particionálás, mert az általános és a hasznos azt. Ez a cikk fennmaradó ranged particionálási sémát összpontosít.

### <a name="ranged-partitioning-scheme"></a>Címkiosztási particionálási sémát
Adjon meg egy egész tartomány (a kis és nagy kulcsot által azonosított) és a partíciók (n) számos szolgál. N partíciók, minden egyes felelős a teljes partíció-tartomány egy mozaikként, átfedés nélkül alosztály hoz létre. Például egy ranged particionálási sémát 0 alacsony kulccsal, 99 magas kulcs, és a 4 számát hozna létre négy partíciót alább látható módon.

![Particionálás között](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Általános gyakorlatként javasolt, ha az adatkészlet belül egyedi kulcs alapján kivonatát. Néhány gyakori példán kulcsok lenne, a vehicle azonosító szám (VIN), az alkalmazott azonosítója vagy egy egyedi karakterlánc. Az egyedi kulccsal, majd egy kivonatoló kódot, a kulcs tartomány, a kulcs használandó modulus hoz létre. Az alsó és felső határát az engedélyezett kulcs is megadhat.

### <a name="select-a-hash-algorithm"></a>A kivonatoló algoritmus kiválasztása
A kivonatolás fontos része a kivonatoló algoritmus kijelölése. Egy kell vizsgálni, hogy a cél az, hogy hasonló kulcsok egymást (helység bizalmas kivonatoláshoz) – csoport, vagy ha körben tevékenységet kell terjeszteni összes partíciójára (terjesztési kivonatoláshoz), amely napjainkban egyre általánosabbá.

Egy jó kivonatoló algoritmus mutatókat, hogy könnyen számítási, van néhány ütközések, és a kulcsok egyenletesen terjesztett. Egy jó példa egy hatékony kivonatoló algoritmust a [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) kivonatoló algoritmus.

Általános kivonatoló kódot algoritmus lehetőségekért megfelelő erőforrás a [Wikipedia oldalon, kivonatoló függvényeket](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Több partíciót az állapotalapú szolgáltatás létrehozása
Hozzuk létre az első megbízható állapotalapú szolgáltatás több partíciókat. Ebben a példában, hol szeretné tárolni a partícióra ugyanazzal a betűvel kezdődő összes Vezetéknév egy nagyon egyszerű alkalmazást fog létrehozni.

Kód írása előtt kell gondolniuk a partíciókat és a partíciós kulcsok. 26 partíciók (egy az ábécé minden betű), de mi van szükség az alacsony és magas kulcsok?
Szó szeretnénk / levél egy partíciót, azt használatával 0, a kis és 25 magas kulcsa, mivel mindegyik betűnek a saját kulcs.

> [!NOTE]
> Ez egy webfarmos egyszerűsített, valójában a terjesztési egyenetlen lenne. Vezetéknév "S" vagy "M" betűk kezdve gyakoribb, mint az "X" kezdetű vagy az "Y".
> 
> 

1. Nyissa meg **Visual Studio** > **fájl** > **új** > **projekt**.
2. Az a **új projekt** párbeszédpanelen válassza ki a Service Fabric-alkalmazás.
3. Hívja meg a projekt "AlphabetPartitions".
4. Az a **szolgáltatás létrehozása** párbeszédpanelen válassza ki **állapotalapú alkalmazások és szolgáltatások** szolgáltatás, és hívja meg az "Alphabet.Processing" az alábbi ábrán látható módon.
       ![Visual Studio új szolgáltatás párbeszédpanelje][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. A partíciók számának megadása. Nyissa meg a Applicationmanifest.xml fájlt a AlphabetPartitions projekt ApplicationPackageRoot mappában található, és frissítse a paraméter Processing_PartitionCount 26 alább látható módon.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Szükség az alább látható módon ApplicationManifest.xml StatefulService elemében LowKey és HighKey tulajdonságainak frissítéséhez.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. A szolgáltatás számára érhető el megnyílik egy portot a végpont ServiceManifest.xml (a PackageRoot mappában található), a végpont elem felvétele a Alphabet.Processing szolgáltatás alább látható módon:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    A szolgáltatás most 26 partíciók belső végpont figyelésére van konfigurálva.
7. A következő lépésben bírálja felül a `CreateServiceReplicaListeners()` feldolgozási osztály.
   
   > [!NOTE]
   > Ebben a példában feltételezzük, hogy egy egyszerű HttpCommunicationListener használunk. A megbízható szolgáltatás kommunikációja további információkért lásd: [a megbízható kommunikációt modell](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Az URL-címhez, amely figyeli a replika egy ajánlott mintát a következő formátumban: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Ezért konfigurálni szeretné a megfelelő végpontok, hogy az ebben a mintában figyelni a kommunikációs figyelő.
   
    A szolgáltatás több replika előfordulhat, hogy futhat ugyanarra a számítógépre, ezért ez a cím egyedinek kell lennie a replikára. Ezért Partícióazonosító + másodpéldány-azonosító az URL-cím van. HttpListener figyelheti a több címet ugyanazt a portot, amíg az URL-előtagját egyedi.
   
    A felesleges GUID van egy speciális eset, amelyen másodlagos replika is a kéréseket kell figyelnie csak olvasható. Ha ez a helyzet, győződjön meg arról, hogy egy új egyedi címet használatos való áttérés menetének elsődleges a másodlagos újra a címek feloldására ügyfelek kényszerítése szeretné. "+" használatos a címet, hogy a replika figyel az összes elérhető gazdagépet (IP, FQDM, localhost stb.) Az alábbi kódot a példáját mutatja be.
   
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
   
    Akkor is érdemes megjegyezni, hogy a közzétett URL-cím némileg eltér a figyelő URL-előtagját.
    A figyelő URL-címet kap arra, hogy HttpListener. A közzétett URL-címe a Service Fabric-szolgáltatás, a szolgáltatásészlelés használt közzétett URL-CÍMÉT. Az ügyfelek ekkor megkérdezi, ehhez a címhez, a felderítés szolgáltatáson keresztül. A cím, az ügyfelek letöltik van szüksége a tényleges IP vagy FQDN-jét a csomópont használata esetén csatlakozhassanak. Ki kell cserélni, "+", a csomópont IP-cím vagy teljes Tartománynevét, ahogy fent látható.
9. Az utolsó lépés a feldolgozó logika hozzáadása a szolgáltatás alább látható módon.
   
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
                addResult ? "sucessfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` olvassa be a lekérdezési karakterlánc paraméter hívni a partíció és hívások használt értékek `AddUserAsync` a Vezetéknév hozzáadása a megbízható szótár `dictionary`.
10. Adjunk állapotmentes szolgáltatások megtekintéséhez, hogy egy adott partíció meghívása a projekthez.
    
    Ez a szolgáltatás, amely elfogadja a lekérdezési karakterlánc paraméterként a Vezetéknév, meghatározza, hogy a partíciós kulcs, és elküldi a feldolgozás Alphabet.Processing szolgáltatás egyszerű webes felületet funkcionál.
11. Az a **szolgáltatás létrehozása** párbeszédpanelen válassza ki **Stateless** szolgáltatás, és hívja meg az "Alphabet.Web" alább látható módon.
    
    ![Állapotmentes szolgáltatások képernyőképe](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. A végpont-információ a Alphabet.WebApi szolgáltatást egy portot az alább látható módon megnyílik a ServiceManifest.xml frissítésére.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Kell visszaadnia ServiceInstanceListeners webes osztályban. Ebben az esetben választhat egy egyszerű HttpCommunicationListener végrehajtásához.
    
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
14. Most kell megvalósítani a feldolgozó logika. A HttpCommunicationListener hívások `ProcessInputRequest` Ha kérelem érkezik. Ezért lépjen tovább, és adja hozzá az alábbi kódot.
    
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
    
    Rajta lépésről lépésre bemutatjuk. A kód beolvassa a lekérdezési karakterlánc paraméter első betűjének `lastname` történő karakter lehet. Ezután határozza meg a partíciókulcs a levél hexadecimális értéket `A` és a Vezetéknév első betűjének hexadecimális érték közötti.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Ne feledje, hogy a jelen példában használjuk 26 partíciók partíciónként több partíciós kulccsal.
    A következő azt beszerzése a szolgáltatás partíció `partition` a kulcs használatával a `ResolveAsync` metódust a `servicePartitionResolver` objektum. `servicePartitionResolver` típusúként van definiálva
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    A `ResolveAsync` paraméterekként token a szolgáltatás URI-azonosítója, a partíciós kulcs és a megszakítási metódust vesz igénybe. A szolgáltatás a feldolgozási szolgáltatás URI nem `fabric:/AlphabetPartitions/Processing`. A következő azt lekérése a végpont a partíció.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Végül azt a végpont URL-cím és a lekérdezési karakterláncban hozza létre, és a feldolgozási szolgáltatás hívásához.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Ha a feldolgozás befejezése után azt írni a kimeneti vissza.
15. Az utolsó lépés a szolgáltatás teszteléséhez. A Visual Studio által használt alkalmazás paramétereket a helyi és a felhő üzembe helyezése. A szolgáltatás helyi 26 partíciókkal rendelkező teszteléséhez frissítenie kell a `Local.xml` fájlt a AlphabetPartitions projekt ApplicationParameters mappában alább látható módon:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Miután befejezte a központi telepítés, ellenőrizheti a szolgáltatás és a Service Fabric Explorerben a partíciókat.
    
    ![Service Fabric Explorer képernyőképe](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. A böngészőben, tesztelheti a particionálási logika megadásával `http://localhost:8081/?lastname=somename`. Látni fogja, hogy minden egyes Vezetéknév ugyanazzal a betűvel kezdődő tárolása az egyazon partícióra kerüljenek.
    
    ![Böngésző képernyőképe](./media/service-fabric-concepts-partitioning/samplerunning.png)

A teljes forráskód a minta érhető el a [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmak információkért tekintse át a következőket:

* [A Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Méretezhetőséget biztosít a Service Fabric-szolgáltatások](service-fabric-concepts-scalability.md)
* [Kapacitástervezés a Service Fabric-alkalmazások](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png