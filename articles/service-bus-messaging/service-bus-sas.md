---
title: Az Azure Service Bus hozzáférés-vezérlés közös hozzáférésű jogosultságkódokkal |} A Microsoft Docs
description: Közös hozzáférésű Jogosultságkódokat – áttekintés, az Azure Service busszal SAS hitelesítési adatait a Service Bus hozzáférés-vezérlés áttekintése.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2018
ms.author: spelluru
ms.openlocfilehash: adc9d64c4756e055b3ff9b772feebd80902d2c9f
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857873"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>A Service Bus hozzáférés-vezérlés közös hozzáférésű jogosultságkódokkal

*A közös hozzáférésű Jogosultságkódot* (SAS) az elsődleges biztonsági mechanizmus a Service Bus-üzenetkezelés. Ez a cikk ismerteti a SAS, hogyan működnek és azok használatát a platform-agnosztikus módon.

SAS védelmet nyújt a Service Bus-engedélyezési szabályok alapján való hozzáférést. Ezek vannak konfigurálva, a névteret, vagy egy üzenetküldési entitásra (relay, üzenetsor vagy témakör). Egy engedélyezési szabályt névvel rendelkezik, társított megadott jogokat és titkosítási kulcsok párjai végzi. A szabály nevét és kulcsát a Service Bus SDK-n keresztül, vagy saját kód használata egy SAS-token létrehozásához. Egy ügyfél ezután továbbíthatja a jogkivonat a Service Bus a kért művelet engedélyezési igazolásához.

## <a name="overview-of-sas"></a>SAS áttekintése

Közös hozzáférésű Jogosultságkódok olyan egy jogcímalapú engedélyezési mechanizmusa, egyszerű jogkivonatok használatával. Az SAS segítségével kulcsok soha nem továbbítódnak a keresztülhaladnak a hálózaton. Titkosítási szempontból aláírni az adatokat később a szolgáltatás által ellenőrzött kulcsot használnak. SAS is használható egy felhasználónevet és jelszót a rendszer hasonló, ahol az ügyfél-engedélyezési szabály neve és a egy megfelelő kulcsot azonnali birtokában van. SAS is használható hasonlóan egy összevont biztonsági modell, ahol az ügyfél fogad egy időben korlátozott és aláírt jogkivonatokat biztonságijogkivonat-szolgáltatás az aláíró kulcs birtokában minden eddiginél érkező nélkül.

