---
title: Az Azure Service Bus hozzáférés-vezérlése megosztott hozzáférésű aláírásokkal
description: A Service Bus hozzáférés-vezérlésáttekintése a megosztott hozzáférésű aláírások használatával – áttekintés, a SAS-engedélyezés részletei az Azure Service Bus szolgáltatással.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: c381d9413c4003bc2ab9a9357ff2769e84d14c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259473"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>A Service Bus hozzáférés-vezérlése megosztott hozzáférésű aláírásokkal

*A megosztott hozzáférésű aláírások* (SAS) a Service Bus üzenetküldés elsődleges biztonsági mechanizmusai. Ez a cikk ismerteti a SAS, hogyan működnek, és hogyan kell használni őket egy platform-független módon.

A SAS engedélyezési szabályok alapján védi a Service Bus-hoz való hozzáférést. Ezek vagy névtérben, vagy üzenetkezelő entitáson (továbbításon, várólistán vagy témakörben) vannak konfigurálva. Az engedélyezési szabálynak neve van, adott jogokkal van társítva, és egy pár kriptográfiai kulcsot hordoz. A szabály nevét és kulcsát a Service Bus SDK-n keresztül vagy a saját kódban használja egy SAS-jogkivonat létrehozásához. Az ügyfél ezután átadhatja a jogkivonatot a Service Bus-nak a kért művelet engedélyezésének bizonyításához.

