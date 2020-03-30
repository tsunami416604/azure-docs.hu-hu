---
title: ASP.NET kimeneti gyorsítótárszolgáltatója a Redis azure-gyorsítótárához
description: Ismerje meg, hogyan gyorsítótárazhat ASP.NET lapkimenetet az Azure Cache for Redis használatával. A Redis kimeneti gyorsítótár-szolgáltató egy folyamaton kívüli tárolási mechanizmus a kimeneti gyorsítótár adataihoz.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: af003f1f0422c2351bcdf9b0c0010e38785c0344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530325"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET kimeneti gyorsítótárszolgáltatója a Redis azure-gyorsítótárához

A Redis kimeneti gyorsítótár-szolgáltató egy folyamaton kívüli tárolási mechanizmus a kimeneti gyorsítótár adataihoz. Ezek az adatok kifejezetten a teljes HTTP-válaszok (lap kimeneti gyorsítótárazás). A szolgáltató a 4.-ASP.NET bevezetett új kimeneti gyorsítótár-szolgáltató bővíthetőségi pontjához csatlakozik.

A Redis kimeneti gyorsítótár-szolgáltató használatához először konfigurálja a gyorsítótárat, majd konfigurálja a ASP.NET alkalmazást a Redis kimeneti gyorsítótárszolgáltató NuGet csomaghasználatával. Ez a témakör útmutatást nyújt az alkalmazás a Redis kimeneti gyorsítótárszolgáltató használatához való konfigurálásához. Az Azure Cache for Redis-példány létrehozásáról és konfigurálásáról további információt a Gyorsítótár létrehozása című témakörben [talál.](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET lapkimenet tárolása a gyorsítótárban

Ha egy ügyfélalkalmazást szeretne beállítani a Visual Studióban az Azure Cache for Redis Session State NuGet csomag használatával, kattintson az Eszközök menü **NuGet csomagkezelő**, **Csomagkezelő konzol** **parancsára.**

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

A Redis kimeneti gyorsítótárszolgáltató NuGet csomag függősége a StackExchange.Redis.StrongName csomag. Ha a StackExchange.Redis.StrongName csomag nem található a projektben, akkor telepítve van. A Redis kimeneti gyorsítótárszolgáltató NuGet csomagjáról a [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet oldalon talál további információt.

>[!NOTE]
>Az erős nevű StackExchange.Redis.StrongName csomag mellett a StackExchange.Redis nem erős nevű verziója is megjelenik. Ha a projekt a nem erős nevű StackExchange.Redis verziót használja, el kell távolítania azt; ellenkező esetben a projekt ben elnevezési ütközéseket tapasztal. Ezekről a csomagokról további információt a [.NET gyorsítótárazási ügyfelek konfigurálása című témakörben talál.](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)

A NuGet csomag letölti és hozzáadja a szükséges összeállítási hivatkozásokat, és hozzáadja a következő szakaszt a web.config fájlhoz. Ez a szakasz tartalmazza a szükséges konfigurációt a ASP.NET alkalmazás a Redis kimeneti gyorsítótárszolgáltató használatához.

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

Konfigurálja az attribútumokat a Microsoft Azure portalon a gyorsítótár-panelértékeivel, és szükség szerint konfigurálja a többi értéket. A gyorsítótár tulajdonságainak eléréséről az [Azure Cache beállítása a Redis-beállításokhoz című](cache-configure.md#configure-azure-cache-for-redis-settings)témakörben olvashat.

| Attribútum | Típus | Alapértelmezett | Leírás |
| --------- | ---- | ------- | ----------- |
| *Fogadó* | sztring | "localhost" | A Redis-kiszolgáló IP-címe vagy állomásneve |
| *port* | pozitív egész szám | 6379 (nem SSL)<br/>6380 (SSL) | Redis szerverport |
| *accessKey (accessKey)* | sztring | "" | Redis kiszolgáló jelszavát, ha a Redis-engedélyezés engedélyezve van. Az érték alapértelmezés szerint üres karakterlánc, ami azt jelenti, hogy a munkamenet-állapot szolgáltatója nem használ jelszót a Redis-kiszolgálóhoz való csatlakozáskor. **Ha a Redis-kiszolgáló egy nyilvánosan elérhető hálózat, például az Azure Redis cache, győződjön meg róla, hogy engedélyezi a Redis-engedélyezés a biztonság növelése érdekében, és biztonságos jelszót.** |
| *Ssl* | logikai | **Hamis** | A Redis-kiszolgálóhoz való csatlakozás ssl-en keresztül. Ez az érték alapértelmezés szerint **hamis,** mert a Redis nem támogatja az SSL-t a dobozból. **Ha az SSL-t támogató Azure Redis-gyorsítótárat használja, a biztonság növelése érdekében állítsa be ezt igaz értékre.**<br/><br/>A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg **a true** értéket ehhez a beállításhoz az SSL-port használatához. A nem SSL-port engedélyezéséről a [Gyorsítótár konfigurálása](cache-configure.md) témakör [Access Ports](cache-configure.md#access-ports) című szakaszában olvashat bővebben. |
| *databaseIdNumber* | pozitív egész szám | 0 | *Ez az attribútum csak a web.config vagy az AppSettings fájlon keresztül adható meg.*<br/><br/>Adja meg, hogy melyik Redis-adatbázist használja. |
| *connectionTimeoutEzmillimásodpercben* | pozitív egész szám | Által biztosított StackExchange.Redis | A *ConnectTimeout* beállítására szolgál a StackExchange.Redis.ConnectionMultiplexer létrehozásakor. |
| *operationTimeoutEzredmásodpercben* | pozitív egész szám | Által biztosított StackExchange.Redis | A *SyncTimeout* beállítására szolgál a StackExchange.Redis.ConnectionMultiplexer létrehozásakor. |
| *connectionString* (Érvényes StackExchange.Redis kapcsolati karakterlánc) | sztring | *N/a* | Vagy egy paraméterhivatkozás az AppSettings vagy a web.config fájlra, vagy pedig egy érvényes StackExchange.Redis kapcsolati karakterlánc. Ez az attribútum *állomás,* *port,* *accessKey*, *ssl*és más StackExchange.Redis attribútumok értékeit adhat meg. A *connectionString*című témakörben az [Attribute notes](#attribute-notes) szakasz [connectionString beállítása](#setting-connectionstring) című témakörben található. |
| *settingsClassName*<br/>*settingsMethodName* | sztring<br/>sztring | *N/a* | *Ezek az attribútumok csak a web.config vagy az AppSettings fájlon keresztül adhatók meg.*<br/><br/>Ezekkel az attribútumokkal kapcsolati karakterláncot adhat meg. *a settingsClassName* elemképminősített osztálynévnek kell lennie, amely a *settingsMethodName*.<br/><br/>Az *settingsMethodName* által megadott metódusnak nyilvánosnak, statikusnak és érvénytelennek kell lennie (nem kell paramétereket venni), **karakterlánctípussal.** Ez a módszer a tényleges kapcsolati karakterláncot adja vissza. |
| *naplózásOsztályneve*<br/>*loggingMetódusneve* | sztring<br/>sztring | *N/a* | *Ezek az attribútumok csak a web.config vagy az AppSettings fájlon keresztül adhatók meg.*<br/><br/>Ezekkel az attribútumokkal hibakeresés az alkalmazás azáltal, hogy naplók at session state/output cache együtt naplók StackExchange.Redis. *a(z) loggingClassName* elemező minősített osztálynévnek kell lennie, amely a *loggingMethodName*paraméterben megadott metódust tartalmazza.<br/><br/>A *loggingMethodName* paraméter által megadott metódusnak nyilvánosnak, statikusnak és érvénytelennek kell lennie (nem kell semmilyen paramétert figyelembe venni), **a System.IO.TextWriter**visszatérési típussal. |
| *alkalmazásnév* | sztring | Az aktuális folyamat modulneve vagy "/" | *Csak sessionStateprovider*<br/>*Ez az attribútum csak a web.config vagy az AppSettings fájlon keresztül adható meg.*<br/><br/>A Redis gyorsítótárban használandó alkalmazásnév-előtag. Az ügyfél használhatja ugyanazt a Redis cache különböző célokra. Annak biztosításához, hogy a munkamenetkulcsok ne ütközzenek, az alkalmazás nevével előtaggal rögzíthető. |
| *throwOnError* | logikai | igaz | *Csak sessionStateprovider*<br/>*Ez az attribútum csak a web.config vagy az AppSettings fájlon keresztül adható meg.*<br/><br/>Azt jelzi, hogy hiba esetén kivételt kell-e tenni.<br/><br/>A *throwOnError*hibáról az [Attribútummegjegyzések](#attribute-notes) szakasz [Megjegyzések a *hibáról* ](#notes-on-throwonerror) című témakörben található. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *újrapróbálkozási időoutEzredmásodpercben* | pozitív egész szám | 5000 | *Csak sessionStateprovider*<br/>*Ez az attribútum csak a web.config vagy az AppSettings fájlon keresztül adható meg.*<br/><br/>Mennyi ideig kell újrapróbálkozni, ha egy művelet sikertelen. Ha ez az érték kisebb, mint *operationTimeoutInMilliseconds*, a szolgáltató nem próbálja meg újra.<br/><br/>Az *újrapróbálkozási idő– Ezredmásodperc ben*további tudnivalókat az [Attribútumjegyzetek](#attribute-notes) szakasz Ban [található: "Az *újrapróbálkozási időről időpontban, ezössze00másodperc).* ](#notes-on-retrytimeoutinmilliseconds) |
| *redisSerializerType típus* | sztring | *N/a* | Megadja a Microsoft.Web.Redis programot megvalósító osztály szerelvényminősített típusnevét. ISerializer, és amely tartalmazza az egyéni logika szerializálására és deszerializálására az értékeket. További információ: [ *A redisSerializerType* ](#about-redisserializertype) az [Attribútummegjegyzések](#attribute-notes) szakaszban. |

## <a name="attribute-notes"></a>Attribútumjegyzetek

### <a name="setting-connectionstring"></a>*ConnectionString* beállítása

A *connectionString* értéke kulcsként szolgál a tényleges kapcsolati karakterlánc appsettings-ból történő lehívásához, ha ilyen karakterlánc létezik az AppSettings alkalmazásban. Ha nem található az AppSettings fájlban, a *connectionString* értéke lesz kulcsként a tényleges kapcsolati karakterlánc lekéréséhez a web.config **ConnectionString** szakaszból, ha az a szakasz létezik. Ha a kapcsolati karakterlánc nem létezik az AppSettings vagy a web.config **ConnectionString** szakaszban, a stackexchange.redis.connectionmultiplexer létrehozásakor a *connectionString* literális értéke lesz a kapcsolati karakterlánc.

Az alábbi példák bemutatják a *connectionString* használatának módját.

#### <a name="example-1"></a>1. példa

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

A `web.config`alkalmazásban a fenti kulcsot használja paraméterértékként a tényleges érték helyett.

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

A `web.config`alkalmazásban a fenti kulcsot használja paraméterértékként a tényleges érték helyett.

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

### <a name="notes-on-throwonerror"></a>Megjegyzések a *throwOnError-hoz*

Jelenleg, ha hiba történik egy munkamenet-művelet során, a munkamenet-állapot szolgáltató kivételt fog kidobni. Ez leállítja az alkalmazást.

Ez a viselkedés úgy módosult, hogy támogatja a meglévő ASP.NET munkamenet-állapot-szolgáltató felhasználóinak elvárásait, ugyanakkor lehetővé teszi a kivételek, ha szükséges. Az alapértelmezett viselkedés továbbra is kivételt okoz, ha hiba történik, összhangban más ASP.NET munkamenet-állapot szolgáltatók; a meglévő kódnak ugyanúgy kell működnie, mint korábban.

Ha a *throwOnError értéket* **hamisra**állítja, akkor hiba esetén kivétel takarása helyett csendben sikertelen lesz. Ha meg szeretné tudni, hogy hiba történt-e, és ha igen, ismerje meg, mi volt a kivétel, ellenőrizze a *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*statikus tulajdonságát.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Az *újrapróbálkozási időről ezredmásodpercben* írt megjegyzések

Ez néhány újrapróbálkozási logikát biztosít, hogy egyszerűsítse azt az esetet, amikor egyes munkamenet-műveleteknek újra meg kell próbálniuk a hálózati hiba miatt, miközben lehetővé teszi az újrapróbálkozási időtúltöltés szabályozását vagy az újrapróbálkozás teljes körű letiltását.

Ha *az újrapróbálkozási időszámozást* egy számra állítja be, például 2000-re, majd ha egy munkamenet-művelet sikertelen, 2000 ezredmásodpercre újra próbálkozik, mielőtt hibaként kezelne. Tehát, ha a munkamenet-állapot szolgáltató ja, hogy alkalmazza ezt az újrapróbálkozási logikát, csak konfigurálja az időtúltöltést. Az első újrapróbálkozás 20 ezredmásodperc után történik, ami a legtöbb esetben elegendő, ha hálózati hiba lép fel. Ezután minden másodpercben újra próbálkozik, amíg időtúlóráznak. Rögtön az időtúlután újra megpróbálja még egyszer, hogy megbizonyosodjon arról, hogy nem vágja le az időtúlot (legfeljebb) egy másodperccel.

Ha úgy gondolja, hogy nem kell újrapróbálkoznia (például amikor a Redis-kiszolgálót ugyanazon a gépen futtatja, mint az alkalmazást), vagy ha saját maga szeretné kezelni az újrapróbálkozási logikát, állítsa az *újrapróbálkozási időszint0-t* 0-ra.

### <a name="about-redisserializertype"></a>A *redisSerializerType-ról*

Alapértelmezés szerint a Redis-en lévő értékek tárolására szolgáló szerializálás a **BinaryFormatter** osztály által biztosított bináris formátumban történik. A *redisSerializerType használatával* adja meg a **Microsoft.Web.Redis.ISerializer** t implementáló osztály szerelvényminősített típusnevét, amely rendelkezik az értékek szerializálásához és deszerializálásához szükséges egyéni logikával. Például, itt van egy Json serializer osztály JSON.NET használatával:

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Feltéve, hogy ez az osztály **a MyCompanyDll**nevű kódösszeállításban van definiálva, beállíthatja a *redisSerializerType* paramétert a használatához:

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

## <a name="output-cache-directive"></a>Kimeneti gyorsítótár-irányelv

Adjon hozzá egy OutputCache-direktívát minden olyan laphoz, amelynek a kimenetét gyorsítótárazni kívánja.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Az előző példában a gyorsítótárban lévő oldaladatok 60 másodpercig maradnak a gyorsítótárban, és az egyes paraméterkombinációkhoz az oldal egy másik verziója kerül a gyorsítótárba. A OutputCache direktíváról [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)további információt a.

A lépések végrehajtása után az alkalmazás a Redis kimeneti gyorsítótárszolgáltató használatára van konfigurálva.

## <a name="third-party-output-cache-providers"></a>Külső kimeneti gyorsítótár-szolgáltatók

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>További lépések

Tekintse meg a [ASP.NET a Redis Azure-gyorsítótára munkamenet-állapotszolgáltatóját.](cache-aspnet-session-state-provider.md)
