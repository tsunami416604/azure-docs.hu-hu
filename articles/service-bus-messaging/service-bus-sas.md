---
title: Az Azure Service Bus hozzáférés-vezérlés megosztott hozzáférési aláírásokkal |} Microsoft Docs
description: Megosztott hozzáférési aláírásokkal – áttekintés, az Azure Service Bus SAS hitelesítési adatait a Service Bus hozzáférés-vezérlés áttekintése.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm
ms.openlocfilehash: 420f4573fbe8b5139a4e1e5fa4dea3404c4e099d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>A Service Bus hozzáférés-vezérlés megosztott hozzáférési aláírásokkal

*Közös hozzáférésű Jogosultságkód* (SAS) a Service Bus üzenetkezelés elsődleges biztonsági mechanizmust. Ez a cikk ismerteti, amelyek SAS, leírás és a platform-független módon használatával.

SAS védi az engedélyezési szabályok alapján a Service Bus eléréséhez. Azok úgy vannak konfigurálva a névtér, vagy egy üzenetküldési entitásra (továbbító, várólista vagy témakör). Az engedélyezési szabály neve, adott jogosultságok társított és hordoz magában, ha két titkosítási kulcsok. A szabály nevét és a Service Bus SDK vagy az Ön saját kódját kulcs használatával a SAS-token létrehozásához. Egy ügyfél majd továbbíthatja a jogkivonat Service Bus számára, hogy a kért művelethez engedélyezési igazolásához.

## <a name="overview-of-sas"></a>SAS áttekintése

Megosztott hozzáférési aláírásokkal egy jogcímalapú engedélyezési mechanizmus használatával egyszerű jogkivonatokat. Az SAS segítségével kulcsok soha nem továbbítódnak a keresztülhaladnak a hálózaton. Kulcsok kriptográfiai aláírásához az információkat, amelyek később ellenőrizhetők a szolgáltatás által használt. SAS is használható egy felhasználónév és jelszó séma hasonló, ha az ügyfél egy engedélyezési szabály neve és a megfelelő kulcs azonnali birtokában van. SAS használhatók hasonló egy összevont biztonsági modell, ahol az ügyfél fogad egy időben korlátozott és aláírt jogkivonatokat biztonságijogkivonat-szolgáltatás az aláírási kulcs birtokában legalább egyszer anélkül.

