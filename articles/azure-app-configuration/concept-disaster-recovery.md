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
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706510"
---
# <a name="resiliency-and-disaster-recovery"></a>Rugalmasság és vészhelyreállítás

Alkalmazások konfigurálása az Azure jelenleg egy regionális szolgáltatás. Minden egyes konfigurációs tár egy adott Azure-régióban jön létre. Egy régióra kiterjedő szolgáltatáskimaradás hatással van minden, az adott régióban tárolja. Alkalmazás konfigurációja nem biztosít automatikus feladatátvételt egy másik régióba. Ez a cikk nyújt általános útmutatást használatát több konfigurációs tároló Azure-régióban a geo-ellenálló-képesség az alkalmazás növelésére.

## <a name="high-availability-architecture"></a>Magas rendelkezésre állású architektúrával

Vegye figyelembe a régiók közötti redundancia, szüksége különböző régiókban lévő több alkalmazás konfigurációs tároló létrehozásához. Ezzel a beállítással az alkalmazás rendelkezik legalább egy további konfigurációs adattároló tud visszatérni, ha az elsődleges tároló elérhetetlenné válik. A következő ábra szemlélteti a topológia az alkalmazás és az elsődleges és másodlagos configuration tárolók között:

![Georedundáns tárolók](./media/geo-redundant-app-configuration-stores.png)

Az alkalmazás mind az elsődleges és másodlagos tárolt párhuzamosan tölt be a konfigurációját. Ez növeli az esélye, hogy a konfigurációs adatok sikeresen első. Ön felelős a egyaránt az áruházakban az adatok szinkronban tartja. Az alábbi szakaszok azt ismertetik, hogyan hozhat létre geo-rugalmasság az alkalmazásba.

## <a name="failover-between-configuration-stores"></a>Feladatátvétel konfigurációs adattárak között

Az alkalmazás technikailag nem végrehajtásakor a feladatátvételt. Ugyanazokat a konfigurációs adatokat beolvasni a két alkalmazás konfigurációját áruházakból származó egyszerre megpróbálja. Rendezze el úgy a kódot, hogy előbb betölti a másodlagos tárolóból, és ezután az elsődleges tárolja. Ez a megközelítés biztosítja, hogy a konfigurációs adatait az elsődleges tároló lép érvénybe, amikor érhető el. A következő kódrészlet azt mutatja be, hogyan implementálható ezzel az elrendezéssel fokozott a .NET Core-CLI-ben:

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

Fontos, hogy az összes georedundáns konfigurációs tárolók ugyanazokat az adatokat. Használhatja a **exportálása** alkalmazások konfigurálása az elsődleges tároló másolhat adatokat a másodlagos igény szerinti függvényt. Ez a funkció az Azure portal és a parancssori felület keresztül érhető el.

Az Azure Portalról akkor is küldjön egy módosítást egy másik a konfigurációs adattárolónál az alábbi lépéseket.

1. Nyissa meg a **Import/Export** lapot, majd **exportálása** > **Alkalmazáskonfiguráció** > **cél**  >  **Válasszon ki egy erőforrást**.

2. A megnyíló új panelen adja meg az előfizetés, erőforráscsoport és a másodlagos tároló erőforrás neve, és válassza ki **alkalmaz**.

3. A felhasználói felület frissül, így választhat, hogy milyen konfigurációs adatokat szeretne exportálni a másodlagos tárolójában. Hagyhatja az alapértelmezett idő értéket, és állítsa mind **címkéről** és **címkére** ugyanarra az értékre. Kattintson az **Alkalmaz** gombra.

4. Ismételje meg az előző lépést az összes konfigurációs módosítást.

Ez az exportálási folyamat automatizálása, az Azure CLI használatával. A következő parancsot az elsődleges tár egyetlen konfigurációmódosítás exportálása a másodlagos mutatja be:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan, mivel megvédi a rugalmasság geo-futtatás ideje alatt az Alkalmazáskonfigurációhoz alkalmazását. Konfigurációs adatok konfigurálása a build és a központi telepítési időpontban is beágyazható. További információkért lásd: [integrálás a CI/CD-folyamat](./integrate-ci-cd-pipeline.md).