A Service Bus SAS-hitelesítés van konfigurálva a nevű [megosztott hozzáférés-engedélyezési szabályok](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) kellene társított hozzáférési jogosultságokat, és az elsődleges és másodlagos titkosítási kulcsok párjai. A kulcsokat a 256 bites Base64-ábrázolásból értékeket. Szabályokat konfigurálhat a névterek szintjén, a Service Buson [továbbítókat](../service-bus-relay/relay-what-is-it.md), [üzenetsorok](/service-bus-messaging/service-bus-messaging-overview.md#queues), és [témakörök](/service-bus-messaging/service-bus-messaging-overview.md#topics).

A [közös hozzáférésű Jogosultságkód](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) jogkivonat a kiválasztott engedélyezési szabály, az erőforrás URI-ját az, hogy legyen elérhető, az azonnali, lejárati nevét tartalmazza, és a egy HMAC-SHA256 kriptográfiai aláírás számított át ezeket a mezőket az elsődleges vagy a kiválasztott engedélyezési szabály másodlagos titkosítási kulcsa.

## <a name="shared-access-authorization-policies"></a>Közös hozzáférésű engedélyezési házirendek

Minden egyes Service Bus-névtér és az egyes Service Bus-entitás rendelkezik egy megosztott hozzáférés-engedélyezési házirendet szabályok áll. A szabályzat a névterek szintjén, attól függetlenül, az egyes házirend-konfigurációt a névtéren belül minden entitás vonatkozik.

Minden egyes engedélyezési házirend szabály dönt, három információt is: **neve**, **hatókör**, és **rights**. A **neve** , csak hogy; egy adott hatókörön belül egyedi nevet. A hatókör elég egyszerűen: az adott erőforrás URI-ját. A Service Bus-névtér, a hatókör el teljesen minősített tartománynevét (FQDN), például `https://<yournamespace>.servicebus.windows.net/`.

A szabály által biztosított jogok kombinációja lehet:

* "Küldés" - jogosít fel üzeneteket küldenek az entitás
* "Figyelés" - jogosít fel (továbbítóként) figyelik, vagy a fogadási (üzenetsor, előfizetések), és az összes kapcsolódó üzenetkezelés
* "Kezelése" – jogosít fel a topológia a névtér, többek között a létrehozása és törlése az entitások kezelése

Jobb kezelése"a"Küldés"és"Receive"jogosultságokat tartalmaz.

Egy névtér vagy entitás házirend képes tárolni legfeljebb 12 megosztott hozzáférés-engedélyezési szabályok, három szabálykészletet, a hely minden egyes alapszintű jogok és a küldési és figyelési együttes használata biztosít. A korlát aláhúzások, amelyek tárolják a SAS-szabályzat nem célja, hogy egy felhasználó vagy szolgáltatás fióktároló lehet. Ha az alkalmazásnak hozzáférést biztosítani a Service Bus, a felhasználó vagy szolgáltatás-identitások alapján, azt a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól, amely a SAS-tokeneket kiad egy hitelesítés- és hozzáférés-ellenőrzést követően kell megvalósítania.

Egy engedélyezési szabályt hozzá van rendelve egy *elsődleges kulcs* és a egy *másodlagos kulcs*. Ezek a titkosítási szempontból erős kulcsokat. Nem, elvesznek vagy szivárogtatnak ki őket – mindig elérhetők legyenek a a [az Azure portal][Azure portal]. A létrehozott kulcsok bármelyikét használhatja, és bármikor helyreállíthatók. Hozza létre újra, vagy módosíthatja a kulcsot, és a szabályzatban, ha az összes korábban kiadott jogkivonatokat alapján a kulcs azonnal érvénytelenné válik. Azonban az ilyen jogkivonatok alapján létre folyamatos kapcsolatok továbbra is használhatók, amíg a jogkivonat lejár.

Service Bus-névteret hoz létre, amikor egy szabály nevű **RootManageSharedAccessKey** a névtér automatikusan létrejön. Ez a házirend a teljes névtérhez tartozó felügyeleti engedélyekkel rendelkezik. Javasoljuk, hogy kezelje-e ez a szabály, például egy felügyeleti **legfelső szintű** fiókját, és ne használja az alkalmazásban. A további szabályok is létrehozhat a **konfigurálása** lapra a névtérhez a Portal, Powershell vagy az Azure CLI-n keresztül.

## <a name="configuration-for-shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód-hitelesítés konfigurálása

Konfigurálhatja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) szabály a Service Bus-névterekhez, üzenetsorokat vagy témaköröket. Konfigurálása egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) egy Service Bus az előfizetés jelenleg nem támogatott, de a névtér vagy témakör konfigurált szabályok segítségével biztonságos hozzáférés az előfizetések. Működik a minta azt mutatja be ezt az eljárást, lásd: a [használata közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus-előfizetések](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) minta.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Ezen az ábrán a *manageRuleNS*, *sendRuleNS*, és *listenRuleNS* engedélyezési szabályok vonatkoznak V1 várólista és a témakör a T1, miközben *listenRuleQ*  és *sendRuleQ* csak V1 várólista vonatkoznak, és *sendRuleT* csak a témakör a T1 vonatkozik.

## <a name="generate-a-shared-access-signature-token"></a>Közös hozzáférésű Jogosultságkód token létrehozása