A Service Bus SAS-hitelesítés van beállítva a nevű [hozzáférés-engedélyezési szabályok megosztott](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) rendelkező társított hozzáférési jogosultságokat, valamint a két elsődleges és másodlagos kriptográfiai kulcsok. A kulcsok értékeket 256 bites Base64 ábrázolás. Konfigurálja a névterek szintjén, a Service Buson szabályokat [továbbítja](service-bus-fundamentals-hybrid-solutions.md#relays), [várólisták](service-bus-fundamentals-hybrid-solutions.md#queues), és [témakörök](service-bus-fundamentals-hybrid-solutions.md#topics).

A [közös hozzáférésű Jogosultságkód](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) jogkivonat a kiválasztott engedélyezési szabályt, az erőforrás legyen elérhető, azonnali, elévülés URI nevét tartalmazza, és ezeket a mezőket keresztül számított egy titkosított HMAC-SHA256-aláírás az elsődleges vagy a kiválasztott engedélyezési szabály másodlagos titkosítási kulcsát.

## <a name="shared-access-authorization-policies"></a>Megosztott hozzáférés-engedélyezési házirendek

Minden egyes Service Bus-névtér és az egyes Szolgáltatásbusz-entitás szabályok álló megosztott hozzáférési engedélyezési házirend tartozik. A házirend a névterek szintjén valamennyi entitást, függetlenül az egyes házirend-konfigurációt a névtéren belül érvényes.

Minden engedélyezési házirend szabály mellett dönt három adatra: **neve**, **hatókör**, és **jogok**. A **neve** most, hogy; adott hatókörén belül egyedi nevet. A hatókör elég egyszerűen: az adott erőforrás URI legyen. A Service Bus-névtér, a hatóköre a teljesen minősített tartománynevét (FQDN), például a `https://<yournamespace>.servicebus.windows.net/`.

A házirendszabály korlátjai kombinációja lehet:

* "Send" - jogosít üzeneteket küldhet az entitás
* "Figyelés" - jogosít (továbbítóként) figyelik, vagy (várólista, előfizetések) fogadására és az összes kapcsolódó üzenetének
* "Kezelése" - jogosít kezelheti a topológia a névtér, beleértve a létrehozása és entitások törlése

A "Kezelése" jogot a "Send" és "Receive" jogosultságokat tartalmaz.

A névtér vagy entitás házirend legfeljebb 12 megosztott hozzáférés-engedélyezési szabályok, biztosító három különböző szabályok, illetve minden egyes alapvető jogainak és a küldési és figyelési kombinációja tárolására képes. A korlát aláhúzások, amely a SAS házirendtároló nem használhatók felhasználó vagy szolgáltatás fióktároló kell lennie. Ha az alkalmazásnak a Service Bus-felhasználó vagy szolgáltatás-identitások alapján ad engedélyt, akkor meg kell valósítania a biztonságijogkivonat-szolgáltatás, amely a SAS-jogkivonatokat bocsát ki egy hitelesítési és hozzáférés-ellenőrzést követően.

Az engedélyezési szabály hozzá van rendelve egy *elsődleges kulcs* és egy *másodlagos kulcs*. Ezek a kriptográfiai erős kulcsokat. Nem elvesznek vagy szivárgás lépett fel őket – ezek mindig lesz elérhető a [Azure-portálon][Azure portal]. A generált kulcsok bármelyikét használhatja, és bármikor helyreállíthatók. Generálja újra, vagy módosítsa a házirendet egy kulcs, ha az összes korábban kiadott jogkivonatok alapján a kulcs azonnal érvénytelenné vált. A folyamatban lévő kapcsolatok alapján ilyen jogkivonatok létre azonban továbbra is használhatók, amíg a jogkivonat lejár.

Amikor létrehoz egy Service Bus-névtér, házirendszabály nevű **RootManageSharedAccessKey** automatikusan létrejön a névtérhez. Ez a házirend a teljes névteret kezelése engedélyekkel rendelkezik. Javasoljuk, hogy úgy kezelje, ez a szabály egy rendszergazda például **legfelső szintű** fiókot, és ne használja az alkalmazásban. A további szabályok is létrehozhat a **konfigurálása** lapra a névtérhez a portálon, a Powershell vagy az Azure parancssori felület használatával.

## <a name="configuration-for-shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód-hitelesítés konfigurációja

Konfigurálhatja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) a Szolgáltatásbusz-névterek, a várólisták vagy a témakörök szabály. Konfigurálása egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) egy Service Bus az előfizetés jelenleg nem támogatott, de a szabályok konfigurálva a névtér vagy témakör segítségével biztonságos hozzáférés a előfizetések. Működő minta azt mutatja be ezt az eljárást, tekintse meg a [Service Bus előfizetések használata közös hozzáférésű Jogosultságkód (SAS) hitelesítési](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) minta.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Az ábrán a *manageRuleNS*, *sendRuleNS*, és *listenRuleNS* engedélyezési szabályai V1 várólista és témakör T1, egyaránt érvényesek közben *listenRuleQ*  és *sendRuleQ* csak a V1 várólista érvényes és *sendRuleT* témakör T1 vonatkozik.

## <a name="generate-a-shared-access-signature-token"></a>A közös hozzáférésű Jogosultságkód-token létrehozásához

Bármely ügyfél, amely hozzáfér az engedélyezési szabály neve alapján, és az aláírási kulcsokat egy SAS-jogkivonat hozhat létre. A jogkivonat a következő formátumú karakterláncot létrehozásával jönnek létre:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Csevegőüzenet jogkivonat lejáratának. A epoch óta eltelt percek tükröző egész `00:00:00 UTC` lévő a jogkivonat lejáratának 1 januárt 1970 (UNIX epoch).
* **`skn`** -Az engedélyezési szabály neve.
* **`sr`** -Az éppen elért erőforrás URI.
* **`sig`** -Aláírás.

A `signature-string` az SHA-256 kivonatoló kiszámítása történik keresztül az erőforrás URI azonosítója (**hatókör** az előző szakaszban leírtak szerint) és az azonnali, jogkivonat lejáratának karakterláncos ábrázolása CRLF elválasztva.

A kivonatoló számítás hasonlít-e a következő pszeudo-kódot, és kivonat 256 bit/32-bájtos értéket ad vissza.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

A jogkivonat nem kivonatolt értékeket tartalmazza, hogy a címzett képes számítsa újra az ugyanezen paraméterekkel rendelkező kivonatoló ellenőrzése, hogy a kibocsátó érvényes aláírási kulcs birtokában van. 

Az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek a Service Bus-erőforrás a teljes URI-címe. Például `http://<namespace>.servicebus.windows.net/<entityPath>` vagy `sb://<namespace>.servicebus.windows.net/<entityPath>`; Ez azt jelenti, hogy `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. Az URI-Azonosítónak kell lennie [százalék-kódolású](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Az entitás ezt az URI, vagy egyik szülőobjektumtól hierarchikus által az aláíráshoz használt megosztott hozzáférési engedélyezési szabály kell konfigurálni. Például `http://contoso.servicebus.windows.net/contosoTopics/T1` vagy `http://contoso.servicebus.windows.net` az előző példában.

