---
title: Meglévő végrehajtható fájl becsomagolása az Azure Service Fabricbe
description: Ismerje meg, hogyan csomagolja be a meglévő alkalmazást vendég végrehajtható fájlként, így egy Service Fabric-fürtre telepíthető.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457832"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Meglévő végrehajtható fájl központi telepítése a Service Fabric ben
Bármilyen típusú kódot futtathat, például node.js, Java vagy C++ az Azure Service Fabric szolgáltatásként. A Service Fabric az ilyen típusú szolgáltatások at vendég végrehajtható fájlként hivatkozik.

Vendég végrehajtható fájlok at a Service Fabric állapotmentes szolgáltatások kezelik. Ennek eredményeképpen a fürt csomópontjaira kerülnek, a rendelkezésre állás és más metrikák alapján. Ez a cikk azt ismerteti, hogyan csomagolható és központi telepítés egy vendég végrehajtható egy Service Fabric-fürt, a Visual Studio vagy a parancssori segédprogram használatával.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>A Service Fabric ben futtatható vendég végrehajtható fájl futtatása előnyei
A Service Fabric-fürtben futtatható vendég végrehajtható fájl futtatása számos előnnyel jár:

* Magas rendelkezésre állás. A Service Fabric-ben futó alkalmazások magas rendelkezésre állásúak. A Service Fabric biztosítja, hogy egy alkalmazás példányai futnak.
* Egészségügyi ellenőrzés. A Service Fabric állapotfigyelése észleli, ha egy alkalmazás fut, és diagnosztikai információkat biztosít, ha hiba van.   
* Alkalmazás életciklus-kezelése. Amellett, hogy a frissítések et állásidő nélkül biztosítja, a Service Fabric automatikus visszaállítást biztosít az előző verzióra, ha a frissítés során rossz állapotú eseményt jelentenek.    
* Sűrűség. A fürtben több alkalmazást is futtathat, így nincs szükség arra, hogy minden alkalmazás a saját hardverén fusson.
* Felderíthetőség: A REST használatával hívhatja a Service Fabric elnevezési szolgáltatás a fürt ben található egyéb szolgáltatások megkereséséhez. 

## <a name="samples"></a>Példák
* [Minta a vendég végrehajtható fájl jának csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlból (C# és nodejs) kommunikál nak az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Alkalmazás- és szolgáltatásjegyzékfájlok áttekintése
Egy vendég végrehajtható fájl központi telepítése részeként hasznos megérteni a Service Fabric csomagolási és üzembe helyezési modelljét az [alkalmazásmodellben](service-fabric-application-model.md)leírtak szerint. A Service Fabric csomagolási modell két XML-fájlra támaszkodik: az alkalmazás ra és a szolgáltatás jegyzékfájljaira. Az ApplicationManifest.xml és ServiceManifest.xml fájlok sémadefiníciója a Service Fabric SDK fájllal együtt a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*fájlba van telepítve.

* **Alkalmazásjegyzék** Az alkalmazásjegyzék az alkalmazás leírására szolgál. Felsorolja az azt alkotó szolgáltatásokat, valamint azokat a paramétereket, amelyek egy vagy több szolgáltatás üzembe helyezésének módját határozzák meg, például a példányok számát.

  A Service Fabric egy alkalmazás egy egység üzembe helyezési és frissítési egység. Egy alkalmazás frissíthető egyetlen egységként, ahol a lehetséges hibák és a lehetséges visszaállítások kezelése. A Service Fabric garantálja, hogy a frissítési folyamat sikeres, vagy ha a frissítés sikertelen, nem hagyja az alkalmazást ismeretlen vagy instabil állapotban.
* **Szolgáltatásjegyzék** A szolgáltatásjegyzék egy szolgáltatás összetevőit írja le. Olyan adatokat tartalmaz, mint például a szolgáltatás neve és típusa, valamint a kód és a konfiguráció. A szolgáltatásjegyzék tartalmaz néhány további paramétert is, amelyek a szolgáltatás üzembe helyezése után konfigurálhatók.

## <a name="application-package-file-structure"></a>Alkalmazáscsomag-fájl szerkezete
Egy alkalmazás üzembe helyezéséhez a Service Fabric, az alkalmazás nak egy előre definiált könyvtárstruktúrát kell követnie. Az alábbi példa erre a struktúrára mutat.

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

Az ApplicationPackageRoot az alkalmazást meghatározó ApplicationManifest.xml fájlt tartalmazza. Az alkalmazásban található minden egyes szolgáltatás alkönyvtára tartalmazza a szolgáltatás által igényelt összes összetevőt. Ezek az alkönyvtárak a ServiceManifest.xml fájl, és általában a következők:

* *Kód*. Ez a könyvtár tartalmazza a szolgáltatáskódot.
* *Konfigurációs*. Ez a könyvtár tartalmaz egy Settings.xml fájlt (és szükség esetén más fájlokat), amelyekhez a szolgáltatás futásidőben hozzáférhet bizonyos konfigurációs beállítások lekéréséhez.
* *Adatok*. Ez egy további könyvtár a szolgáltatás számára szükséges további helyi adatok tárolására. Az adatokat csak ideiglenes adatok tárolására szabad használni. A Service Fabric nem másolja vagy replikálja a módosításokat az adatkönyvtárba, ha a szolgáltatást át kell helyezni (például feladatátvétel során).

> [!NOTE]
> Nem kell létrehozni a `config` és `data` a könyvtárakat, ha nincs rájuk szükség.
>
>

## <a name="next-steps"></a>További lépések
A kapcsolódó információkat és feladatokat az alábbi cikkekben talál.
* [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [Az első vendég végrehajtható alkalmazás létrehozása a Visual Studio használatával](quickstart-guest-app.md)
* [Minta a vendég végrehajtható fájl csomagolásához és üzembe helyezéséhez,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)beleértve a csomagolási eszköz előzetes kiadására mutató hivatkozást
* [Minta két vendég végrehajtható fájlból (C# és nodejs) kommunikál nak az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-containers)