Minden olyan ügyfél, amely hozzáfér az engedélyezési szabály neve és a egy, az aláírási kulcsokat hozhat létre egy SAS-token. A jogkivonat elvégezte a karakterlánc a következő formátumban hozza létre:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Azonnali jogkivonat lejárati. Az alapidőpont óta eltelt tükröző egész `00:00:00 UTC` az 1. január 1970 (UNIX alapidőpont) amikor a jogkivonat érvényessége lejár.
* **`skn`** -A-engedélyezési szabály neve.
* **`sr`** -Az elért erőforrás URI azonosítója.
* **`sig`** -Aláírás.

A `signature-string` az SHA-256 kivonatoló számított keresztül az erőforrás-URI (**hatókör** az előző szakaszban leírtak szerint), és az azonnali, a jogkivonat lejárati karakterláncként CRLF elválasztva.

A kivonatoló számítás hasonlít az alábbi pszeudo-kódot, és a kivonat 256 bit/32-bájtos értéket ad vissza.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

A jogkivonat nem kivonatolt értékeket tartalmazza, úgy, hogy a címzett be is újraszámítható egy ugyanazokkal a paraméterekkel, a kivonatoló ellenőrzése, hogy a kibocsátó érvényes aláíró kulcs birtokában van. 

Az erőforrás URI azonosítója a teljes erőforrás URI-ját a Service Bus, amelyhez hozzáférést igényelnek. Ha például `http://<namespace>.servicebus.windows.net/<entityPath>` vagy `sb://<namespace>.servicebus.windows.net/<entityPath>`fiókerdőből, azaz `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. Az URI-t kell [kódolt](https://msdn.microsoft.com/library/4fkewx0t.aspx).

A megosztott hozzáférés-engedélyezési szabály, az aláíráshoz használt ezt az URI, vagy egy a hierarchikus szülők megadott entitást kell konfigurálni. Ha például `http://contoso.servicebus.windows.net/contosoTopics/T1` vagy `http://contoso.servicebus.windows.net` az előző példában.

SAS-token érvénytelen, az összes erőforrás előtaggal van ellátva a `<resourceURI>` használatban a `signature-string`.

## <a name="regenerating-keys"></a>Hozzáférési kulcsainak ismételt létrehozása

A használt kulcsok rendszeres újragenerálása ajánlott a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum. Az elsődleges és másodlagos kulcsok tárolóhelyei, hogy fokozatosan is kulcsainak rotálása létezik. Ha az alkalmazás általánosan az elsődleges kulcsot használ, másolja az elsődleges kulcsot a másodlagos kulcs tárolóhely, és csak ezután újragenerálja az elsődleges kulcs. Az új elsődleges kulcs értékét dokumentumtárára is konfigurálható, az ügyfélalkalmazások számára, amely továbbra is a régi elsődleges kulcsot használja a másodlagos tárolóhelyben hozzáférést. Után minden ügyfelei már frissítettek, létrehozhatja a másodlagos kulcsot végül vonja ki a régi elsődleges kulcsot.

Ha ismeri vagy feltételezhető, hogy a kulcs biztonsága sérült, és a kulcsok visszavonása az kell, újból létrehozhatja is a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) és a [adni az újragenerálni](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) , egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), és cserélje le őket az új kulcsokkal. Ez az eljárás a régi kulcsok aláírt összes jogkivonatok érvényteleníti.

## <a name="shared-access-signature-authentication-with-service-bus"></a>A Service Bus közös hozzáférésű Jogosultságkód hitelesítési

Leírása a következő forgatókönyvek között megtalálható az engedélyezési szabályok konfigurálása, valamint SAS-tokeneket, és az ügyfél engedélyezési generációja.

