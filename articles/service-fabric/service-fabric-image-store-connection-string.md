---
title: Az Azure Service Fabric rendszerkép-tároló kapcsolati karakterlánca
description: Ismerje meg a lemezkép-tároló kapcsolati karakterlánc, beleértve annak felhasználásait és alkalmazásait a Service Fabric-fürthöz.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645667"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Az ImageStoreConnectionString beállítás ismertetése

Néhány dokumentációnkban röviden megemlítjük az "ImageStoreConnectionString" paraméter létezését anélkül, hogy leírnánk, mit is jelent valójában. És miután egy cikket, [például alkalmazások telepítése és eltávolítása a PowerShell használatával,][10]úgy néz ki, mint minden, amit csinál, másolja/illessze be az értéket, ahogy az a célfürt jegyzékfájljában látható. Így a beállítás konfigurálhatónak kell lennie fürtenként, de amikor létrehoz egy fürtöt az [Azure Portalon][11]keresztül, nincs lehetőség a beállítás konfigurálására, és mindig "fabric:ImageStore". Akkor mi a célja ennek a beállításnak?

![Fürtjegyzék][img_cm]

A Service Fabric a Microsoft belső fogyasztásának platformjaként indult számos különböző csapat számára, így bizonyos aspektusai nagymértékben testreszabhatók - az "Image Store" az egyik ilyen szempont. Lényegében az Image Store egy csatlakoztatható tárház az alkalmazáscsomagok tárolására. Amikor az alkalmazás telepítve van a fürt egyik csomópontjára, a csomópont letölti az alkalmazáscsomag tartalmát a rendszerképtárból. Az ImageStoreConnectionString egy olyan beállítás, amely tartalmazza az összes szükséges információt mind az ügyfelek és a csomópontok, hogy megtalálják a megfelelő image store egy adott fürthöz.

Jelenleg három féle Image Store szolgáltatók és a megfelelő kapcsolat karakterláncok a következők:

1. Image Store szolgáltatás: "fabric:ImageStore"

2. Fájlrendszer: "file:[fájlrendszer elérési útja]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https; AccountName=[...]; Számlakulcs =[...]; Konténer=[...]"

Az éles környezetben használt szolgáltató típusa a Image Store szolgáltatás, amely egy állapotalapú, megőrzött rendszerszolgáltatás, amely a Service Fabric Explorerből látható. 

![Image Store szolgáltatás][img_is]

Az Image Store üzemeltetése a fürtön belüli rendszerszolgáltatásban kiküszöböli a csomagtár külső függőségeit, és nagyobb ellenőrzést biztosít a tárolási hely felett. Az Image Store körüli jövőbeli fejlesztések valószínűleg először az Image Store-szolgáltatót célozzák meg, ha nem kizárólag. A Image Store-szolgáltató kapcsolati karakterlánca nem rendelkezik egyedi adatokkal, mivel az ügyfél már csatlakozik a célfürthöz. Az ügyfélnek csak tudnia kell, hogy a rendszerszolgáltatást célzó protokollokat kell használni.

A fájlrendszer-szolgáltatót a rendszer a helyi egydobozos fürtök képtárszolgáltatása helyett használja a fejlesztés során, hogy a fürtet kissé gyorsabban próbálja megbeiktatni. A különbség általában kicsi, de ez egy hasznos optimalizálás a legtöbb ember a fejlesztés során. Lehetőség van egy helyi egydobozos fürt üzembe helyezésére a többi tárolószolgáltató-típussal is, de általában nincs ok erre, mivel a fejlesztési/tesztelési munkafolyamat a szolgáltatótól függetlenül ugyanaz marad. Az Azure Storage-szolgáltató csak az Image Store-szolgáltató bevezetése előtt üzembe helyezett régi fürtök örökölt támogatásához létezik.

Továbbá nem a fájlrendszer-szolgáltatót vagy az Azure Storage-szolgáltatót kell használni a képtár több fürt közötti megosztásának módjaként – ez a fürt konfigurációs adatainak sérülését eredményezi, mivel minden fürt ütköző adatokat írhat a lemezképre Tárolja. A kiépített alkalmazáscsomagok több fürt közötti megosztásához használjon [sfpkg-fájlokat,][12] amelyek bármely külső tárolóba feltölthetők letöltési URI-val.

Tehát míg az ImageStoreConnectionString konfigurálható, csak az alapértelmezett beállítást használja. Amikor a Visual Studio-n keresztül teszi közzé az Azure-t, a paraméter ennek megfelelően automatikusan be van állítva. Az Azure-ban üzemeltetett fürtöknek történő programozott központi telepítés esetén a kapcsolati karakterlánc mindig "fabric:ImageStore". Ha kétségei vannak, értéke mindig ellenőrizhető a [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx)vagy [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest)fürtjegyzékének beolvasásával. Mind a helyszíni teszt, mind az éles fürtök mindig úgy vannak konfigurálva, hogy a Image Store-szolgáltatót is használják.

### <a name="next-steps"></a>További lépések
[Alkalmazások telepítése és eltávolítása a PowerShell használatával][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
