---
title: A Redis az Azure Cache ASP.NET kimenetigyorsítótár-szolgáltatója
description: Ismerje meg, hogyan lehet ASP.NET lap kimenete a Azure Cache redis gyorsítótár
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943872"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>A Redis az Azure Cache ASP.NET kimenetigyorsítótár-szolgáltatója

A redis Cache kimeneti gyorsítótár-szolgáltatóját egy folyamaton tárolási mechanizmus kimeneti gyorsítótár adatainak. Ezeket az adatokat a kifejezetten a teljes HTTP-válaszok (kimeneti gyorsítótár oldalon). A szolgáltató rendkívüli az új kimeneti gyorsítótár szolgáltató indításkiterjesztési pont az ASP.NET 4 verzióban jelent meg.

A redis Cache kimeneti gyorsítótár-szolgáltató használatához először konfigurálja a gyorsítótárat, és konfigurálja az ASP.NET-alkalmazást, a redis Cache kimeneti gyorsítótár szolgáltató NuGet-csomag használatával. Ez a témakör útmutatást nyújt az alkalmazás használhatja a redis Cache kimeneti gyorsítótár-szolgáltató konfigurálásáról. Létrehozásával és egy Redis-példányt az Azure Cache konfigurálásával kapcsolatos további információkért lásd: [hozzon létre egy gyorsítótárat](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store ASP.NET lap kimenete a gyorsítótárban

Ügyfélalkalmazás konfigurálása az Azure Cache Redis munkamenet-állapot NuGet-csomag használatával a Visual studióban, kattintson a **NuGet-Csomagkezelő**, **Package Manager Console** származó a **eszközök**  menüben.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

A redis Cache kimeneti gyorsítótár szolgáltató NuGet-csomag maga a StackExchange.Redis.StrongName csomagot. Ha a StackExchange.Redis.StrongName csomag nem található a projekt, telepíti a rendszer. A redis Cache kimeneti gyorsítótár szolgáltató NuGet-csomaggal kapcsolatos további információkért lásd: a [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet-oldalon.

>[!NOTE]
>Az erős elnevezésű StackExchange.Redis.StrongName csomagon kívül van a StackExchange.Redis nem – erős elnevezésű verzióját. Ha a projekt StackExchange.Redis nem – erős elnevezésű verzióját használja, el kell távolítania azt. Ellenkező esetben a projekt mappaelnevezési ütközéseket fog tapasztalni. Ezek a csomagok kapcsolatos további információkért lásd: [gyorsítótárügyfelek konfigurálása .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

A NuGet-csomag letölti és hozzáadja a szükséges szerelvényhivatkozásokat, és hozzáadja az alábbi szakasz a web.config fájl. Ez a szakasz tartalmazza a szükséges konfigurációval, hogy az ASP.NET alkalmazás a redis Cache kimeneti gyorsítótár-szolgáltatóját használja.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Az attribútumok konfigurálása a Microsoft Azure Portalon a gyorsítótár panelről az értékekkel, és igény szerint állítsa be a más értékeket. Utasítások a fér hozzá a gyorsítótár tulajdonságai: [konfigurálása Azure Cache Redis beállítások](cache-configure.md#configure-azure-cache-for-redis-settings).

| Attribútum | Típus | Alapértelmezett | Leírás |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | A Redis kiszolgáló IP-cím vagy a gazdagép nevét |
| *port* | pozitív egész szám lehet | 6379 (non-SSL)<br/>6380 (SSL) | A redis-kiszolgáló portja |
| *accessKey* | string | "" | Ha engedélyezve van a Redis-engedélyezési a redis-kiszolgáló jelszavát. Az érték: üres karakterlánc, alapértelmezés szerint azt jelenti, hogy a munkamenetállapot-szolgáltatója nem használt jelszót, a Redis-kiszolgálóhoz való kapcsolódás során. **A nyilvánosan elérhető hálózat hasonlóan az Azure Redis Cache a Redis-kiszolgáló esetén ügyeljen arra, hogy a Redis engedélyezése a biztonság növelése érdekében, és adjon meg egy biztonságos jelszót.** |
| *ssl* | logikai | **false** | Kapcsolódás a Redis-kiszolgáló SSL-en keresztül kell-e. Ez az érték **hamis** alapértelmezés szerint, mert a Redis SSL nem támogatja a beépített. **Támogatja az SSL a beépített, ügyeljen arra, hogy ezt a TRUE állítja be az Azure Redis Cache használata a biztonság növelése.**<br/><br/>A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg **igaz** állítja ezt a beállítást, az SSL-port használatára. A nem SSL port engedélyezésével kapcsolatos további információkért lásd: a [hozzáférési portok](cache-configure.md#access-ports) című rész a [gyorsítótár konfigurálása](cache-configure.md) témakör. |
| *databaseIdNumber* | pozitív egész szám lehet | 0 | *Ez az attribútum csak a web.config vagy az AppSettings keresztül adható meg.*<br/><br/>Adja meg, melyik Redis használni kívánt adatbázist. |
| *connectionTimeoutInMilliseconds* | pozitív egész szám lehet | StackExchange.Redis által biztosított | Beállítására szolgáló *ConnectTimeout* StackExchange.Redis.ConnectionMultiplexer létrehozásakor. |
| *operationTimeoutInMilliseconds* | pozitív egész szám lehet | StackExchange.Redis által biztosított | Beállítására szolgáló *SyncTimeout* StackExchange.Redis.ConnectionMultiplexer létrehozásakor. |
| *connectionString* (StackExchange.Redis érvényes kapcsolati karakterlánc) | string | *n/a* | Mindkét paraméter hivatkozás AppSettings vagy web.config, vagy pedig egy érvényes StackExchange.Redis kapcsolati karakterláncot. Ez az attribútum megadhat értékeket a *gazdagép*, *port*, *accessKey*, *ssl*, és az egyéb StackExchange.Redis attribútumokat. Egy közelebb át azokat *connectionString*, lásd: [connectionString beállítás](#setting-connectionstring) a a [megjegyzések attribútum](#attribute-notes) szakaszban. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Ezek az attribútumok csak a web.config vagy az AppSettings keresztül adható meg.*<br/><br/>Ezek az attribútumok használatával adjon meg egy kapcsolati karakterláncot. *settingsClassName* kell lennie egy összeállításának minősített osztálynevét metoda zadaná daty tartalmazó *settingsMethodName*.<br/><br/>Metoda zadaná daty *settingsMethodName* kell lennie a nyilvános, statikus és void (nem használ paramétereket), a typ vrácené hodnoty **karakterlánc**. Ez a módszer a tényleges kapcsolati karakterláncot ad vissza. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Ezek az attribútumok csak a web.config vagy az AppSettings keresztül adható meg.*<br/><br/>Ezek az attribútumok használatával naplókat a munkamenet-állapot és a kimeneti gyorsítótárból, naplók és azáltal, hogy a StackExchange.Redis az alkalmazás hibakeresését. *loggingClassName* kell lennie egy összeállításának minősített osztálynevét metoda zadaná daty tartalmazó *loggingMethodName*.<br/><br/>Metoda zadaná daty *loggingMethodName* kell lennie a nyilvános, statikus és void (nem használ paramétereket), a typ vrácené hodnoty **System.IO.TextWriter**. |
| *applicationName* | string | A modul neve, az aktuális folyamat vagy "/" | *Csak SessionStateProvider*<br/>*Ez az attribútum csak a web.config vagy az AppSettings keresztül adható meg.*<br/><br/>Az alkalmazás használata Redis cache-ben előtagja. Az ügyfél a különböző célokra használjuk, az ugyanazon a Redis cache. Annak érdekében, hogy a nem ütköznek a munkamenetkulcsok, azt is a következő előtaggal az alkalmazás nevét. |
| *throwOnError* | logikai | true | *Csak SessionStateProvider*<br/>*Ez az attribútum csak a web.config vagy az AppSettings keresztül adható meg.*<br/><br/>E hiba esetén kivételt.<br/><br/>További információt a *throwOnError*, lásd: [a megjegyzések *throwOnError* ](#notes-on-throwonerror) a a [megjegyzések attribútum](#attribute-notes) szakaszban. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | pozitív egész szám lehet | 5000 | *Csak SessionStateProvider*<br/>*Ez az attribútum csak a web.config vagy az AppSettings keresztül adható meg.*<br/><br/>Mennyi időt kell újra, amikor egy művelet meghiúsul. Ha ez az érték kisebb, mint *operationTimeoutInMilliseconds*, a szolgáltató nem próbálkozik újra.<br/><br/>További információt a *retryTimeoutInMilliseconds*, lásd: [a megjegyzések *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) a a [megjegyzések attribútum](#attribute-notes) szakaszban. |
| *redisSerializerType* | string | *n/a* | Meghatározza a szerelvény típus nevét Microsoft.Web.Redis implementáló osztályt. ISerializer és, amely tartalmazza az egyéni logikát szerializálható és deszerializálható az értékeket. További információkért lásd: [kapcsolatos *redisSerializerType* ](#about-redisserializertype) a a [megjegyzések attribútum](#attribute-notes) szakaszban. |
|

## <a name="attribute-notes"></a>Attribútum megjegyzések

### <a name="setting-connectionstring"></a>Beállítás *kapcsolati Sztringje*

Az érték *connectionString* használatos kulcsként a tényleges kapcsolati karakterlánc beolvasása az AppSettings, ha már létezik ilyen egy karakterlánc AppSettings. Ha nem található az AppSettings, értékét belül *connectionString* tényleges kapcsolati karakterláncra beolvasni a Web.config fájl kulcsaként használt **ConnectionString** szakaszra, ha az adott szakaszt létezik. Ha a kapcsolati karakterlánc nem létezik az AppSettings vagy a Web.config fájl **ConnectionString** szakasz szövegkonstans értéke *connectionString* létrehozásakor a kapcsolati karakterlánc lesz StackExchange.Redis.ConnectionMultiplexer.

Az alábbi példák bemutatják, hogyan *connectionString* szolgál.

#### <a name="example-1"></a>1. példa

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

A `web.config`, tényleges érték helyett a paraméter értékeként fent kulcsot használja.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>2. példa

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

A `web.config`, tényleges érték helyett a paraméter értékeként fent kulcsot használja.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>3. példa

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>A megjegyzések *throwOnError*

Jelenleg Ha hiba történik, a munkamenet közben, a munkamenetállapot-szolgáltatóját kivételt. Ez az alkalmazás leáll.

Ez a viselkedés, amely támogatja a meglévő ASP.NET munkamenet állapota szolgáltató felhasználók elvárásainak is segíti az lehetővé teszi, hogy a kivételeket, ha megfelelő módon módosították. Alapértelmezés szerint továbbra is kivételt jelez, ha hiba történik, az egyéb ASP.NET munkamenet-állapot szolgáltatói; meglévő kódot a korábbiaknak megfelelően működnek.

Ha *throwOnError* való **hamis**, hívása kivétel, ha hiba történik, ahelyett hogy sikertelen lesz, csendes. Tekintse meg, ha hiba történt, és ha igen, Fedezze fel a kivétel történt, ellenőrizze a statikus tulajdonságot *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>A megjegyzések *retryTimeoutInMilliseconds*

Ez lehetővé teszi az egyes újrapróbálkozási logikát egyszerűsítése érdekében az esetet, ahol néhány művelet végrehajtásához meg kell ismételni hiba esetén miatt például hálózati hiba okozhatta, miközben is lehetővé teszi, hogy szabályozható, próbálkozzon újra, vagy teljesen tilthatják le az újra gombra.

Ha *retryTimeoutInMilliseconds* számot, például 2000, majd egy munkamenet-művelet sikertelen lesz, amikor meg fog próbálkozni a 2000 ezredmásodpercig előtt hibaként kezeli azt. Ezért ahhoz, hogy az újrapróbálkozási logika alkalmazására munkamenetállapot-szolgáltatóját, csak az időkorlát konfigurálása. Az első újrapróbálkozás után 20 ezredmásodperc, fog történni, azaz a legtöbb esetben elegendő a hálózati hiba okozhatta történik. Ezt követően próbálkozik újra másodpercenként, amíg azt az időkorlátot. Az időkorlát után győződjön meg arról, hogy azt nem elszigeteli a időkorlátja (legfeljebb) egy második, hogy még egyszer próbálkozik újra.

Ha nem úgy gondolja, hogy van szüksége (például, ha az alkalmazás ugyanazon a számítógépen futtatja a Redis-kiszolgáló) próbálkozzon újra, vagy ha szeretné kezelni az újrapróbálkozási logika saját maga is *retryTimeoutInMilliseconds* 0-ra.

### <a name="about-redisserializertype"></a>Kapcsolatos *redisSerializerType*

Alapértelmezés szerint a szerializálási értékeket tárolja a redis által biztosított bináris formátumban történik a **BinaryFormatter** osztály. Használat *redisSerializerType* egy osztály, amely megvalósítja a szerelvény típus nevének megadásához **Microsoft.Web.Redis.ISerializer** , és az egyéni logikát szerializálható és deszerializálható az értékeket. Ha például a következő egy Json-diagramszerializáló osztály JSON.NET használatával:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Feltéve, hogy ez az osztály definiálva van egy szerelvény neve **MyCompanyDll**, beállíthatja a paramétert *redisSerializerType* vele:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Kimeneti gyorsítótár irányelv

Adjon hozzá egy OutputCache irányelv minden olyan oldalhoz, amelyhez szeretné, a kimeneti gyorsítótárban.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Az előző példában az oldal gyorsítótárazott adatokat a gyorsítótárban marad 60 másodpercig, és a egy másik verziót az oldal az egyes paraméterkombináció van gyorsítótárazva. Direktiva OutputCache kapcsolatos további információkért lásd: [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Ezek a lépések elvégzése után az alkalmazás a redis Cache kimeneti gyorsítótár-szolgáltató használatára van konfigurálva.

## <a name="next-steps"></a>További lépések

Tekintse meg a [ASP.NET munkamenetállapot-szolgáltatóját Azure Cache redis](cache-aspnet-session-state-provider.md).
