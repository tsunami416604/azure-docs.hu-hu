---
title: Az Azure Service Fabric lemezképet tároló kapcsolati karakterlánc |} A Microsoft Docs
description: Megismerheti a lemezképet tároló kapcsolati karakterlánc
services: service-fabric
documentationcenter: .net
author: alexwun
manager: chackdan
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 4a56b48c0041e963b89312c59335b45cabacc1bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667549"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Az ImageStoreConnectionString beállítás

Néhány dokumentációnk azt röviden díjszabásunkban létezik-e egy "ImageStoreConnectionString" paraméter nélkül leíró valóban feletti. És a egy cikk például elvégzése után [PowerShell-lel telepítés és eltávolítás alkalmazások][10], úgy tűnik, hajtsa végre az összes érték másolja és illessze be a fürtjegyzék a cél-fürt látható módon. Ezért a beállítás konfigurálható egy fürtben kell lennie, de a fürt létrehozásakor a [az Azure portal][11], ez a beállítás konfigurálása nem választható, a beállítás mindig "fabric: ImageStore". Mi az a célja, hogy ezt a beállítást, majd?

![Cluster Manifest][img_cm]

A Service Fabric lépésként platformként a Microsoft – belső használat számos különböző csapat, így bizonyos aspektusainak nagymértékben testre szabható – a "lemezkép Store" egyik tulajdonsága. Lényegében a lemezkép Store tárháza moduláris alkalmazáscsomagok tárolására. A fürt egy csomópontjára telepítik az alkalmazást, akkor a csomóponton a lemezkép Store alkalmazáscsomag tartalmának tölt. Az ImageStoreConnectionString beállítás, amely tartalmazza az ügyfelek és a csomópontok a megfelelő Store kép egy adott fürtben található összes szükséges információt.

Jelenleg nincsenek Store kép szolgáltatók három lehetséges típusú, és a megfelelő kapcsolati karakterláncok az alábbiak szerint:

1. Kép Store szolgáltatás: "fabric: ImageStore"

2. Fájlrendszer: "file:[file rendszer elérési út]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

A szolgáltató típusát, éles környezetben használt érték a lemezkép Store szolgáltatást, amely egy állapotalapú megőrzött rendszert szolgáltatás, amely a Service Fabric Explorerből tekintheti. 

![Kép Store szolgáltatás][img_is]

A rendszer szolgáltatásként magát a fürtön belül a lemezkép Store üzemeltető kiküszöböli a csomagtárház vonatkozó külső függőségeket, és lehetővé teszi az jobban szabályozhatja a tárolási helye. A kép Store körül jövőbeli fejlesztések várhatóan először, ha nem, kizárólag a Store kép szolgáltató lehetőséget. A kapcsolati karakterláncot a lemezkép Store szolgáltató nem lehet bármilyen egyedi adatokat, mert az ügyfél már csatlakoztatva van a cél fürtnek. Az ügyfél csak tudnia kell, hogy a rendszer szolgáltatás célzó protokollok kell használni.

A fájlrendszer-szolgáltatót szolgál a lemezkép Store szolgáltatás helyett helyi beépített fürtök fejlesztése során a fürt némileg gyorsabb elindíthat. A különbség az, általában kicsi, de a legtöbb azok számára, a fejlesztés során hasznos optimalizálás. Fürt üzembe helyezése helyi beépített más tárolási szolgáltató típusai is lehet, de általában nem indokolt ehhez, mivel a fejlesztés és tesztelés munkafolyamat szolgáltató függetlenül ugyanaz marad. Az Azure Storage-szolgáltató csak létezik támogatása a régi fürtök telepíteni, mielőtt a lemezképet Store szolgáltató jelent meg.

Továbbá nem a fájlrendszer-szolgáltatót, vagy az Azure Storage-szolgáltató egy kép Store több fürt közötti megosztása módszerként használandó – ennek eredményeképpen a fürtök konfigurációs adatainak sérülését, mivel minden fürt ütköző adatokat írhatna a kép Store. Kiépített alkalmazáscsomagok több fürt közötti megosztásához használni [sfpkg] [ 12] fájlok, amelyek is feltölthetők a bármely külső tárolóra egy letöltési URI-t.

Tehát bár az ImageStoreConnectionString konfigurálható, ugyanúgy használhatja az alapértelmezett beállítás. Azure-t a Visual Studióban való közzétételkor a paraméter értéke automatikusan, ennek megfelelően. Programozott üzembe helyezés az Azure-ban futó fürtök, a kapcsolati karakterláncot, mindig "fabric: ImageStore". Azonban Ha kétségei vannak, az érték mindig ellenőrizhető a fürtjegyzék által lekérésével [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), vagy [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Mind a helyi tesztelése, és éles fürtökben, valamint a lemezkép Store szolgáltatás szolgáltatóval mindig kell konfigurálni.

### <a name="next-steps"></a>További lépések
[Üzembe helyezése és távolíthat el alkalmazásokat a PowerShell használatával][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
