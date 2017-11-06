---
title: "Az Azure Service Bus authentication megosztott hozzáférési aláírásokkal |} Microsoft Docs"
description: "Service Bus hitelesítési megosztott hozzáférési aláírásokkal – áttekintés, SAS-hitelesítés és az Azure Service Bus adatait áttekintése."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>A Service Bus hitelesítési megosztott hozzáférési aláírásokkal

*Közös hozzáférésű Jogosultságkód* (SAS) a Service Bus üzenetkezelés elsődleges biztonsági mechanizmust. Ez a cikk ismerteti, amelyek SAS, leírás és a platform-független módon használatával.

SAS hitelesítési lehetővé teszi, hogy az alkalmazások a Service Bus a névtér, vagy az üzenetküldési entitásra (üzenetsor vagy témakör), amellyel adott jogosultságok konfigurálva hozzáférési kulcs használata a hitelesítéshez. Ezt a kulcsot egy SAS-jogkivonatot, amellyel az ügyfelek pedig a Service Bus felé történő hitelesítésre létrehozásához használhatja.

SAS-hitelesítési támogatás az Azure SDK 2.0-s és újabb verziók része.

## <a name="overview-of-sas"></a>SAS áttekintése

Megosztott hozzáférési aláírásokkal olyan hitelesítési mechanizmus biztonságos SHA-256 kivonatokkal vagy URI-k alapján. SAS, akkor ez egy nagyon erős eszköz összes Service Bus szolgáltatás által használt. Tényleges használatban van, SAS két részből áll: egy *megosztott hozzáférési házirend* és egy *közös hozzáférésű Jogosultságkód* (gyakran nevezik egy *token*).

SAS-hitelesítés a Service Bus során konfigurálnia kell a Service Bus erőforrás társított jogosultságok titkosítási kulcsot. Ügyfelek Service Bus erőforrásainak elérésére jogcím egy SAS-jogkivonat segítségével. Az erőforrás URI azonosítója férnek hozzá a token áll, és elévülés konfigurált kulccsal aláírva.

A Service Bus közös hozzáférésű Jogosultságkód-engedélyezési szabályok konfigurálásával [továbbítja](service-bus-fundamentals-hybrid-solutions.md#relays), [várólisták](service-bus-fundamentals-hybrid-solutions.md#queues), és [témakörök](service-bus-fundamentals-hybrid-solutions.md#topics).

SAS-hitelesítés a következő elemeket használja:

* [Megosztott hozzáférés-engedélyezési szabály](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): A 256 bites Base64 ábrázolás elsődleges titkosítási kulcs, egy nem kötelező másodlagos kulcsot, és a kulcs nevét és társított jogosultságok (gyűjteménye *figyelésére*, *küldése*, vagy *kezelése* jogosultságok).
* [Közös hozzáférésű Jogosultságkód](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token: a HMAC-SHA256-kivonata egy erőforrás-karakterlánc, a titkosítási kulccsal, amely érhető el az erőforrás URI és egy lejárati álló generálta. Az aláírás és más elemek, a következő szakaszok ismertetik a SAS-jogkivonat létrehozásához egy karakterlánccá egyesít formázott.

## <a name="shared-access-policy"></a>Megosztott elérési házirendet

Egy fontos biztonsági Társítások megismerkedett, kezdődik, hogy egy házirend. Minden egyes házirend mellett dönt három adatra: **neve**, **hatókör**, és **engedélyek**. A **neve** most, hogy; adott hatókörén belül egyedi nevet. A hatókör elég egyszerűen: az adott erőforrás URI legyen. A Service Bus-névtér, a hatóköre a teljesen minősített tartománynevét (FQDN), például a `https://<yournamespace>.servicebus.windows.net/`.

A házirend az elérhető engedélyeket magától értetődő-nagymértékben:

* Küldés
* Figyelés
* Kezelés

Miután létrehozta a házirendet, mert van hozzárendelve egy *elsődleges kulcs* és egy *másodlagos kulcs*. Ezek a kriptográfiai erős kulcsokat. Nem elvesznek vagy szivárgás lépett fel őket – ezek mindig lesz elérhető a [Azure-portálon][Azure portal]. A generált kulcsok bármelyikét használhatja, és bármikor helyreállíthatók. Azonban ha generálja újra, vagy módosítsa az elsődleges kulcsot a házirend, minden megosztott hozzáférési aláírásokkal létre belőle a rendszer érvényteleníti.

A Service Bus-névtér létrehozásakor házirend automatikusan létrejön a teljes névteret nevű **RootManageSharedAccessKey**, és ez a házirend az összes jogosult. Nem bejelentkezni **legfelső szintű**, így nem használja ezt a házirendet, kivéve, ha valóban okunk. A további házirendeket is létrehozhat a **konfigurálása** lapra a névtérhez a portálon. Fontos megjegyezni, hogy a Service Bus (névtér, várólista stb.) egy egyetlen fa szint csak van legfeljebb 12 házirendek nem csatlakoztatható.

## <a name="configuration-for-shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód-hitelesítés konfigurációja
Konfigurálhatja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) a Szolgáltatásbusz-névterek, a várólisták vagy a témakörök szabály. Konfigurálása egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) egy Service Bus az előfizetés jelenleg nem támogatott, de a szabályok konfigurálva a névtér vagy témakör segítségével biztonságos hozzáférés a előfizetések. Működő minta azt mutatja be ezt az eljárást, tekintse meg a [Service Bus előfizetések használata közös hozzáférésű Jogosultságkód (SAS) hitelesítési](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) minta.