SAS-token érvénytelen, az összes erőforrás előtagként a `<resourceURI>` szerepel a `signature-string`.

## <a name="regenerating-keys"></a>Kulcsok újragenerálása

A használt kulcsok rendszeres újragenerálása ajánlott a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum. Az elsődleges és másodlagos kulcsok tárhelyek léteznek, hogy a kulcsok fokozatosan forgassa el. Ha az alkalmazás általában az elsődleges kulcs, az elsődleges kulcs másolása a másodlagos kulcs tárolási helyre, és csak ezután generálni az elsődleges kulcsot. Az új elsődleges kulcs értéke majd konfigurálható úgy, hogy az ügyfélalkalmazások, amelyek továbbra is a régi elsődleges kulcsot használó másodlagos tárolóhelye hozzáférés be. Amennyiben az összes ügyfél frissítése, újragenerálás végül kivonja a régi elsődleges kulcsot a másodlagos kulcsot.

Ha tudja, vagy feltételezhető, hogy a kulcs biztonsága sérül, és, hogy a kulcsok visszavonása, újragenerálás mind a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) és a [másodlagos kulcs](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) , egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), felülírás új kulcsokkal. Ez az eljárás érvényteleníti összes jogkivonatot a régi kulccsal aláírva.

## <a name="shared-access-signature-authentication-with-service-bus"></a>A Service busszal megosztott hozzáférési aláírást hitelesítés

A leírása a következő esetek az engedélyezési szabályok konfigurálása, SAS-tokenje és ügyfél-hitelesítés létrehozását.

