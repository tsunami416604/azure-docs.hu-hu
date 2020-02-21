---
title: Az Azure-alkalmazások konfigurációjának rugalmassága és a vész-helyreállítás
description: A rugalmasság és a vész-helyreállítás megvalósítása az Azure-alkalmazások konfigurációjával.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523764"
---
# <a name="resiliency-and-disaster-recovery"></a>Rugalmasság és vészhelyreállítás

Az Azure app Configuration jelenleg egy regionális szolgáltatás. Az egyes konfigurációs tárolók egy adott Azure-régióban jönnek létre. Az egész régióra kiterjedő leállás hatással van az adott régióban lévő összes tárolóra. Az alkalmazás konfigurációja nem biztosít automatikus feladatátvételt egy másik régióba. Ez a cikk általános útmutatást nyújt arról, hogy miként használható több konfigurációs tároló az Azure-régiókban az alkalmazás földrajzi rugalmasságának növelésére.

## <a name="high-availability-architecture"></a>Magas rendelkezésre állású architektúra

A régiók közötti redundancia megvalósításához több alkalmazás-konfigurációs tárolót kell létrehoznia különböző régiókban. Ezzel a beállítással az alkalmazásnak legalább egy további konfigurációs tárolóval kell visszaesnie, ha az elsődleges tároló elérhetetlenné válik. Az alábbi ábra az alkalmazás és az elsődleges és másodlagos konfigurációs tárolók közötti topológiát szemlélteti:

![Geo-redundáns tárolók](./media/geo-redundant-app-configuration-stores.png)

Az alkalmazás az elsődleges és a másodlagos tárolóból is betölti a konfigurációját párhuzamosan. Ez növeli a konfigurációs adatmennyiség sikeres beolvasásának esélyét. Ön felelős az adattárakban tárolt Adattárolásért. A következő szakaszokból megtudhatja, hogyan hozhat létre geo-rugalmasságot az alkalmazásba.

## <a name="failover-between-configuration-stores"></a>A konfigurációs tárolók közötti feladatátvétel

Technikailag az alkalmazás nem hajt végre feladatátvételt. Ugyanazon konfigurációs adatok egyidejű lekérését kísérli meg egyszerre két alkalmazás-konfigurációs tárolóból. Rendezze a kódot úgy, hogy az a másodlagos tárolóból, majd az elsődleges tárolóból betöltődik. Ez a megközelítés biztosítja, hogy az elsődleges tárolóban lévő konfigurációs adatközpont elsőbbséget élvez, ha elérhető. A következő kódrészletből megtudhatja, hogyan implementálhatja ezt a megállapodást a .NET Core-ban:

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Figyelje meg a `AddAzureAppConfiguration` függvénynek átadott `optional` paramétert. Ha `true`értékre van állítva, ez a paraméter megakadályozza, hogy az alkalmazás folytassa a műveletet, ha a függvény nem tudja betölteni a konfigurációs adatait.

## <a name="synchronization-between-configuration-stores"></a>A konfigurációs tárolók közötti szinkronizálás

Fontos, hogy a Geo-redundáns konfiguráció tárolja az összes azonos adathalmazt. Az alkalmazás konfigurációja az **Exportálás** funkcióval az adatok az elsődleges tárolóból a másodlagos igény szerinti másolására használhatók. Ez a függvény a Azure Portal és a parancssori felületen egyaránt elérhető.

A Azure Portal az alábbi lépéseket követve elküldheti egy másik konfigurációs tároló módosítását.

1. Nyissa meg az **Importálás/exportálás** lapot, majd válassza az **Exportálás** > az **alkalmazás konfigurációja** > a **cél** > **válasszon ki egy erőforrást**.

1. A megnyíló új panelen adja meg a másodlagos tároló előfizetés, erőforráscsoport és erőforrás nevét, majd kattintson az **alkalmaz**gombra.

1. A felhasználói felület frissül, így kiválaszthatja, hogy milyen konfigurációs adatait szeretné exportálni a másodlagos tárolóba. Az alapértelmezett időértéket meghagyhatja, és a **címkéből** és a **címkéből** is megadhatja ugyanazt az értéket. Kattintson az **Alkalmaz** gombra.

1. Ismételje meg az előző lépéseket az összes konfigurációs módosításnál.

Az exportálási folyamat automatizálásához használja az Azure CLI-t. A következő parancs bemutatja, hogyan exportálhat egyetlen konfigurációs változást az elsődleges tárolóból a másodlagosra:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan növelhető az alkalmazás a földrajzi rugalmasság eléréséhez az alkalmazás konfigurálásakor. A konfigurációs adatok beágyazási vagy központi telepítési idő alatt is beágyazható az alkalmazás konfigurációjától. További információ: [integrálás CI/CD-folyamattal](./integrate-ci-cd-pipeline.md).
