---
title: Az Azure Service Fabric lemezképet tároló karakterláncában |} Microsoft Docs
description: A lemezkép tárolási kapcsolati karakterlánc ismertetése
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 3c34a3851dbb5c5258b3dc0cf35a510f62cbe14e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>A előtaggal beállítás ismertetése

Egyes dokumentációban azt röviden említse meg létezik-e egy "Előtaggal" paraméter nélkül valóban jelentés leíró. És egy cikk például elvégzése után [központi telepítése és törlése alkalmazás PowerShell-lel][10], úgy tűnik, tegye az összes érték másolja és illessze be a fürtjegyzékben, a cél fürt megjelenő. Így a beállítás konfigurálható fürtönként kell lennie, de a fürt létrehozásakor a [Azure-portálon][11], nincs lehetőség kell konfigurálnia a beállítást, és a rendszer mindig "fabric: Lemezképtárolóba". Mi az a célja az ezt a beállítást, majd?

![A Fürtjegyzékben][img_cm]

Service Fabric a belső Microsoft felhasználásához platformként által indított számos különböző csapatok, így nagy mértékben testre szabható bizonyos aspektusainak - a "Image Store" az egyik ilyen tulajdonsága. Alapvetően az Image Store tárháza moduláris alkalmazáscsomagok tárolására. Az alkalmazás központi telepítése a fürt egyik csomópontjában levő, adott csomóponton alkalmazáscsomag tartalmának az Image Store letölti. A előtaggal, amely tartalmazza az összes szükséges információt az ügyfelek és a csomópont található a megfelelő lemezképet tároló egy adott fürt beállítás.

Jelenleg nincsenek Image Store szolgáltatók három lehetséges típusú, és a megfelelő kapcsolati karakterláncok a következők:

1. Image Store szolgáltatás: "fabric: Lemezképtárolóba"

2. Fájlrendszer: "file:[file rendszer path]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

A szolgáltató éles környezetben használt a kép Store szolgáltatást, egy állapot-nyilvántartó megőrzött rendszerszolgáltatás is látható, a Service Fabric Explorerből. 

![Image Store Service][img_is]

Az Image Store rendszerszolgáltatás egyrészt a fürt belül az üzemeltetési megszünteti a csomag tárház vonatkozó külső függőségeket, és a tárolási hely több ellenőrzést ad. Az Image Store körül jövőbeli fejlesztések valószínűleg úgy, hogy a lemezkép tárolási szolgáltató célozza, először, ha nem kizárólag. A kapcsolati karakterláncot, a lemezkép tárolási szolgáltató nem rendelkezik egyedi információt, mivel az ügyfél már csatlakoztatva van a célfürtön. Az ügyfélnek csak kell tudnia, hogy a rendszer szolgáltatás célzó protokollok kell használni.

A fájlrendszer szolgáltató szolgál a lemezképet tároló szolgáltatás helyett egy beépített helyi fürtök a fejlesztés során bootstrap némileg gyorsabb a fürt. A különbség az általában kicsi, de a legtöbb segítsen a fejlesztés során hasznos optimalizálás. Egy helyi egy beépített fürt más tárolási szolgáltató típusai is telepíthető, de általában nincs szükség erre, mivel a fejlesztése és tesztelési célú munkafolyamat szolgáltató függetlenül ugyanaz marad. Az Azure Storage-szolgáltató csak örökölt támogatása a régi fürtök telepítve előtt a lemezkép tárolási szolgáltató létezik.

Továbbá, sem a fájlrendszer szolgáltató és az Azure tárolási szolgáltatót, nem kell használni az Image Store több fürt közötti megosztása módszerként – ennek eredményeképpen a fürt konfigurációs adatok sérülése, mivel egyes fürtök ütköző adatokat írhat az Lemezképtárolóhoz. Kiépített alkalmazáscsomagok több fürt közötti megosztásához használni [sfpkg] [ 12] fájlok, amelyek is feltölthetők a letöltési URI Azonosítójának bármely külső tárolóba.

Ezért konfigurálható a előtaggal pedig általában csak az alapértelmezett beállítást használja. Visual Studio használatával Azure közzétételekor a paraméter értéke automatikusan meg ennek megfelelően. Az Azure-ban üzemeltetett fürtökhöz a programozott telepítés, a kapcsolati karakterlánc: mindig "fabric: Lemezképtárolóba". Ha kétségei vannak, az értéke mindig ellenőrizhetők a fürtjegyzékben által lekérésével [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), vagy [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Mind a helyszíni tesztelése, és mindig éles fürtök úgy konfigurálni, hogy a lemezkép tárolási szolgáltató használata is.

### <a name="next-steps"></a>További lépések
[Központi telepítése, és távolítsa el az alkalmazásokat a PowerShell használatával][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
