---
title: Meglévő végrehajtható fájl becsomagolása az Azure Service Fabricba | Microsoft Docs
description: További információ a meglévő alkalmazások vendég végrehajtható fájlként való csomagolásáról, így Service Fabric-fürtön is üzembe helyezhető.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: atsenthi
ms.openlocfilehash: bd6984db67a8a7b9c38988558ada51e12d337f52
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013262"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Meglévő végrehajtható fájl üzembe helyezése Service Fabric
Bármilyen típusú kód, például a Node. js, a Java vagy C++ az Azure Service Fabric szolgáltatásként is futtatható. A Service Fabric vendég végrehajtható fájlokként hivatkozik az ilyen típusú szolgáltatásokra.

A vendég végrehajtható fájlokat a Service Fabric, például az állapot nélküli szolgáltatások kezelik. Ennek eredményeképpen a fürt csomópontjain vannak elhelyezve a rendelkezésre állás és az egyéb mérőszámok alapján. Ez a cikk azt ismerteti, hogyan lehet a Visual Studióval vagy egy parancssori segédprogrammal becsomagolni és üzembe helyezni egy vendég végrehajtható fájlt egy Service Fabric-fürtön.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>A vendég végrehajtható fájl futtatásának előnyei Service Fabric
A Service Fabric-fürtben több előnye van a vendég végrehajtható fájl futtatására:

* Magas rendelkezésre állás. A Service Fabricon futó alkalmazások nagyon elérhetővé válnak. Service Fabric biztosítja, hogy egy alkalmazás példányai futnak.
* Állapot monitorozása. Service Fabric állapot-figyelés észleli, ha egy alkalmazás fut, és diagnosztikai adatokat biztosít, ha hiba történt.   
* Az alkalmazások életciklusának kezelése. A leállás nélküli verziófrissítések mellett a Service Fabric automatikus visszaállítást biztosít az előző verzióra, ha a frissítés során helytelen állapotú esemény jelent meg.    
* Sűrűségű. Több alkalmazást is futtathat egy fürtben, ami szükségtelenné teszi az egyes alkalmazások saját hardveren való futtatásának szükségességét.
* Felderíthetőség: a REST használatával meghívhatja a Service Fabric Naming Service-t, hogy a fürt más szolgáltatásait is megkeresse. 

## <a name="samples"></a>Példák
* [Minta a vendég végrehajtható fájlok csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlrólC# (és NodeJS) a REST használatával kommunikáló elnevezési szolgáltatás segítségével](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Az alkalmazások és szolgáltatások jegyzékfájljának áttekintése
A vendég végrehajtható fájlok üzembe helyezésének részeként érdemes megismerni a Service Fabric csomagolási és telepítési modellt az [Application modelben](service-fabric-application-model.md)leírtak szerint. A Service Fabric csomagolási modell két XML-fájlra támaszkodik: az alkalmazásra és a szolgáltatásokra vonatkozó jegyzékfájlokra. A ApplicationManifest. XML és az ServiceManifest. XML fájlok sémájának definíciója az Service Fabric SDK-ba van telepítve a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Alkalmazás jegyzékfájlja** Az alkalmazás jegyzékfájlja az alkalmazás leírására szolgál. Felsorolja az azt alkotó szolgáltatásokat, valamint azokat a paramétereket, amelyek segítségével meghatározható egy vagy több szolgáltatás üzembe helyezése, például a példányok száma.

  Service Fabric az alkalmazás az üzembe helyezés és a frissítés egysége. Egy alkalmazás frissíthető egyetlen egységként, ahol a lehetséges hibák és a lehetséges visszaállítások kezelhetők. Service Fabric garantálja, hogy a frissítési folyamat sikeres, vagy ha a frissítés sikertelen, az alkalmazás ismeretlen vagy instabil állapotban marad.
* **Szolgáltatás jegyzékfájlja** A szolgáltatás jegyzékfájlja a szolgáltatás összetevőit ismerteti. Ide tartoznak az olyan adatmennyiségek, mint a név és a szolgáltatás típusa, valamint a kód és a konfiguráció. A szolgáltatás jegyzékfájlja Emellett néhány további paramétert is tartalmaz, amelyek segítségével konfigurálhatja a szolgáltatást az üzembe helyezés után.

## <a name="application-package-file-structure"></a>Alkalmazáscsomag-fájl szerkezete
Egy alkalmazás Service Fabricba való telepítéséhez az alkalmazásnak egy előre meghatározott címtár-struktúrát kell követnie. A következő példa erre a szerkezetre mutat.

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

A ApplicationPackageRoot tartalmazza az alkalmazást definiáló ApplicationManifest. xml fájlt. Az alkalmazásban található összes szolgáltatás alkönyvtára a szolgáltatás által igényelt összes összetevőt tartalmazza. Ezek az alkönyvtárak a ServiceManifest. XML, és általában a következők:

* *Kód*. Ez a könyvtár tartalmazza a szolgáltatási kódot.
* *Konfiguráció*. Ez a könyvtár tartalmaz egy Settings. xml fájlt (és szükség esetén más fájlokat is), amelyekkel a szolgáltatás futásidőben elérheti az adott konfigurációs beállításokat.
* *Az adathalmazt*. Ez egy további könyvtár a szolgáltatás által igényelt további helyi információk tárolására. Csak az időszakos adattárolást kell használni. Service Fabric nem másolja vagy replikálja az adatkönyvtár módosításait, ha a szolgáltatást át kell helyezni (például a feladatátvétel során).

> [!NOTE]
> Ha nincs szüksége rájuk, nem kell létrehoznia a `config` és a `data` könyvtárakat.
>
>

## <a name="next-steps"></a>Következő lépések
A kapcsolódó információkkal és feladatokkal kapcsolatban tekintse meg a következő cikkeket.
* [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [Az első vendég végrehajtható alkalmazás létrehozása a Visual Studio használatával](quickstart-guest-app.md)
* [Minta egy vendég végrehajtható fájl csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), beleértve a csomagoló eszköz előzetes kiadására mutató hivatkozást is.
* [Minta két vendég végrehajtható fájlrólC# (és NodeJS) a REST használatával kommunikáló elnevezési szolgáltatás segítségével](https://github.com/Azure-Samples/service-fabric-containers)