Ezek a szabályok 12 legfeljebb egy Service Bus-névtér, üzenetsor vagy témakör is lehet konfigurálni. Service Bus-névtér konfigurált szabályok adott névtér összes entitásának vonatkoznak.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Az ábrán a *manageRuleNS*, *sendRuleNS*, és *listenRuleNS* engedélyezési szabályai V1 várólista és témakör T1, egyaránt érvényesek közben *listenRuleQ*  és *sendRuleQ* csak a V1 várólista érvényes és *sendRuleT* témakör T1 vonatkozik.

A kulcs paraméterei egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) a következők:

| Paraméter | Leírás |
| --- | --- |
| *Kulcsnév* |Egy karakterlánc, amely a engedélyezési szabályt. |
| *PrimaryKey* |A base64-kódolású 256 bites elsődleges kulcs aláírása és a SAS-token ellenőrzése. |
| *Másodlagos kulcs* |A base64-kódolású 256 bites másodlagos kulcs aláírása és a SAS-token ellenőrzése. |
| *AccessRights* |Az engedélyezési szabály által megadott hozzáférési jogok listáját. Ezek a jogosultságok bármely gyűjtemény figyelés, a Küldés és a kezelés jogok lehet. |

A Service Bus-névtér ki van építve, amikor egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), a [kulcsnév](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) beállítása **RootManageSharedAccessKey**, alapértelmezés szerint létrejön.

## <a name="generate-a-shared-access-signature-token"></a>Egy közös hozzáférésű Jogosultságkód (jogkivonat) létrehozása

Maga a házirend nincs Service Bus hozzáférési jogkivonatot. Az objektum, a hozzáférési jogkivonat képzésére szolgáló - mind az elsődleges vagy másodlagos kulcsot is. Bármely ügyfél, amely hozzáfér az aláíró kulcsok a közös hozzáférésű engedélyezési szabályban megadott hozhat létre a SAS-jogkivonat. A jogkivonat a következő formátumú karakterláncot gondosan létrehozásával jönnek létre:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Ahol `signature-string` a hatókör a jogkivonat az SHA-256-kivonata (**hatókör** az előző szakaszban leírtak szerint) a CR LF Karakterpár fűzött és a lejárati idő (másodpercben óta kor alapidőpontjának korábban: `00:00:00 UTC` 1970. január 1. a). 

