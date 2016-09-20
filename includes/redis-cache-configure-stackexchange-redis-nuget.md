A .NET-alkalmazások képesek használni a **StackExchange.Redis** gyorsítótárügyfelet, amely a Visual Studióban konfigurálható a gyorsítótár-ügyfélalkalmazások konfigurálását leegyszerűsítő NuGet-csomagokkal. 

>[AZURE.NOTE] További információkat a [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) github-oldalon és a [StackExchange.Redis gyorsítótárügyfél dokumentációjában](http://github.com/StackExchange/StackExchange.Redis#documentation) talál.

Egy ügyfélalkalmazás a Visual Studióban a StackExchange.Redis NuGet-csomag használatával történő konfigurálásához kattintson a jobb gombbal a projektre a **Solution Explorer** (Megoldáskezelő) felületén, majd válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. 

![NuGet-csomagok kezelése](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Írja be a **StackExchange.Redis** vagy a **StackExchange.Redis.StrongName** kifejezést a keresőmezőbe, az eredmények közül válassza ki a kívánt verziót, majd kattintson az **Install** (Telepítés) gombra.

>[AZURE.NOTE] Ha inkább a **StackExchange.Redis** ügyfélkönyvtár erős elnevezésű verzióját kívánja használni, válassza a **StackExchange.Redis.StrongName**, ellenkező esetben pedig a **StackExchange.Redis** lehetőséget.

![StackExchange.Redis NuGet-csomag](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

A NuGet-csomag letölti és hozzáadja az ügyfélalkalmazás számára szükséges szerelvényhivatkozásokat az Azure Redis Cache a StackExchange.Redis gyorsítótárügyféllel történő eléréséhez.

>[AZURE.NOTE] Ha a projektet korábban a StackExchange.Redis használatára konfigurálta, a **NuGet-csomagkezelőben** ellenőrizheti, hogy elérhető-e új frissítés a csomaghoz. A StackExchange.Redis NuGet-csomag frissített verzióit a **NuGet-csomagkezelő** ablakában az **Updates** (Frissítések) elemre kattintva érheti el és telepítheti. Ha a StackExchange.Redis NuGet-csomaghoz elérhetővé válik egy frissítés, frissítheti a projektjét is, hogy az a csomag frissített verzióját használja.




<!--HONumber=sep16_HO1-->


