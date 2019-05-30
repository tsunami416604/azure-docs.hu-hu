---
title: Az Azure Alkalmazáskonfiguráció rugalmasság és a vész helyreállítási |} A Microsoft Docs
description: Rugalmasság és a katasztrófa utáni helyreállítás az Azure-alkalmazások konfigurálása megvalósításának áttekintése.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394026"
---
# <a name="resiliency-and-disaster-recovery"></a>Rugalmasság és vészhelyreállítás

Alkalmazások konfigurálása az Azure jelenleg egy regionális szolgáltatás. Minden egyes konfigurációs tár egy adott Azure-régióban jön létre. Egy régióra kiterjedő szolgáltatáskimaradás hatással lesz az összes üzlet, az adott régióban. Alkalmazások konfigurálása az automatikus feladatátvételt egy másik régióban nem kínál. Ez a cikk nyújt általános útmutatást használatát több konfigurációs tároló Azure-régióban a geo-ellenálló-képesség az alkalmazás növelésére.

## <a name="high-availability-architecture"></a>Magas rendelkezésre állású architektúra

Vegye figyelembe a régiók közötti redundancia, szüksége különböző régiókban lévő több alkalmazás konfigurációs tároló létrehozásához. Ezzel a beállítással az alkalmazás lesz legalább egy további konfigurációs adattároló tud visszatérni az alakzatot a elsődleges áruházhoz elérhetetlenné válik. Az alábbi, egy diagram, amely az alkalmazás és az elsődleges és másodlagos configuration adattárak közötti topológiája.

![Georedundáns tárolók](./media/geo-redundant-app-configuration-stores.png)

Az alkalmazás mind az elsődleges és másodlagos tárolt párhuzamosan fog betölteni annak konfigurációját. Ez növeli az esélye, hogy sikeresen első jelentősen a konfigurációs adatokat. Ön felelős a egyaránt az áruházakban az adatok szinkronban tartja. Az alábbi szakaszok azt ismertetik, hogyan hozhat létre geo-rugalmasság az alkalmazásba.

## <a name="failover-between-configuration-stores"></a>Feladatátvétel konfigurációs adattárak között

Technikailag az alkalmazás nem végrehajtásakor a feladatátvételt. Egyidejűleg ugyanazokat a konfigurációs adatok lekérését két konfigurációs alkalmazásáruházak megpróbálja. A kód kell rendezése, úgy, hogy betölti először a másodlagos áruházból először, és ezután az elsődleges tárolja. Ez a megközelítés biztosítja, hogy a konfigurációs adatait az elsődleges tároló elsőbbséget, ha azok elérhetők. Az alábbi kódrészlet bemutatja, hogyan valósíthat meg ezt a .NET Core.

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
    }
```

Figyelje meg a `optional` átadott paraméter a `AddAzureAppConfiguration` függvény. Ha a beállítása `true`, ez a paraméter megakadályozza, hogy az alkalmazás hibás továbbra is, ha a függvény nem tudja betölteni a konfigurációs adatokat.

## <a name="synchronization-between-configuration-stores"></a>Szinkronizálási konfiguráció adattárak között

Fontos, hogy az összes georedundáns konfigurációs tárolók ugyanazokat az adatokat. Használhatja a **exportálása** függvényt az alkalmazás konfigurációs adatokat az elsődleges tároló átmásolása a másodlagos igény szerinti. Ez a funkció az Azure portal és a parancssori felület érhető el.

Az Azure Portalról akkor is küldjön egy módosítást egy másik a konfigurációs adattárolónál az alábbi lépéseket:

1. Navigáljon a **Import/Export** lapon jelölje be **exportálása**, jelölje be **Alkalmazáskonfiguráció** , a **cél** szolgáltatást, kattintson a  **Válasszon ki egy erőforrást**.

2. Az új panel, amely megnyitva a beállítása, adja meg az előfizetés, erőforráscsoport és a másodlagos tároló erőforrás neve, majd kattintson **alkalmaz**.

3. A felhasználói felület is frissülnek, így választhat, hogy milyen konfigurációs adatokat szeretne exportálni a másodlagos tárolójában. Hagyhatja az alapértelmezett idő értéket, és állítsa mind **címkéről** , az **címke** ugyanarra az értékre. Kattintson az **Alkalmaz** gombra.

4. Ismételje meg a fenti lépéseket az összes konfigurációs módosítást.

Automatizálhatja a folyamatot az Azure CLI használatával exportálása. Az alábbi parancsot az elsődleges tár egyetlen konfigurációmódosítás exportálása a másodlagos mutatja be.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan, mivel megvédi a rugalmasság geo-futtatás ideje alatt az Alkalmazáskonfigurációhoz alkalmazását. Azt is megteheti build és a központi telepítési időpontban Alkalmazáskonfiguráció konfigurációs adatait is beágyazható. További információkért lásd: [integrálás a CI/CD-folyamat](./integrate-ci-cd-pipeline.md).