> [!NOTE]
> Egy rövid jogkivonat lejárati idejének elkerülése érdekében javasoljuk, hogy a lejárati idő értéknek megfelelően kódolja-e legalább egy 32 bites előjel nélküli egész számokat, vagy lehetőleg egy hosszú (64 bites) egész szám.  
> 
> 

A kivonatoló hasonlít-e a következő pszeudo-kódot, és 32 bájt adja vissza.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Nem kivonatolt értékei a **SharedAccessSignature** úgy, hogy a címzett számíthatja a kivonat ugyanazokkal a paraméterekkel, győződjön meg arról, hogy ugyanazt az eredményt adja vissza. Az URI megadja a hatókör, és a kulcs neve azonosítja a házirendet, a kivonatoló kiszámításához használható. Ez fontos biztonsági szempontból. Az aláírás nem egyezik, amelyet a címzett (Service Bus) számítja ki, ha a hozzáférés megtagadva. Ezen a ponton is lehet, hogy a küldő volna a kulcs eléréséhez, és a házirendben meghatározott jogosultságokat jogot kell biztosítani.

Vegye figyelembe, hogy a kódolt erőforrás URI használjon ehhez a művelethez. Az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek a Service Bus-erőforrás a teljes URI-címe. Például `http://<namespace>.servicebus.windows.net/<entityPath>` vagy `sb://<namespace>.servicebus.windows.net/<entityPath>`; Ez azt jelenti, hogy `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

Az entitás ezt az URI, vagy egyik szülőobjektumtól hierarchikus által az aláíráshoz használt megosztott hozzáférési engedélyezési szabály kell konfigurálni. Például `http://contoso.servicebus.windows.net/contosoTopics/T1` vagy `http://contoso.servicebus.windows.net` az előző példában.

A SAS-jogkivonat esetén érvényes tartozó összes erőforrást a `<resourceURI>` szerepel a `signature-string`.

A [kulcsnév](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) a SAS tokent hivatkozik a **kulcsnév** a közös hozzáférésű engedélyezési szabály a jogkivonat létrehozásához használt.

A *URL-kódolású-resourceURI* az URI-azonosítóként használt karakterlánc-aláírása során az aláírás számítási azonosnak kell lennie. Meg kell [százalék-kódolású](https://msdn.microsoft.com/library/4fkewx0t.aspx).

A használt kulcsok rendszeres újragenerálása ajánlott a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum. Alkalmazások általában kell használnia a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) SAS-token létrehozásához. Ha a kulcsok újragenerálása, le kell cserélni a [másodlagos kulcs](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) a régi elsődleges kulcs, és hozzon létre egy új kulcsot az új elsődleges kulcsként. Ez lehetővé teszi, hogy folytatja a tokenek használatára a hitelesítéshez, amely a régi elsődleges kulccsal rendelkező adtak, és még nem lejárt.

Ha a kulcs biztonsága sérül, és, hogy a kulcsok visszavonása, egyaránt helyreállíthatók a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) és a [másodlagos kulcs](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) , egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), majd cserélje Ezeket az új kulcsokkal. Ez az eljárás érvényteleníti összes jogkivonatot a régi kulccsal aláírva.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>A Service Bus közös hozzáférésű Jogosultságkód-hitelesítés használata

A következő forgatókönyvek például az engedélyezési szabályok konfigurálása, SAS-tokenje és ügyfél-hitelesítés létrehozását.

