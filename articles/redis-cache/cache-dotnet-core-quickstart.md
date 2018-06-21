---
title: Rövid útmutató az Azure Redis Cache .NET Core-alkalmazásokkal történő használatához | Microsoft Docs
description: Ez a rövid útmutató azt ismerteti, hogyan érheti el az Azure Redis Cache-t a .NET Core-alkalmazásokban
services: redis-cache,app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 63bab0e5c77204ea1e122c32a508e31ed9bcd114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660537"
---
# <a name="quickstart-use-azure-redis-cache-with-a-net-core-app"></a>Rövid útmutató: Az Azure Redis Cache használata .NET Core-alkalmazásokkal



Ez a rövid útmutató a Microsoft Azure Redis Cache .NET Core-ral való használatának első lépéseit mutatja be. A Microsoft Azure Redis Cache a népszerű, nyílt forráskódú Redis Cache-re épül. Hozzáférést biztosít egy biztonságos, dedikált Redis Cache gyorsítótárhoz, amelyet a Microsoft felügyel. Az Azure Redis Cache használatával létrehozott gyorsítótárak a Microsoft Azure összes alkalmazásából elérhetőek.

Ebben a rövid útmutatóban a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) ügyfelet fogja használni C\#-kóddal a konzolalkalmazásban. Létre fog hozni egy gyorsítótárat, és konfigurálni fogja a .NET Core-ügyfélalkalmazást. Ezután objektumokat fog hozzáadni, és frissíteni fogja a gyorsítótárban található objektumokat. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