> [!NOTE]
> Az Azure Service Bus támogatja a Service Bus-névtérhez és az Azure Active Directory (Azure AD) használatával az entitásokhoz való hozzáférés engedélyezését. Az Azure AD által visszaadott OAuth 2.0-s jogkivonatot használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a megosztott hozzáférésű aláírások (SAS) használatával. Az Azure AD-vel nincs szükség a jogkivonatok tárolására a kódban, és potenciális biztonsági rések et kockáztat.
>
> A Microsoft azt javasolja, hogy az Azure AD-t az Azure Service Bus-alkalmazásokkal, ha lehetséges, használja. További információkért tekintse át a következő cikkeket:
> - [Hitelesítsen és engedélyezzen egy alkalmazást az Azure Active Directoryval az Azure Service Bus-entitások eléréséhez.](authenticate-application.md)
> - [Felügyelt identitás hitelesítése az Azure Active Directoryval az Azure Service Bus erőforrásainak eléréséhez](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>A SAS áttekintése

A megosztott hozzáférésű aláírások egyszerű jogkivonatokat használó jogcímalapú engedélyezési mechanizmusok. A SAS használatával a kulcsok soha nem kerülnek át a vezetéken. A kulcsok olyan információk titkosítására szolgálnak, amelyeket a szolgáltatás később ellenőrizhet. A SAS hasonló a felhasználónévhez és jelszósémához, ahol az ügyfél azonnali hatállyal rendelkezik egy engedélyezési szabály nevével és egy megfelelő kulccsal. A SAS az összevont biztonsági modellhez hasonlóan is használható, ahol az ügyfél egy korlátozott idejű és aláírt hozzáférési jogkivonatot kap egy biztonsági jogkivonat-szolgáltatásból anélkül, hogy az aláíró kulcs birtokába jutna.

A Service Bus SAS-hitelesítése a [megosztott hozzáférésengedélyezési szabályok nevű,](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) kapcsolódó hozzáférési jogokkal rendelkező, valamint egy elsődleges és másodlagos titkosítási kulcspárral van konfigurálva. A kulcsok 256 bites értékek a Base64 ábrázolásban. A szabályokat a névtér szintjén, a Service [Bus-reléken,](../service-bus-relay/relay-what-is-it.md) [várólistákon](service-bus-messaging-overview.md#queues)és [témakörökön állíthatja](service-bus-messaging-overview.md#topics)be.

A [megosztott hozzáférésű](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) aláírás-jogkivonat tartalmazza a kiválasztott engedélyezési szabály nevét, az elérendő erőforrás URI-ját, egy lejárati azonnali kulcsot és egy HMAC-SHA256 kriptográfiai aláírást, amelyet ezeken a mezőkön a kiválasztott engedélyezési szabály elsődleges vagy másodlagos kriptográfiai kulcsa alapján számítottak ki.

## <a name="shared-access-authorization-policies"></a>Megosztott hozzáférés engedélyezési házirendjei

Minden Service Bus névtér és minden Service Bus entitás rendelkezik egy megosztott hozzáférés engedélyezési házirend szabályokból álló. A névtér szintjén lévő házirend a névtérben lévő összes entitásra vonatkozik, függetlenül az egyéni házirendkonfigurációtól.

Minden engedélyezési házirend-szabálynál három adat közül kell döntenie: **név,** **hatókör**és **jogok.** A **név** csak, hogy; egy egyedi nevet a hatókörön belül. A hatókör elég egyszerű: ez a kérdéses erőforrás URI-ja. Service Bus-névtér esetén a hatókör a teljesen minősített tartománynév (FQDN), például `https://<yournamespace>.servicebus.windows.net/`.

A politikai szabály által biztosított jogok a következők lehetnek:

* "Küldés" - Jogot ad arra, hogy üzeneteket küldjön a jogi személynek
* "Listen" - Jogot biztosít a hallgatásra (továbbítás) vagy fogadásra (várólista, előfizetések) és az összes kapcsolódó üzenetkezelésre
* "Kezelés" – Jogot biztosít a névtér topológiájának kezelésére, beleértve entitások létrehozását és törlését

A "Manage" jog tartalmazza a "Küldés" és a "Fogadás" jogokat.

Egy névtér vagy entitásházirend legfeljebb 12 megosztott hozzáférés-engedélyezési szabályt tarthat fenn, így három szabálykészlet nek biztosít helyet, amelyek mindegyike az alapvető jogokra és a Küldés és figyelés kombinációjára vonatkozik. Ez a korlát kiemeli, hogy a SAS-házirendtároló nem felhasználói vagy szolgáltatásfiók-tároló. Ha az alkalmazásnak hozzáférést kell biztosítania a Service Bus felhasználói vagy szolgáltatásidentitások alapján, egy biztonsági jogkivonat-szolgáltatást kell megvalósítania, amely hitelesítési és hozzáférési ellenőrzés után SAS-jogkivonatokat ad ki.

Az engedélyezési szabály elsődleges és *másodlagos kulcshoz*van rendelve. *Primary Key* Ezek kriptográfiailag erős kulcsok. Ne veszítse el őket, és ne szivárogtassa ki őket – mindig elérhetők lesznek az [Azure Portalon.][Azure portal] Használhatja a létrehozott kulcsok bármelyikét, és bármikor újragenerálhatja őket. Ha újragenerál vagy módosít egy kulcsot a házirendben, az adott kulcson alapuló, korábban kiadott jogkivonatok azonnal érvénytelenné válnak. Az ilyen jogkivonatok alapján létrehozott folyamatos kapcsolatok azonban a jogkivonat lejáratáig továbbra is működni fognak.

Service Bus-névtér létrehozásakor a rendszer automatikusan létrehoz egy **RootManageSharedAccessKey** nevű házirendszabályt a névtérhez. Ez a házirend a teljes névtér kezelése engedélyekkel rendelkezik. Javasoljuk, hogy kezelje ezt a szabályt, mint egy **rendszergazdai** gyökérfiókot, és ne használja az alkalmazásban. További szabályzatszabályokat hozhat létre a **Konfigurálás** lapon a névtérhez a portálon, a Powershell vagy az Azure CLI segítségével.

## <a name="configuration-for-shared-access-signature-authentication"></a>A megosztott hozzáférésű aláírás hitelesítésének konfigurációja

A [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) szabályt a Service Bus névterein, várólistáin vagy témakörein konfigurálhatja. [A SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) konfigurálása egy Service Bus-előfizetésen jelenleg nem támogatott, de a névtérben vagy témakörben konfigurált szabályok kal biztosíthatja az előfizetések elérését. Az eljárást szemléltető munkaminta a [Megosztott hozzáférésű aláírás (SAS) hitelesítés használata a Service Bus-előfizetések használatával](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) című példában tekinthető meg.

![Sas](./media/service-bus-sas/service-bus-namespace.png)

Ebben az ábrán a *manageRuleNS*, *sendRuleNS*, és *listenRuleNS* engedélyezési szabályok mind a Q1 várólistára, mind a T1 témakörre vonatkoznak, míg *a listenRuleQ* és *sendRuleQ* csak a Q1 várólistára vonatkozik, *a sendRuleT* pedig csak a T1 témakörre.

## <a name="generate-a-shared-access-signature-token"></a>Megosztott hozzáférésű aláírási jogkivonat létrehozása

Minden olyan ügyfél, amely hozzáfér egy engedélyezési szabály nevének és az egyik aláíró kulcsának nevéhez, sas-jogkivonatot hozhat létre. A jogkivonat egy karakterlánc a következő formátumban történő létrehozásával jön létre:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**- Token lejárati azonnali. Egész szám, amely az `00:00:00 UTC` 1970.
* **`skn`**- Az engedélyezési szabály neve.
* **`sr`**- Az elérendő erőforrás URI-ja.
* **`sig`**- Aláírás.

Az `signature-string` az erőforrás URI-n (hatókör az előző szakaszban leírtak szerint) és a token lejárati azonnali, LF által elválasztott karakterlánc-ábrázolása az erőforrás URI-n **(hatókör)** és a token lejárati pillanatának karakterlánc-ábrázolása.

A kivonatszámítás a következő pszeudokódhoz hasonlóan néz ki, és 256 bites/32 bájtos kivonatértéket ad vissza.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

A jogkivonat tartalmazza a nem kivonatolt értékeket, így a címzett újraszámíthatja a kivonatot ugyanazzal a paraméterrel, ellenőrizve, hogy a kibocsátó rendelkezik-e érvényes aláíró kulccsal.

Az erőforrás URI-je annak a Service Bus-erőforrásnak a teljes URI-ja, amelyhez a hozzáférés igényelt. Például, `http://<namespace>.servicebus.windows.net/<entityPath>` `sb://<namespace>.servicebus.windows.net/<entityPath>`vagy ; azaz, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. 

**Az [URI-t százalékkódolással kell elkönyvelni.](https://msdn.microsoft.com/library/4fkewx0t.aspx)**

Az aláíráshoz használt megosztott hozzáférés engedélyezési szabályát az uri által megadott entitáson vagy annak egyik hierarchikus szülőjében kell konfigurálni. Például, `http://contoso.servicebus.windows.net/contosoTopics/T1` `http://contoso.servicebus.windows.net` vagy az előző példában.

A SAS-jogkivonat a `<resourceURI>` ban használt összes `signature-string`erőforrásra érvényes.

## <a name="regenerating-keys"></a>Kulcsok újragenerálása

A [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektumban használt kulcsok rendszeres időközönkénti újragenerálása ajánlott. Az elsődleges és másodlagos kulcstárolók léteznek, így a billentyűk fokozatosan forgathatók. Ha az alkalmazás általában az elsődleges kulcsot használja, átmásolhatja az elsődleges kulcsot a másodlagos kulcstárolóba, és csak ezután hozza létre újra az elsődleges kulcsot. Az új elsődleges kulcs értéke ezután konfigurálható az ügyfélalkalmazásokba, amelyek folyamatos hozzáféréssel rendelkeznek a másodlagos tárolóban lévő régi elsődleges kulcs használatával. Az összes ügyfél frissítése után újragenerálhatja a másodlagos kulcsot a régi elsődleges kulcs végleges kivonásához.

Ha tudja, vagy gyanítja, hogy egy kulcs biztonsága sérül, és vissza kell vonnia a kulcsokat, újragenerálhatja a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) és a [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) egy [SharedAccessAuthorizationRule,](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)helyettük új kulcsokat. Ez az eljárás érvényteleníti a régi kulcsokkal aláírt összes jogkivonatot.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Megosztott hozzáférésű aláírás-hitelesítés a Service Bus szolgáltatással

A forgatókönyvek a következőkben leírt közé tartozik az engedélyezési szabályok konfigurálása, A SAS-jogkivonatok létrehozása és az ügyfél-engedélyezés.

A Konfigurációt és a SAS-hitelesítést használó Service Bus-alkalmazások teljes munkamintáját a Shared Access Signature authentication with Service Bus című [témakörben tetszetős.](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8) A Névtereken vagy témakörökben a Service Bus-előfizetések biztonságossá tétele érdekében konfigurált SAS-engedélyezési szabályok használatát szemléltető kapcsolódó minta itt érhető el: [A megosztott hozzáférésű aláírás (SAS) hitelesítés használata a Service Bus-előfizetések használatával.](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Megosztott hozzáférés engedélyezési szabályainak elérése entitáson

A Service Bus . NET keretkódtárak segítségével a Service Bus várólistán vagy témakörön konfigurált [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum a megfelelő [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)számú [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) gyűjteményen keresztül érhető el.

A következő kód bemutatja, hogyan adhat hozzá engedélyezési szabályokat egy várólistához.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Megosztott hozzáférésű aláírás engedélyezése

Az Azure .NET SDK-t a Service Bus .NET kódtárakkal használó alkalmazások sas-hitelesítést használhatnak a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) osztályon keresztül. A következő kód bemutatja a jogkivonat-szolgáltató használatát a Service Bus-várólistába való üzenetküldéshez. Az itt látható használat alternatívájaként egy korábban kiadott jogkivonatot is átadhat a tokenszolgáltató gyári metódusának.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

A jogkivonat-szolgáltató közvetlenül is használhatja a jogkivonatok más ügyfeleknek történő továbbadására.

A kapcsolati karakterláncok tartalmazhatnak szabálynevet (*SharedAccessKeyName*) és szabálykulcsot (*SharedAccessKey*) vagy egy korábban kiadott jogkivonatot (*SharedAccessSignature*). Ha ezek jelen vannak a kapcsolati karakterlánc átadott bármely konstruktor vagy gyári metódus elfogadja a kapcsolati karakterláncot, a SAS jogkivonat-szolgáltató automatikusan létrejön, és kitölti.

Vegye figyelembe, hogy a SAS-engedélyezés service bus relék használatával használhatja a Service Bus névtérben konfigurált SAS-kulcsokat. Ha explicit módon hoz létre egy továbbító a névtér ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) egy [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objektum, beállíthatja a SAS-szabályok csak az adott továbbító. A SAS-engedélyezés Service Bus-előfizetésekkel való használatához használhatja a Service Bus-névtérben vagy egy témakörben konfigurált SAS-kulcsokat.

## <a name="use-the-shared-access-signature-at-http-level"></a>A megosztott hozzáférésű aláírás használata (HTTP-szinten)

Most, hogy már tudja, hogyan hozhat létre megosztott hozzáférésű aláírásokat a Service Bus bármely entitásához, készen áll a HTTP-posta végrehajtására:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Ne feledd, ez mindennél működik. SAS-t létrehozhat egy várólistához, témakörhöz vagy előfizetéshez.

Ha ad egy feladó vagy ügyfél egy SAS-jogkivonatot, nem rendelkeznek a kulcs közvetlenül, és nem tudják visszafordítani a kivonatot annak megszerzéséhez. Mint ilyen, akkor szabályozhatja, hogy mit érhetnek el, és mennyi ideig. Fontos megjegyezni, hogy ha módosítja az elsődleges kulcsot a házirendben, az abból létrehozott megosztott hozzáférési aláírások érvénytelenné válnak.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>A megosztott hozzáférésű aláírás használata (AMQP szinten)

Az előző szakaszban azt látta, hogyan használhatja a SAS-jogkivonatot egy HTTP POST-kérelemmel a Service Bus-ra történő adatküldéshez. Mint tudja, a Service Bus a Speciális Message Queuing protokoll (AMQP) használatával érhető el, amely a teljesítménybeli okokból előnyben részesített protokoll, számos esetben. Az AMQP-vel végzett SAS-jogkivonat-használatot az [AMQP jogcímalapú 1.0-s verziója](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) című dokumentum ismerteti, amely 2013 óta dolgozik, de az Azure ma már jól támogatja.

Mielőtt adatokat küldene a Service Busnak, a közzétevőnek el kell küldenie a SAS-jogkivonatot egy AMQP-üzenetben egy $cbs **nevű,** jól definiált AMQP-csomópontnak (a szolgáltatás által az összes SAS-jogkivonat beszerzéséhez és érvényesítéséhez használt "speciális" várólistaként láthatja). A közzétevőnek meg kell adnia a **ReplyTo** mezőt az AMQP üzenetben; ez az a csomópont, amelyben a szolgáltatás válaszol a közzétevőnek a jogkivonat-ellenőrzés (egy egyszerű kérés/válasz minta a közzétevő és a szolgáltatás között). Ez a válaszcsomópont "menet közben" jön létre, és az AMQP 1.0 specifikációban leírt "távoli csomópont dinamikus létrehozásáról" beszél. Miután ellenőrizte, hogy a SAS-jogkivonat érvényes-e, a közzétevő előreléphet, és elkezdheti az adatok küldését a szolgáltatásnak.

A következő lépések bemutatják, hogyan küldheti el a SAS-jogkivonatot Az AMQP protokollal a [AMQP.NET Lite-kódtár](https://github.com/Azure/amqpnetlite) használatával. Ez akkor hasznos, ha nem tudja használni a Hivatalos\#Service Bus SDK-t (például a C-ben fejlődő Hivatalos Service Bus SDK-t (például a WinRT, .NET Compact Framework, .NET Micro Framework és Mono) . Természetesen ez a könyvtár hasznos, hogy segítsen megérteni, hogyan működik a jogcímalapú biztonság az AMQP szintjén, ahogy látta, hogyan működik a HTTP szinten (egy HTTP POST-kérelem és a SAS-jogkivonat küldött az "Engedélyezés" fejléc). Ha nincs szüksége ilyen mély ismeretekre az AMQP-vel kapcsolatban, használhatja a hivatalos Service Bus SDK-t .

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

A `PutCbsToken()` metódus fogadja a *kapcsolatot* (AMQP kapcsolatosztály példány a [amqp .NET Lite könyvtár),](https://github.com/Azure/amqpnetlite)amely a TCP-kapcsolat a szolgáltatásés a *SasToken* paraméter, amely a SAS-jogkivonat küldeni.

> [!NOTE]
> Fontos, hogy a kapcsolat létre a **SASL hitelesítési mechanizmus beállítása ANONYMOUS** (és nem az alapértelmezett PLAIN felhasználónevet és jelszót használnak, ha nem kell elküldeni a SAS-jogkivonatot).
>
>

Ezután a közzétevő két AMQP-hivatkozást hoz létre a SAS-jogkivonat küldéséhez és a válasz (a jogkivonat-érvényesítési eredmény) fogadásához a szolgáltatástól.

Az AMQP-üzenet tulajdonságok készletét és több információt tartalmaz, mint egy egyszerű üzenet. A SAS-token az üzenet törzse (a konstruktor használatával). A **"ReplyTo"** tulajdonság a csomópont nevére van beállítva, hogy megkapja az érvényesítési eredményt a fogadó hivatkozáson (megváltoztathatja a nevét, ha szeretné, és a szolgáltatás dinamikusan hozza létre). Az utolsó három alkalmazás/egyéni tulajdonságot a szolgáltatás arra használja, hogy jelezze, milyen műveletet kell végrehajtania. A CBS-tervezet specifikációja szerint a **művelet nevének** ("put-token"), a **token típusának** (ebben az esetben a) `servicebus.windows.net:sastoken`és annak a **közönségnek a "neve",** amelyre a token vonatkozik (az egész entitás).

Miután elküldte a SAS-jogkivonatot a feladó hivatkozásán, a közzétevőnek el kell olvasnia a választ a címzett hivatkozáson. A válasz egy egyszerű AMQP-üzenet **egy "status-code"** nevű alkalmazástulajdonsággal, amely ugyanazokat az értékeket tartalmazhatja, mint egy HTTP-állapotkód.

## <a name="rights-required-for-service-bus-operations"></a>A Service Bus műveleteihez szükséges jogok

Az alábbi táblázat a Service Bus-erőforrások különböző műveleteihez szükséges hozzáférési jogokat mutatja be.

| Művelet | Jogcím szükséges | Jogcím hatóköre |
| --- | --- | --- |
| **Namespace** | | |
| Engedélyezési szabály konfigurálása névtéren |Kezelés |Bármilyen névtércím |
| **Szolgáltatás beállításjegyzéke** | | |
| Privát házirendek számbavétele |Kezelés |Bármilyen névtércím |
| Névtér hallgatásának megkezdése |Figyelés |Bármilyen névtércím |
| Üzenetek küldése figyelőnek névtérben |Küldés |Bármilyen névtércím |
| **Várólista** | | |
| Üzenetsor létrehozása |Kezelés |Bármilyen névtércím |
| Üzenetsor törlése |Kezelés |Bármely érvényes várólista-cím |
| Várólisták számbavétele |Kezelés |/$Resources/Várólisták |
| A várólista leírásának beszereznie |Kezelés |Bármely érvényes várólista-cím |
| Engedélyezési szabály konfigurálása várólistához |Kezelés |Bármely érvényes várólista-cím |
| Küldés a várólistába |Küldés |Bármely érvényes várólista-cím |
| Üzenetek fogadása várólistából |Figyelés |Bármely érvényes várólista-cím |
| Üzenetek elhagyása vagy befejezése az üzenet betekintési zárolási módban való fogadása után |Figyelés |Bármely érvényes várólista-cím |
| Üzenet elhalasztása későbbi visszakereséshez |Figyelés |Bármely érvényes várólista-cím |
| Deadletter egy üzenet |Figyelés |Bármely érvényes várólista-cím |
| Az üzenetsor-munkamenethez társított állapot beszerezni |Figyelés |Bármely érvényes várólista-cím |
| Üzenetvárólista-munkamenethez társított állapot beállítása |Figyelés |Bármely érvényes várólista-cím |
| Üzenet ütemezése későbbi kézbesítésre; például [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Figyelés | Bármely érvényes várólista-cím
| **Téma** | | |
| Üzenettémakör létrehozása |Kezelés |Bármilyen névtércím |
| Témakör törlése |Kezelés |Bármely érvényes témakörcím |
| Témakörök felsorolása |Kezelés |/$Resources/Témakörök |
| A témakör leírásának beszerezése |Kezelés |Bármely érvényes témakörcím |
| Témakör engedélyezési szabályának konfigurálása |Kezelés |Bármely érvényes témakörcím |
| Küldés a témakörbe |Küldés |Bármely érvényes témakörcím |
| **Előfizetés** | | |
| Előfizetés létrehozása |Kezelés |Bármilyen névtércím |
| Előfizetés törlése |Kezelés |.. /myTopic/Előfizetések/mySubscription |
| Előfizetések számbavétele |Kezelés |.. /myTopic/Előfizetések |
| Előfizetés leírásának beszerezni |Kezelés |.. /myTopic/Előfizetések/mySubscription |
| Üzenetek elhagyása vagy befejezése az üzenet betekintési zárolási módban való fogadása után |Figyelés |.. /myTopic/Előfizetések/mySubscription |
| Üzenet elhalasztása későbbi visszakereséshez |Figyelés |.. /myTopic/Előfizetések/mySubscription |
| Deadletter egy üzenet |Figyelés |.. /myTopic/Előfizetések/mySubscription |
| A témakörmunkamenethez társított állapot beszerezni |Figyelés |.. /myTopic/Előfizetések/mySubscription |
| A témakörmunkamenethez társított állapot beállítása |Figyelés |.. /myTopic/Előfizetések/mySubscription |
| **Szabályok** | | |
| Szabály létrehozása |Kezelés |.. /myTopic/Előfizetések/mySubscription |
| Szabály törlése |Kezelés |.. /myTopic/Előfizetések/mySubscription |
| Szabályok számbavétele |Kezelés vagy figyelés |.. /myTopic/Előfizetések/mySubscription/Szabályok

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [A Service Bus-várólisták használata](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