Egy teljes tekintse meg a Service Bus-alkalmazás, amely bemutatja a konfiguráció és a használt SAS engedélyezési, működő minta [közös hozzáférésű Jogosultságkód hitelesítés a Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Egy kapcsolódó minta, amely a SAS-engedélyezési szabályok konfigurálva a névterek vagy témaköröket biztonságossá tételéhez a Service Bus-előfizetések használatát illusztrálja érhető el itt: [használata közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus-előfizetések](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Hozzáférés a megosztott hozzáférés-engedélyezési szabályok entitás

A Service Bus .NET-keretrendszer tárak, hozzáférhet a [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektumot egy Service Bus-üzenetsor vagy témakör segítségével a konfigurálása a [szabályok](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) a megfelelő gyűjteményt [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

A következő kód bemutatja, hogyan adhat hozzá az engedélyezési szabályok várólista.

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

## <a name="use-shared-access-signature-authorization"></a>Közös hozzáférésű Jogosultságkód engedélyezési használata

Az Azure .NET SDK használatával a Service Bus .NET-kódtárakkal alkalmazások használhatják a SAS-engedélyezési keresztül a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) osztály. Az alábbi kód bemutatja a Service Bus-üzenetsorba való üzenetküldéshez jogkivonat-szolgáltató használatát. A használati látható itt is átadhat egy korábban kiadott jogkivonatot a jogkivonat-szolgáltató gyári metódus helyett.

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

Használhatja a jogkivonat-szolgáltató jogkivonatok kiállításához közvetlenül más ügyfelek számára. 

Kapcsolati karakterláncok tartalmazhatnak a szabály nevét (*SharedAccessKeyName*) és a szabály kulcs (*SharedAccessKey*) vagy egy korábban kiadott token (*SharedAccessSignature*). Ha azok szerepelnek a kapcsolati karakterlánc minden konstruktor vagy egy kapcsolati karakterláncot elfogadó gyári metódus átadott, a SAS jogkivonat-szolgáltató az automatikusan létrehozott és kitölti a rendszer.

Vegye figyelembe, hogy SAS engedélyezési használata a Service Bus-továbbítókat, a következőn: a Service Bus-névtér SAS-kulcsok is használhatja. Ha explicit módon létrehoz egy továbbítót a névtér ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) együtt egy [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objektumot, beállíthatja, hogy a továbbítás és az SAS-szabályok. SAS-engedélyezési használata a Service Bus-előfizetések, használhatja a Service Bus-névtér és a egy témakör SAS-kulcsok.

## <a name="use-the-shared-access-signature-at-http-level"></a>Használja a közös hozzáférésű Jogosultságkód (HTTP szinten)

Most, hogy tudja, hogyan hozhat létre közös hozzáférésű Jogosultságkódokat bármely entitás a Service Bus, készen áll egy HTTP POST végrehajtásához:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Ne feledje, hogy minden működik. Egy üzenetsor, témakör vagy előfizetés SAS hozhat létre.

Ha megad egy feladó vagy az ügyfél egy SAS-token, közvetlenül a kulcsot ne kelljen, és azok nem vonható vissza a kivonatot a beszerzéséhez. Emiatt a hozzáférése van mit hozzáférés felett, és milyen hosszú. Egy fontos megjegyezni, hogy ha módosítja az elsődleges kulcsot a házirend, minden olyan közös hozzáférésű Jogosultságkódokat abból létrehozott érvénytelenné válnak.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Használja a közös hozzáférésű Jogosultságkód (AMQP szinten)

Az előző szakaszban bemutatta, hogyan használható a SAS-jogkivonat HTTP POST-kérelmet adatokat küldeni a Service Bus. Mint már tudja, a Service Bus, a speciális Message Queuing AMQP protokoll, amely az előnyben részesített protokoll használata a teljesítmény javítása érdekében számos forgatókönyv használatával érheti el. A SAS-token használati AMQP-t használó a dokumentumban leírt [AMQP Claim-Based biztonsági 1.0-s verziója](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , amely a működő draft tevékenykedtem 2013 azonban jól támogatott az Azure-ban még ma.

Mielőtt hozzákezdene adatokat küldeni a Service Bus, a közzétevő kell küldenie egy AMQP üzenetben a SAS-jogkivonat nevű jól definiált AMQP csomóponthoz **$cbs** (tekintheti meg a szolgáltatás által használt beszerezni, és ellenőrizze a biztonsági Társítások "speciális" üzenetsorokkal token). A közzétevő meg kell adnia a **ReplyTo** mezőt az AMQP-üzenetben; Ez a csomópont, amelyben a szolgáltatás válaszol azokra a közzétevőkre a jogkivonat érvényesítésére (egy egyszerű kérés/válasz mintának közzétevője és a szolgáltatás közötti eredménye ). Ez a válasz csomópont jön létre "menet közben," "távoli csomópont dinamikus létrehozása" kapcsolatos különféle leírtak szerint az AMQP 1.0-s specifikációnak. Az SAS-token érvényességének ellenőrzése után a kiadó előre, és adatokat küldeni a szolgáltatás indítása.

A következő lépések bemutatják, hogyan küldhet a SAS-jogkivonat AMQP protokoll használatával a [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) könyvtár. Ez akkor hasznos, ha nem használja a hivatalos Service Bus SDK-t (például a WinRT, .net Compact Framework, a .net Micro keretrendszer és a Mono) fejlesztése C nyelven\#. Természetesen ez a kódtár akkor hasznos, hogy jobban elképzelhesse, hogyan jogcímalapú biztonsági módon működik-e az AMQP szintjén bemutatta, hogyan működik a HTTP (a HTTP POST-kérelmet, és a SAS-jogkivonat az "Engedélyezés" fejléc belül küldött) szintjén. Ha már nincs szüksége az ilyen AMQP mélyreható ismereteket, is használhatja a hivatalos Service Bus SDK a .net Framework alkalmazások, amelyek az Ön számára el.

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

A `PutCbsToken()` metódus kap a *kapcsolat* (AMQP kapcsolati osztály példánya által biztosított módon a [AMQP .NET Lite könyvtár](https://github.com/Azure/amqpnetlite)), amely a TCP-kapcsolatot jelenti. a szolgáltatás és a *sasToken* paraméter, amely a SAS-token küldése. 

> [!NOTE]
> Fontos, hogy a kapcsolat létrejött-e a **SASL hitelesítési mechanizmust beállítása névtelen** (és nem az alapértelmezett egyszerű felhasználónévvel és jelszóval, ha már nincs szükség az SAS-token küldéséhez használt).
> 
> 

Ezután a közzétevő hozza létre a SAS-jogkivonat küldésére és fogadására a a válasz (tokenérvényesítés eredmény) a szolgáltatás két AMQP hivatkozást.

Az AMQP üzenet tulajdonságait és a egy egyszerű üzenet több információt tartalmaz. A SAS-jogkivonat neve (a konstruktor használatával) az üzenet törzsében. A **"ReplyTo"** tulajdonsága a csomópont nevét, az ellenőrzés eredménye a fogadó hivatkozásra (módosíthatja a nevet, ha azt szeretné, és a szolgáltatás által dinamikusan jön) fogadására. Az utolsó három alkalmazás/egyéni tulajdonságok jelzi, hogy milyen típusú műveletet rendelkezik végrehajtásához a szolgáltatást használják. Leírtak szerint a CBS-től draft meghatározása, hogy fel kell a **műveletnév** ("put-token"), a **jogkivonattípusnak** (ebben az esetben egy `servicebus.windows.net:sastoken`), és a **"név"célközönség** , amelyre a jogkivonat (a teljes entitás) érvényes.

A SAS-jogkivonat elküldése a küldő hivatkozásra, után a közzétevő a válasz a fogadó hivatkozásra kell olvasni. A választ az egy egyszerű AMQP üzenet egy alkalmazás tulajdonsággal nevű **"állapotkódot"** , amely a HTTP-állapotkód azonos értékeket tartalmazhat.

## <a name="rights-required-for-service-bus-operations"></a>A Service Bus-műveletekhez szükséges jogosultságokkal

Az alábbi táblázat bemutatja a Service Bus-erőforrások különféle műveletekhez szükséges hozzáférési jogosultságokat.

| Művelet | Az igényt szükséges | Jogcím-hatókör |
| --- | --- | --- |
| **Namespace** | | |
| Engedélyezési szabály konfigurálása egy névtérhez |Kezelés |Minden névtér címe |
| **Service Registry** | | |
| Privát házirendek számbavétele |Kezelés |Minden névtér címe |
| Megkezdheti a figyelést egy névtérhez |Figyelés |Minden névtér címe |
| Üzenetek küldése egy figyelőt egy névtérben |Küldés |Minden névtér címe |
| **várólista** | | |
| Üzenetsor létrehozása |Kezelés |Minden névtér címe |
| Üzenetsor törlése |Kezelés |Bármely érvényes üzenetsor címe |
| Üzenetsorok számbavétele |Kezelés |$ Erőforrások/üzenetsorok |
| Az üzenetsor leírását beolvasása |Kezelés |Bármely érvényes üzenetsor címe |
| Várólista engedélyezési szabály konfigurálása |Kezelés |Bármely érvényes üzenetsor címe |
| Történő küldése az üzenetsorba |Küldés |Bármely érvényes üzenetsor címe |
| Üzenetek fogadása egy üzenetsorból |Figyelés |Bármely érvényes üzenetsor címe |
| Abandon, vagy fejezze be az üzenetek Service Bus-módban az üzenet fogadása után |Figyelés |Bármely érvényes üzenetsor címe |
| A későbbi beolvasásához üzenet késleltetése |Figyelés |Bármely érvényes üzenetsor címe |
| Kézbesítetlen üzenet |Figyelés |Bármely érvényes üzenetsor címe |
| A message queue munkamenet társított állapot lekérése |Figyelés |Bármely érvényes üzenetsor címe |
| Állítsa be a üzenet-várólista munkamenethez tartozó állapotát |Figyelés |Bármely érvényes üzenetsor címe |
| Ütemezés későbbi kézbesítésre; üzenet Ha például [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Figyelés | Bármely érvényes üzenetsor címe
| **A témakör** | | |
| Üzenettémakör létrehozása |Kezelés |Minden névtér címe |
| A témakör törlése |Kezelés |Bármely érvényes témakör címe |
| Témakörök számbavétele |Kezelés |$ Erőforrások/kapcsolatos témakörök |
| A témakör leírása beolvasása |Kezelés |Bármely érvényes témakör címe |
| A témakör az engedélyezési szabály konfigurálása |Kezelés |Bármely érvényes témakör címe |
| Elküldhet a témakörbe |Küldés |Bármely érvényes témakör címe |
| **Előfizetés** | | |
| Előfizetés létrehozása |Kezelés |Minden névtér címe |
| Előfizetés törlése |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Előfizetések számbavétele |Kezelés |.. / myTopic/előfizetések |
| Első előfizetés leírása |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Abandon, vagy fejezze be az üzenetek Service Bus-módban az üzenet fogadása után |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| A későbbi beolvasásához üzenet késleltetése |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Kézbesítetlen üzenet |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| A témakör munkamenet társított állapot lekérése |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Állítsa be a témakör munkamenethez tartozó állapotát |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| **Szabályok** | | |
| Szabály létrehozása |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Szabály törlése |Kezelés |.. /myTopic/Subscriptions/mySubscription |
| Szabályok számbavétele |Kezelheti, vagy hallgassa |.. /myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [How to use Service Bus Queues](service-bus-dotnet-get-started-with-queues.md) (A Service Bus-üzenetsorok használata)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com