![Kész konzolalkalmazás](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [.Net SDK 2.0](https://www.microsoft.com/net/learn/get-started/windows) vagy újabb.
* A StackExchange.Redis ügyfél használatához a [.NET-keretrendszer 4-es vagy újabb verziója](https://www.microsoft.com/net/download/dotnet-framework-runtime) szükséges.

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Jegyezze fel a **GAZDAGÉP NEVÉT** és az **Elsődleges** hozzáférési kulcsot. Ezeket az értékeket később a *CacheConnection* titkos kódjának létrehozásához fogja használni.



## <a name="create-a-console-app"></a>Konzolalkalmazás létrehozása

Nyisson meg egy új parancsablakot, és a következő parancs végrehajtásával hozzon létre egy új .NET Core-konzolalkalmazást:

```
dotnet new console -o Redistest
```

A parancsablakban váltson az új *Redistest* projektkönyvtárra.



## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban hozzáadja a [Secret Manager eszközt](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

Nyissa meg a *Redistest.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Adjon hozzá egy `UserSecretsId` elemet is az alábbiakban látható módon, és mentse a fájlt.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
    <UserSecretsId>Redistest</UserSecretsId>
  </PropertyGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
  </ItemGroup>
</Project>
```

A következő parancs végrehajtásával adja hozzá a *Microsoft.Extensions.Configuration.UserSecrets* csomagot a projekthez:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

A csomagok visszaállításához hajtsa végre az alábbi parancsot:

```
dotnet restore
```

A parancsablakban futtassa a következő parancsot az új, *CacheConnection* nevű titkos kód tárolásához, miután kicserélte a helyőrzőket (a csúcsos zárójelekkel együtt) a saját gyorsítótárának nevére és elsődleges hozzáférési kulcsára:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<primary-access-key>"
```

Adja hozzá a következő `using` utasítást a *Program.cs* fájlhoz:

```csharp
using Microsoft.Extensions.Configuration;
```

Adja hozzá a következő tagokat a `Program` osztályhoz a *Program.cs* fájlban. Ez a kód inicializál egy konfigurációt, amellyel elérhető a Redis Cache kapcsolati sztringjének felhasználói titkos kódja.

```csharp
        private static IConfigurationRoot Configuration { get; set; }
        const string SecretName = "CacheConnection";

        private static void InitializeConfiguration()
        {
            var builder = new ConfigurationBuilder()
                .AddUserSecrets<Program>();

            Configuration = builder.Build();
        }
```

## <a name="configure-the-cache-client"></a>A gyorsítótárügyfél konfigurálása

Ebben a szakaszban konfigurálja a konzolalkalmazást a .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) ügyfél használatára.

A parancsablakban hajtsa végre a következő parancsot a *Redistest* projektkönyvtárban:

```
dotnet add package StackExchange.Redis
```

A telepítés befejezése után a *StackExchange.Redis* gyorsítótárügyfél már használható a projekttel.


## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz

Adja hozzá a következő `using` utasítást a *Program.cs* fájlhoz:

```csharp
using StackExchange.Redis;
```

Az Azure Redis Cache-sel való kapcsolatot a `ConnectionMultiplexer` osztály kezeli. Ennek az osztálynak megoszthatónak és ismét felhasználhatónak kell lennie az ügyfélalkalmazásban. Ne hozzon létre új kapcsolatot minden művelethez. 

A *Program.cs* fájlban adja hozzá a következő tagokat a konzolalkalmazás `Program` osztályához:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = Configuration[SecretName];
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
```

A `ConnectionMultiplexer` példány alkalmazásban való megosztásának ez a módszere egy statikus tulajdonságot használ, amely egy csatlakoztatott példányt ad vissza. A kód egy szálbiztos módszert biztosít egyetlen csatlakoztatott `ConnectionMultiplexer`-példány inicializálásához. Az `abortConnect` értéke false, ami azt jelenti, hogy a hívás akkor is sikeres lesz, ha nincs kapcsolat az Azure Redis Cache-sel. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

A *CacheConnection* titkos kód értékéhez a Secret Manager konfigurációszolgáltatóval lehet hozzáférni, és jelszóparaméterként használható.

## <a name="executing-cache-commands"></a>Gyorsítótárparancsok végrehajtása

A *Program.cs* fájlban adja hozzá a következő kódot a konzolalkalmazás `Program` osztályának `Main` eljárásához:

```csharp
        static void Main(string[] args)
        {
            InitializeConfiguration();

            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            string cacheCommand = "PING";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

            // Simple get and put of integral data types into the cache
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

            // Demostrate "SET Message" executed as expected...
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            // Get the client list, useful to see if connection list is growing...
            cacheCommand = "CLIENT LIST";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

            lazyConnection.Value.Dispose();
        }
```

Mentse a *Program.cs* fájlt.

Az Azure Redis Cache-ek adatbázisainak száma konfigurálható (alapértelmezés szerint 16). Ezek az adatbázisok a Redis Cache-ben található adatok logikai szétválasztására használhatóak. A kód az alapértelmezett adatbázishoz csatlakozik (DB 0). További információ: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration).

A gyorsítótárelemek a `StringSet` és a `StringGet` metódussal tárolhatók és kérhetők le.

A Redis a legtöbb adatot Redis-sztringekként tárolja, azonban ezek a sztringek több adattípust is tartalmazhatnak, például szerializált bináris adatokat, amelyek akkor használhatók, ha .NET-objektumokat tárol a gyorsítótárban.

Hajtsa végre a következő parancsot a parancsablakban az alkalmazás létrehozásához:

```
dotnet build
```

Ezután futtassa az alkalmazást a következő paranccsal:

```
dotnet run
```

Az alábbi példában a `Message` kulcsot láthatja. A kulcsnak korábban gyorsítótárazott értéke volt, amely az Azure Portalon, a Redis Console használatával lett beállítva. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

![Részleges konzolalkalmazás](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>.NET-objektumok használata a gyorsítótárban

Az Azure Redis Cache .NET-objektumokat és primitív adattípusokat is képes gyorsítótárazni, de a .NET-objektumokat a gyorsítótárazásuk előtt szerializálni kell. Ez a .NET-objektumszerializálás az alkalmazásfejlesztők feladata, akik így rugalmasan kiválaszthatják a szerializálót.

Az objektumok szerializálásának egy egyszerű módja, ha a [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) kódtárban található `JsonConvert` szerializálási metódusokat használja a JSON formátumból és a JSON formátumba szerializálásra. Ebben a szakaszban egy .NET-objektumot fog hozzáadni a gyorsítótárhoz.

A következő parancs végrehajtásával adja hozzá az alkalmazáshoz a *Newtonsoft.json* csomagot:

```
dotnet add package Newtonsoft.json
```

Adja hozzá a következő `using` utasítást a *Program.cs* fájl elejéhez:

```charp
using Newtonsoft.Json;
```

Adja hozzá a következő `Employee` osztálydefiníciót a *Program.cs* fájlhoz:

```csharp
        class Employee
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }

            public Employee(string EmployeeId, string Name, int Age)
            {
                this.Id = EmployeeId;
                this.Name = Name;
                this.Age = Age;
            }
        }
```

Másolja és illessze be a következő kódsorokat a *Program.cs* fájl `Main()` eljárásának végére, a `Dispose()` hívása elé, egy szerializált .NET-objektum gyorsítótárazásához és lekéréséhez:

```csharp
            // Store .NET object to cache
            Employee e007 = new Employee("007", "Davide Columbo", 100);
            Console.WriteLine("Cache response from storing Employee .NET object : " + 
                cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

            // Retrieve .NET object from cache
            Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
            Console.WriteLine("Deserialized Employee .NET object :\n");
            Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
            Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
            Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Mentse a *Program.cs* fájlt, és hozza létre újra az alkalmazást a következő paranccsal:

```
dotnet build
```

Futtassa az alkalmazást a következő paranccsal a .NET-objektumok szerializálásának teszteléséhez:

```
dotnet run
```

![Kész konzolalkalmazás](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

![Törlés](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.



<a name="next-steps"></a>

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja az Azure Redis Cache-t egy .NET Core-alkalmazásból. Folytassa a következő rövid útmutatóval a Redis Cache ASP.NET-webalkalmazással történő használatához.

> [!div class="nextstepaction"]
> [Egy Azure Redis Cache-t használó ASP.NET-webalkalmazás létrehozása](./cache-web-app-howto.md)




