---
title: Azure Service Fabric egy létező végrehajtható fájl központi telepítése |} Microsoft Docs
description: További információk a csomagolására vendégként végrehajtható, egy meglévő alkalmazást, a Service Fabric-fürt telepíthető.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: mfussell
ms.openlocfilehash: cdaf3dae12c2c9da1f6bcbebbff560b98e62bade
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>A Service Fabric egy létező végrehajtható fájl központi telepítése
Az Azure Service Fabric szolgáltatásként futtatható kódok, például a Node.js, Java vagy C++ bármilyen típusú. A Service Fabric Vendég végrehajtható fájlok szolgáltatások az ilyen típusú néven hivatkozik.

Vendég végrehajtható fájlok állapotmentes szolgáltatásokhoz hasonlóan a Service Fabric által kell kezelni. Emiatt a fürt rendelkezésre állási és más metrikák alapján csomópontján kerülnek. Ez a cikk ismerteti, hogyan csomag és a Vendég végrehajtható telepítése a Service Fabric-fürt Visual Studio vagy a parancssori eszköz használatával.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>A Vendég a Service Fabric végrehajtható előnyei
Egy Vendég végrehajtható, a Service Fabric-fürt futtatására számos előnye van:

* Magas rendelkezésre állás. A Service Fabric futó alkalmazások vannak magas rendelkezésre állású. A Service Fabric biztosítja, hogy futnak-e egy alkalmazás példányai.
* Állapotfigyelés. Service Fabric állapotfigyelésének észleli, ha egy alkalmazás fut, és diagnosztikai információkat nyújt, ha hiba történik.   
* Alkalmazás-életciklus kezelésének. Mellett frissítéseket biztosító állásidő nélkül, a Service Fabric automatikus visszaállítása az előző verzió biztosít, ha egy frissítés során rossz állapotesemény.    
* Sűrűség. A fürt, így nem kell saját hardveren futó minden alkalmazás több alkalmazást is futtathatja.
* Felderíthetőség: Használó többi hívása a Service Fabric-szolgáltatás más szolgáltatások a fürtben található. 

## <a name="samples"></a>Példák
* [Minta csomagolás és központi telepítése egy Vendég végrehajtható fájl](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) kapcsolaton keresztül kommunikáljon a Naming szolgáltatás REST használatával](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Alkalmazás és szolgáltatás jegyzékfájlt áttekintése
Központi telepítése egy Vendég végrehajtható részeként célszerű a Service Fabric csomagolás és a telepítési modell megérteni a [alkalmazásmodell](service-fabric-application-model.md). A Service Fabric-csomagban modell támaszkodik két XML-fájlokat: az alkalmazás és szolgáltatás jegyzékfájljai. A sémadefiníciót a ApplicationManifest.xml és ServiceManifest.xml fájlok telepítve van a Service Fabric SDK az *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Az alkalmazásjegyzék** az alkalmazás jegyzékében az alkalmazás leírására használatos. Felsorolja az azt alkotó szolgáltatásokat, és más paramétereket, illetve hogyan egy vagy több szolgáltatás meghatározásához kell telepíteni, például a példányok száma.

  A Service Fabric egy alkalmazás központi telepítése és frissítése munkaegység. Ahol lehetséges hibák és a potenciális visszagörgetése felügyelete egyetlen egységként alkalmazás frissítése. A Service Fabric biztosítja, hogy a frissítési folyamat vagy sikeres, vagy ha a frissítés sikertelen lesz, ne hagyja el az alkalmazás ismeretlen vagy instabil állapotban.
* **Szolgáltatás jegyzékfájl** a szolgáltatás jegyzékfájl szolgáltatás összetevőit mutatja be. Ez magában foglalja az adatok, például a nevét és típusát. szolgáltatás, és a kód és a konfiguráció. A szolgáltatás jegyzékfájl is néhány további paraméterek, amelyek segítségével konfigurálhatja a szolgáltatást, ha telepítve van.

## <a name="application-package-file-structure"></a>Alkalmazás csomag fájlstruktúra
A Service Fabric alkalmazás központi telepítése, az alkalmazás egy előre meghatározott könyvtárstruktúrát kell követnie. Struktúra példát a következő:

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

A ApplicationPackageRoot, amely meghatározza az alkalmazás ApplicationManifest.xml fájlt tartalmazza. Az alkalmazáshoz tartozó minden egyes szolgáltatás alkönyvtár használatban van, amely a szolgáltatás megköveteli az összes összetevők. Ezek alkönyvtárai a ServiceManifest.xml, és általában a következőket:

* *Kód*. Ez a könyvtár kódját tartalmazza.
* *Config*. Ez a könyvtár tartalmaz egy Settings.xml fájlban (és egyéb fájlokat, ha szükséges), hogy a szolgáltatás hozzá tud-e férni, megadott konfigurációs beállítások futásidőben.
* *Adatok*. Ez az egy további könyvtár, amely a szolgáltatás esetleg további helyi adatok tárolására. Adatok csak a rövid élettartamú adatok tárolására használandó. A Service Fabric másolja vagy módosítások adatkönyvtára replikációját, ha a szolgáltatásnak kell lennie (például feladatátvételkor) áthelyezését.

> [!NOTE]
> Nem kell létrehoznia a `config` és `data` könyvtárak, ha már nincs szükség.
>
>

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkeket azzal kapcsolatos információkat és feladatokat.
* [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [Az első Vendég végrehajtható-alkalmazás létrehozása a Visual Studio használatával](quickstart-guest-app.md)
* [Minta csomagolás és központi telepítése egy Vendég végrehajtható](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), beleértve az előzetes verzióját a csomagolás eszköz mutató hivatkozás
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) kapcsolaton keresztül kommunikáljon a Naming szolgáltatás REST használatával](https://github.com/Azure-Samples/service-fabric-containers)