A teljes minta a Service Bus-alkalmazás, amely bemutatja a konfigurációs és a SAS engedélyezése, lásd: [közös hozzáférésű Jogosultságkód hitelesítés a Service busszal](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Kapcsolódó minta azt mutatja be a SAS-engedélyezési szabályok konfigurálva a névterek vagy témakörök biztonságossá tételéhez a Service Bus-előfizetések használatát érhető el itt: [használatával közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus előfizetések](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Egy névtér hozzáférés megosztott hozzáférés-engedélyezési szabályok

Műveletek a Service Bus-névtér legfelső szintű tanúsítvány hitelesítés szükséges. Azure-előfizetése egy felügyeleti tanúsítványt kell feltöltenie. Töltse fel egy felügyeleti tanúsítványra, kövesse a lépéseket [Itt](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate)használatával a [Azure-portálon][Azure portal]. További információ az Azure felügyeleti tanúsítványokról további információ: a [Azure tanúsítványok – áttekintés](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

A végpont közös hozzáférésű engedélyezési szabályok a Service Bus-névtér eléréséhez a következőképpen történik:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Létrehozásához egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) a Service Bus-névtér objektumazonosító, a POST műveletet ezen a végponton végrehajtását olyan JSON-vagy XML szerializálni a szabályra vonatkozó információt. Példa:

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Hasonlóképpen a GET műveletet a végpont segítségével olvassa el a névtéren beállított engedélyezési szabályokat.

A frissítés, vagy egy adott engedélyezési szabály törlés, használja a következő végpontot:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Egy entitás hozzáférés megosztott hozzáférés-engedélyezési szabályok

Próbál hozzáférni egy [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) konfigurálva a Service Bus-üzenetsor vagy témakör keresztül objektum a [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) gyűjtemény a megfelelő [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

A következő kód bemutatja, hogyan várólista engedélyezési szabályok hozzáadásához.

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

Alkalmazások az Azure .NET SDK használatával a Service Bus .NET-kódtárakra SAS engedélyezést használhatják a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) osztály. Az alábbi kód bemutatja a jogkivonat-szolgáltató egy Service Bus-üzenetsorba küldéséhez használatát.

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

Alkalmazások is használható SAS hitelesítési módszereket, amelyek fogadja el a kapcsolati karakterláncok egy SAS-kapcsolati karakterlánc használatával.

Vegye figyelembe, hogy SAS engedélyezési használata a Service Bus-továbbítók, használhatja a Service Bus-névtér konfigurált SAS-kulcsok. A névtér explicit módon létrehozott egy továbbítót ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rendelkező egy [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objektum is beállíthatja, hogy a továbbító a SAS csak szabályait. Service Bus-előfizetések az SAS-engedélyezési használja, a Service Bus-névtér és a témakör a SAS-kulcsok is használhat.

## <a name="use-the-shared-access-signature-at-http-level"></a>A közös hozzáférésű Jogosultságkód (HTTP szinten) használata

Most, hogy tudja, hogyan hozzon létre a megosztott hozzáférési aláírásokkal bármely entitás a Service Bus, készen áll egy HTTP POST elvégzéséhez:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Ne feledje, hogy ez minden működik. Az üzenetsor, témakör vagy előfizetés SAS hozhat létre. 

Ha a feladó, vagy az ügyfél egy SAS-jogkivonatot, nem rendelkeznek a kulcs közvetlenül, és azok nem fordított szerezheti be, hogy a kivonat. Hozzáférése van keresztül mi eléréséhez, és mennyi ideig. Egy fontos ne feledje, hogy ha megváltoztatja az elsődleges kulcsot a házirendben, minden megosztott hozzáférési aláírásokkal létre belőle a rendszer érvényteleníti.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>A közös hozzáférésű Jogosultságkód (AMQP szinten) használata

Az előző szakaszban megtudhatta, hogyan használható a SAS-jogkivonat HTTP POST-kérelmet a adatokat küldeni a Service Bus. Tudja, mint a speciális Message Queuing protokoll (AMQP), amely az előnyben részesített teljesítményének javítására szolgál, számos forgatókönyvben használandó protokoll használatával a Service Bus végezheti el. A SAS-token használatát a AMQP a dokumentumban ismertetett [AMQP Claim-Based biztonsági 1.0-s verziójának](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , amely a munkát vázlat 2013 jól támogatott az Azure-ban, de még ma óta.

Szeretnék adatokat küldeni a Service Bus megkezdése előtt a közzétevő kell küldenie a SAS-jogkivonat egy AMQP Kivételüzenet egy jól meghatározott AMQP csomópontot **$cbs** (tekintheti meg a beszerzést, mind a SAS ellenőrzése a szolgáltatás által használt "speciális" várólista jogkivonatok). A közzétevő meg kell adnia a **ReplyTo** az AMQP Kivételüzenet mezőben; ez a csomópont, amelyben a szolgáltatás adott válaszok a közzétevő a jogkivonatok érvényesség-ellenőrzése (egyszerű kérelem/válasz mintát közzétevő és a szolgáltatás között eredménye ). Létrejön a válasz csomópont "a parancsprogramok," "távoli csomópont dinamikus létrehozása" beszéd az AMQP 1.0-specifikáció szerint. A SAS-jogkivonat érvényességének ellenőrzése után a közzétevő haladni, és adatokat küldeni a szolgáltatás indítása.

A következő lépések bemutatják, hogyan küldhet a SAS-jogkivonat AMQP protokoll használatát a [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) könyvtár. Ez akkor hasznos, ha nem használja a hivatalos Service Bus SDK (például a WinRT, .net Compact Framework, a .net keretrendszer Micro és monó) c. fejlesztése\#. Természetesen a szalagtár akkor hasznos, annak megértésében, hogyan jogcímalapú biztonsági az AMQP szinten működik, mint megtudhatta, hogyan működik (a HTTP POST-kérelmet, és a SAS-jogkivonat belül az "Engedélyezés" fejléc küldése) HTTP szinten. Ha nem kívánja ilyen AMQP átfogó ismerete, a hivatalos Service Bus SDK használhatja a .net Framework alkalmazások, amelyek fog ő elvégezze ezt.

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

A `PutCbsToken()` metódus kap a *kapcsolat* (AMQP kapcsolat osztálypéldány által biztosított a [AMQP .NET Lite könyvtár](https://github.com/Azure/amqpnetlite)), amely a TCP-kapcsolatot jelenti. a szolgáltatás és a *sasToken* paraméter, amely a SAS-token küldeni. 

> [!NOTE]
> Fontos, hogy a kapcsolat létrejött-e a **SASL hitelesítési módszer beállítása külső** (és nem az alapértelmezett egyszerű, ha már nincs szükség a SAS-jogkivonat küldéséhez használt felhasználónév és jelszó).
> 
> 

Ezután a közzétevő két AMQP hivatkozásokat hoz küldését a SAS-jogkivonatot, és kezelőtéglától érkezett a szolgáltatás a válasz (a token érvényességi eredmény).

Az AMQP üzenet tulajdonságait, és egy egyszerű üzenet több információt tartalmaz. A SAS-jogkivonat neve (a saját konstruktoraikban használatával) az üzenet törzsét. A **"ReplyTo"** tulajdonsága a csomópont neve (módosíthatja a nevét, és a szolgáltatás által dinamikusan létrejön) fogadó hivatkozásra az ellenőrzési eredmény fogadására. Az utolsó három alkalmazáshoz vagy egyéni tulajdonságok a szolgáltatást használják annak jelzésére, hogy milyen műveletet tartalmaz végrehajtásához. A CBS-től vázlat specifikáció szerint, fel kell a **műveletnév** ("put-token"), a **jogkivonattípusnak** (az ebben az esetben a "servicebus.windows.net:sastoken"), és a **"neve" a célközönség** , amelyhez a token (a teljes entitás) érvényes.

A közzétevő után küld a SAS-jogkivonat a küldő hivatkozásra, olvasási kell a válasz a fogadó hivatkozásra. A válasz az egy egyszerű AMQP üzenet és nevű alkalmazás tulajdonsága **"állapot-kód"** , amelyek a HTTP-állapotkódot konfigurációjával megegyező értékeket tartalmazhat.

## <a name="rights-required-for-service-bus-operations"></a>A Service Bus műveletekhez szükséges jogok

A következő táblázat a különféle műveletek a Service Bus erőforrásainak szükséges hozzáférési jogok.

| Művelet | Jogcím szükséges | Jogcím-hatókör |
| --- | --- | --- |
| **Namespace** | | |
| A névtér engedélyezési szabály konfigurálása |Kezelés |Minden névtér cím |
| **Szolgáltatás-beállításjegyzék** | | |
| Magánfelhő házirendek felsorolása |Kezelés |Minden névtér cím |
| A névtér figyelését |Figyelés |Minden névtér cím |
| Üzenetek küldése egy névtérben figyelő |Küldés |Minden névtér cím |
| **Várólista** | | |
| Üzenetsor létrehozása |Kezelés |Minden névtér cím |
| Üzenetsor törlése |Kezelés |Bármilyen érvényes várósor címe |
| A várólisták számbavétele |Kezelés |$ Erőforrások/várólisták |
| A várólista leírás beolvasása |Kezelés |Bármilyen érvényes várósor címe |
| A várólisták engedélyezési szabály konfigurálása |Kezelés |Bármilyen érvényes várósor címe |
| Történő küldése az üzenetsorba |Küldés |Bármilyen érvényes várósor címe |
| Üzenetek fogadása egy üzenetsorból |Figyelés |Bármilyen érvényes várósor címe |
| Szakítsa vagy üzenetek befejezése után az üzenetet kapta a betekintés-zárolási mód |Figyelés |Bármilyen érvényes várósor címe |
| Késlelteti a későbbi beolvasásához üzenet |Figyelés |Bármilyen érvényes várósor címe |
| Kézbesítetlen levelek üzenet |Figyelés |Bármilyen érvényes várósor címe |
| Az állapot, egy üzenet-várólista munkamenethez társított beolvasása |Figyelés |Bármilyen érvényes várósor címe |
| Állítsa be a üzenet-várólista munkamenethez tartozó állapotát |Figyelés |Bármilyen érvényes várósor címe |
| **A témakör** | | |
| Üzenettémakör létrehozása |Kezelés |Minden névtér cím |
| Egy témakör törlése |Kezelés |Bármilyen érvényes témakör cím |
| Témakörök számbavétele |Kezelés |$ Erőforrások/kapcsolatos témakörök |
| A témakör leírás beolvasása |Kezelés |Bármilyen érvényes témakör cím |
| A témakör az engedélyezési szabály konfigurálása |Kezelés |Bármilyen érvényes témakör cím |
| A témakör küldése |Küldés |Bármilyen érvényes témakör cím |
| **Előfizetés** | | |
| Előfizetés létrehozása |Kezelés |Minden névtér cím |
| Előfizetés törlése |Kezelés |../myTopic/Subscriptions/mySubscription |
| Előfizetések számbavétele |Kezelés |../myTopic/előfizetések |
| Előfizetés leírását beolvasása |Kezelés |../myTopic/Subscriptions/mySubscription |
| Szakítsa vagy üzenetek befejezése után az üzenetet kapta a betekintés-zárolási mód |Figyelés |../myTopic/Subscriptions/mySubscription |
| Késlelteti a későbbi beolvasásához üzenet |Figyelés |../myTopic/Subscriptions/mySubscription |
| Kézbesítetlen levelek üzenet |Figyelés |../myTopic/Subscriptions/mySubscription |
| A témakör munkamenethez tartozó állapot beolvasása |Figyelés |../myTopic/Subscriptions/mySubscription |
| A témakör munkamenethez tartozó állapot beállítása |Figyelés |../myTopic/Subscriptions/mySubscription |
| **Szabályok** | | |
| Szabály létrehozása |Kezelés |../myTopic/Subscriptions/mySubscription |
| Szabály törlése |Kezelés |../myTopic/Subscriptions/mySubscription |
| Szabályok számbavétele |Kezelése vagy figyelésére |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Következő lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [A Service Bus-üzenetsorok használata](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com