A teljes minta a Service Bus-alkalmazás, amely bemutatja a konfigurációs és a SAS engedélyezése, lásd: [közös hozzáférésű Jogosultságkód hitelesítés a Service busszal](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Kapcsolódó minta azt mutatja be a SAS-engedélyezési szabályok konfigurálva a névterek vagy témakörök biztonságossá tételéhez a Service Bus-előfizetések használatát érhető el itt: [használatával közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus előfizetések](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Egy entitás hozzáférés megosztott hozzáférés-engedélyezési szabályok

A Service Bus .NET-keretrendszer könyvtárak, hozzáférhet a [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) konfigurálva a Service Bus-üzenetsor vagy témakör keresztül objektum a [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) a megfelelő gyűjteményt [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

Alkalmazások az Azure .NET SDK használatával a Service Bus .NET-kódtárakra SAS engedélyezést használhatják a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) osztály. Az alábbi kód bemutatja a jogkivonat-szolgáltató egy Service Bus-üzenetsorba küldéséhez használatát. A látható itt, a korábban kiállított jogkivonat adja át a jogkivonat-szolgáltató gyári metódus használata helyett.

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

Más ügyfelek számára, hogy közvetlenül a jogkivonatok kiállítása is használhatja a jogkivonat-szolgáltató. 

Kapcsolati karakterláncok lehetnek egy nevének (*SharedAccessKeyName*) és a szabály kulcs (*SharedAccessKey*) vagy a korábban kiállított jogkivonat (*SharedAccessSignature*). Ha azok meg adva a kapcsolódási karakterláncban bármely konstruktor vagy egy kapcsolati karakterláncot elfogadása gyártómetódussal átadott, a biztonsági Társítások jogkivonat-szolgáltató az automatikusan létrehozott és feltöltve.

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

Ha a feladó, vagy az ügyfél egy SAS-jogkivonatot, nem rendelkeznek a kulcs közvetlenül, és azok nem fordított szerezheti be, hogy a kivonat. Hozzáférése van keresztül mi eléréséhez, és mennyi ideig. Egy fontos ne feledje, hogy az elsődleges kulcsot a házirendben módosítja, ha bármely megosztott hozzáférési aláírásokkal létre belőle érvénytelenné válnak.

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
> Fontos, hogy a kapcsolat létrejött-e a **SASL hitelesítési mechanizmus névtelen értékre beállított** (és nem az alapértelmezett egyszerű, ha már nincs szükség a SAS-jogkivonat küldéséhez használt felhasználónév és jelszó).
> 
> 

Ezután a közzétevő két AMQP hivatkozásokat hoz küldését a SAS-jogkivonatot, és kezelőtéglától érkezett a szolgáltatás a válasz (a token érvényességi eredmény).

Az AMQP üzenet tulajdonságait, és egy egyszerű üzenet több információt tartalmaz. A SAS-jogkivonat neve (a saját konstruktoraikban használatával) az üzenet törzsét. A **"ReplyTo"** tulajdonsága a csomópont neve (módosíthatja a nevét, és a szolgáltatás által dinamikusan létrejön) fogadó hivatkozásra az ellenőrzési eredmény fogadására. Az utolsó három alkalmazáshoz vagy egyéni tulajdonságok a szolgáltatást használják annak jelzésére, hogy milyen műveletet tartalmaz végrehajtásához. A CBS-től vázlat specifikáció szerint, fel kell a **műveletnév** ("put-token"), a **jogkivonattípusnak** (ebben az esetben egy `servicebus.windows.net:sastoken`), és a **"név" célközönség** , amelyhez a token (a teljes entitás) érvényes.

A közzétevő után küld a SAS-jogkivonat a küldő hivatkozásra, olvasási kell a válasz a fogadó hivatkozásra. A válasz az egy egyszerű AMQP üzenet és nevű alkalmazás tulajdonsága **"állapot-kód"** , amelyek a HTTP-állapotkódot konfigurációjával megegyező értékeket tartalmazhat.

## <a name="rights-required-for-service-bus-operations"></a>A Service Bus műveletekhez szükséges jogok

A következő táblázat a különféle műveletek a Service Bus erőforrásainak szükséges hozzáférési jogok.

| Művelet | Jogcím szükséges | Jogcím-hatókör |
| --- | --- | --- |
| **Namespace** | | |
| A névtér engedélyezési szabály konfigurálása |Felügyelet |Minden névtér cím |
| **Service Registry** | | |
| Magánfelhő házirendek felsorolása |Felügyelet |Minden névtér cím |
| A névtér figyelését |Figyelés |Minden névtér cím |
| Üzenetek küldése egy névtérben figyelő |Küldés |Minden névtér cím |
| **Várólista** | | |
| Üzenetsor létrehozása |Felügyelet |Minden névtér cím |
| Üzenetsor törlése |Felügyelet |Bármilyen érvényes várósor címe |
| A várólisták számbavétele |Felügyelet |$ Erőforrások/várólisták |
| A várólista leírás beolvasása |Felügyelet |Bármilyen érvényes várósor címe |
| A várólisták engedélyezési szabály konfigurálása |Felügyelet |Bármilyen érvényes várósor címe |
| Történő küldése az üzenetsorba |Küldés |Bármilyen érvényes várósor címe |
| Üzenetek fogadása egy üzenetsorból |Figyelés |Bármilyen érvényes várósor címe |
| Szakítsa vagy üzenetek befejezése után az üzenetet kapta a betekintés-zárolási mód |Figyelés |Bármilyen érvényes várósor címe |
| Késlelteti a későbbi beolvasásához üzenet |Figyelés |Bármilyen érvényes várósor címe |
| Kézbesítetlen levelek üzenet |Figyelés |Bármilyen érvényes várósor címe |
| Az állapot, egy üzenet-várólista munkamenethez társított beolvasása |Figyelés |Bármilyen érvényes várósor címe |
| Állítsa be a üzenet-várólista munkamenethez tartozó állapotát |Figyelés |Bármilyen érvényes várósor címe |
| Üzenet a későbbi kézbesítésre, ütemezés például [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Figyelés | Bármilyen érvényes várósor címe
| **A témakör** | | |
| Üzenettémakör létrehozása |Felügyelet |Minden névtér cím |
| Egy témakör törlése |Felügyelet |Bármilyen érvényes témakör cím |
| Témakörök számbavétele |Felügyelet |$ Erőforrások/kapcsolatos témakörök |
| A témakör leírás beolvasása |Felügyelet |Bármilyen érvényes témakör cím |
| A témakör az engedélyezési szabály konfigurálása |Felügyelet |Bármilyen érvényes témakör cím |
| A témakör küldése |Küldés |Bármilyen érvényes témakör cím |
| **Előfizetés** | | |
| Előfizetés létrehozása |Felügyelet |Minden névtér cím |
| Előfizetés törlése |Felügyelet |.. /myTopic/Subscriptions/mySubscription |
| Előfizetések számbavétele |Felügyelet |.. / myTopic/előfizetések |
| Előfizetés leírását beolvasása |Felügyelet |.. /myTopic/Subscriptions/mySubscription |
| Szakítsa vagy üzenetek befejezése után az üzenetet kapta a betekintés-zárolási mód |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Késlelteti a későbbi beolvasásához üzenet |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| Kézbesítetlen levelek üzenet |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| A témakör munkamenethez tartozó állapot beolvasása |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| A témakör munkamenethez tartozó állapot beállítása |Figyelés |.. /myTopic/Subscriptions/mySubscription |
| **Szabályok** | | |
| Szabály létrehozása |Felügyelet |.. /myTopic/Subscriptions/mySubscription |
| Szabály törlése |Felügyelet |.. /myTopic/Subscriptions/mySubscription |
| Szabályok számbavétele |Kezelése vagy figyelésére |.. /myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [How to use Service Bus Queues](service-bus-dotnet-get-started-with-queues.md) (A Service Bus-üzenetsorok használata)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com