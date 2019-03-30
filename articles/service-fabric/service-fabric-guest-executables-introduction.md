---
title: Egy meglévő végrehajtható telepítése Azure Service fabric |} A Microsoft Docs
description: Ismerje meg a Vendég végrehajtható, mint egy meglévő alkalmazás becsomagolásához úgy is üzembe helyezhető a Service Fabric-fürt.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: aljo
ms.openlocfilehash: b7efeb1b4d83f6a6b372f73a7c0a5ca9bffdc052
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670593"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Egy meglévő végrehajtható telepítése Service Fabricre
Bármilyen, Node.js, Java, C++ kódot az Azure Service Fabric szolgáltatásként is futtathatja. A Service Fabric services ilyen típusú vendég végrehajtható fájlokként hivatkozik.

Futtatható vendégalkalmazás állapotmentes szolgáltatásokhoz hasonlóan a Service Fabric által kell kezelni. Ennek eredményeképpen a csomópontok a fürtben, a rendelkezésre állás és egyéb mérőszámok alapján kerülnek. Ez a cikk ismerteti, hogyan csomagolása és üzembe helyezése a Vendég végrehajtható a Service Fabric-fürtön a Visual Studio vagy a parancssori segédprogram használatával.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>A Vendég végrehajtható, a Service Fabric előnyei
Van egy Vendég végrehajtható, a Service Fabric-fürtön futó számos előnnyel jár:

* Magas rendelkezésre állás. Magas rendelkezésre állású alkalmazások Service Fabric futtatására menjenek végbe. A Service Fabric biztosítja, hogy futnak-e egy alkalmazás példánya.
* Szolgáltatásállapot-figyelést. Service Fabric állapotmonitorozásának észleli, ha egy alkalmazás fut, és diagnosztikai adatokat biztosít, ha hiba történik.   
* Alkalmazáséletciklus-kezelésre. Frissítések megadása üzemkimaradás nélkül, mellett a Service Fabric korábbi verziójának automatikus visszaállítása biztosít, ha a frissítés során jelzett hibás állapottal kapcsolatos esemény.    
* Sűrűsége. Több alkalmazást futtathatja egy fürtben, amely kiküszöböli a mindegyik alkalmazás saját hardveren futtatott.
* Könnyebben: REST segítségével meghívhatja a Service Fabric elnevezési szolgáltatásban található más szolgáltatások a fürtben. 

## <a name="samples"></a>Példák
* [Minta csomagolás és a egy futtatható vendégalkalmazás üzembe helyezése](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) keresztül kommunikáljon az elnevezési szolgáltatásban REST használatával](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Alkalmazás és szolgáltatás jegyzékfájlok áttekintése
A futtatható vendégalkalmazás üzembe helyezése részeként hasznos lehet megérteni a Service Fabric formátumokat támogató csomagolási és üzembe helyezési modell leírtak szerint [alkalmazásmodell](service-fabric-application-model.md). A Service Fabric csomagolási modell támaszkodik két XML-fájlt: az alkalmazás és jegyzékek. A ApplicationManifest.xml és ServiceManifest.xml fájlt sémadefiníciója telepítve van a Service Fabric SDK be *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Alkalmazásjegyzék** az alkalmazásjegyzéknek az alkalmazás leírására szolgál. A lista felsorolja az azt alkotó szolgáltatásokat, és más paramétereket, amelyek segítségével határozhatók meg, hogy egy vagy több szolgáltatást kell üzembe helyezni, mint például a példányok számát.

  A Service Fabric, az alkalmazás egységek, üzembe helyezés és frissítés. Egy alkalmazás, a lehetséges hibák és a lehetséges visszaállítások kezelve egyetlen egységként frissíthetők. A Service Fabric biztosítja, hogy a frissítési folyamat akár sikeres, vagy ha a frissítés sikertelen lesz, nem hagyja el a kérelem ismeretlen vagy nem stabil állapotban.
* **Adatszolgáltatási jegyzékfájl** a szolgáltatásjegyzék szolgáltatás összetevőit ismerteti. Ez magában foglalja az adatok, például a nevét és a szolgáltatás, és a kód és a konfiguráció típusát. A szolgáltatásjegyzék is tartalmaz néhány további paraméterek, amelyek a szolgáltatás konfigurálásához, üzembe helyezése után is használható.

## <a name="application-package-file-structure"></a>Alkalmazás-csomag fájlstruktúra
A Service Fabric-alkalmazás üzembe helyezése, az alkalmazás egy előre meghatározott könyvtárstruktúrát kell követni. Az alábbiakban látható egy példa, hogy a struktúra.

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

A ApplicationPackageRoot az ApplicationManifest.xml fájl, amely meghatározza az alkalmazás tartalmaz. Tartalmazza az összes a azokat az összetevőket, amelyek a szolgáltatás megköveteli az alkalmazás minden egyes szolgáltatás alkönyvtárban szolgál. Ezek alkönyvtárai a ServiceManifest.xml, és általában a következőket:

* *Kód*. Ez a könyvtár kódját tartalmazza.
* *Config*. Ez a könyvtár tartalmaz egy Settings.xml fájlt (és egyéb fájlokat, ha szükséges), hogy a szolgáltatás hozzáfér-e futásidőben egyes konfigurációs beállítások lekéréséhez.
* *Adatok*. Ez a további címtárak a szolgáltatás esetleg szükséges további helyi adatokat tárolhat. Adatok csak a rövid élettartamú adatok tárolására használandó. A Service Fabric nem másolja vagy módosításokat replikálja a adatkönyvtárat, ha a szolgáltatást kell (például feladatátvételkor) helyezhetők.

> [!NOTE]
> Nem kell létrehozni a `config` és `data` könyvtárak, ha már nincs szükség rájuk.
>
>

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket a kapcsolódó információkat és feladatokat.
* [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [Az első Vendég végrehajtható-alkalmazás létrehozása Visual studióval](quickstart-guest-app.md)
* [Minta csomagolás és a egy futtatható vendégalkalmazás üzembe helyezése a](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), és a telepítőre mutató előzetes verzióját a csomagolás eszköz
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) keresztül kommunikáljon az elnevezési szolgáltatásban REST használatával](https://github.com/Azure-Samples/service-fabric-containers)

