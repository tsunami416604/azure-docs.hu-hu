---
title: Azure-hoz készült Avere vFXT
description: Bevezetés az Azure-hoz készült Avere vFXT, a HPC felhőalapú gyorsítótárazási rétegének használatába
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: a9dab2ca844feb1c24dfffc48b4f29cd1a311314
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76153191"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Mi az Azure-hoz készült Avere vFXT?

Az Azure-hoz készült Avere vFXT egy adatigényes, nagy teljesítményű feldolgozási (HPC-) feladatokra szabott fájlrendszer-gyorsítótárazási megoldás. E megoldás segítségével a felhőalapú számítástechnika méretezhetőségét kihasználva ott és akkor teheti elérhetővé az adatokat (akár a saját helyszíni hardverén tárolt adatokat is), ahol és amikor arra szükség van.

Az Avere vFXT a következő gyakori számítási megoldásokat támogatja:

* Hibrid felhőarchitektúra – Az Avere vFXT for Azure egy hardveres tárolórendszerrel is használható, amely a fájlok áthelyezése nélkül biztosítja a felhőalapú számítástechnika előnyeit.

* Felhőalapú adatcsere – Az Avere vFXT for Azure segítségével egyetlen projekt hez helyezheti át az adatokat a felhőbe, vagy "felemelheti és áthelyezheti" a teljes munkafolyamatot véglegesen.

![Blob Storage-hez és helyszíni adatközponthoz csatlakoztatott, Azure-előfizetésbeli Avere vFXT rendszer részleteit bemutató ábra](media/avere-vfxt-hybrid.png)

Az Avere vFXT for Azure az alábbi helyzetekre a legmegfelelőbb:

* HPC számítási feladatok intenzív olvasási műveletei
* Közös NFS protokollt használó alkalmazások
* 1000–40 000 processzormagot használó számítási farmok
* Integráció helyszíni hardveres megoldásokkal (NAS, Azure Blob Storage vagy mindkettő)

További információ: <https://azure.microsoft.com/services/storage/avere-vfxt/>

## <a name="who-uses-avere-vfxt-for-azure"></a>Kik használják az Azure-hoz készült Avere vFXT-t?

Az Avere vFXT valamennyi olvasásigényes számítási feladat esetében hatékonyan alkalmazható:

### <a name="visual-effects-rendering"></a>Vizuális effektusok renderelése

Multimédiás és szórakoztatóipari alkalmazásoknál az Avere vFXT-fürt használatával gyorsabb adathozzáférés biztosítható az idő szempontjából kritikus fontosságú renderelési projektek esetében. Rugalmasan, hatékonyan kezelhetők a nagyobb projektek is, mivel a gyorsítótártér kibővíthető, illetve további számítási csomópontok vehetők fel az Azure-ban.

### <a name="life-sciences"></a>Élettudományok

Az Avere vFXT lehetővé teszi a kutatók számára, hogy másodlagos elemzési munkafolyamatokat futtassanak az Azure Compute-ban, és a földrajzi adatokhoz való hozzáférést a helyüktől függetlenül.

A gyógyszeripari kutatás, Avere vFXT klaszterek felgyorsíthatja a kábítószer-felfedezés azáltal, hogy segít a kutatók megjósolni a kábítószer-cél kölcsönhatások és elemzi a kutatási adatokat.

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Avere vFXT-fürtökkel felgyorsíthatók a mennyiségi elemzési számítások, ezáltal a pénzügyi szolgáltatók megalapozottabb stratégiai döntéseket hozhatnak.

## <a name="features-and-specifications"></a>Funkciók és specifikációk

Az Avere vFXT rendszer három vagy több, fürtben konfigurált virtuális peremhálózati szűrőcsomópontból áll. Elhelyezhető az ügyfélszámítógépek közelében, amelyek így a fürthöz, és nem közvetlenül a tárolóhoz csatlakoznak.

Az Avere vFXT-fürt igény szerint gyorsítótárazza a fájlokat. Az ismételt kérelmek az idő több mint 80%-ában kiszolgálhatók a gyorsítótárból.

### <a name="compatibility"></a>Kompatibilitás

* Kompatibilis a NetApp és a Dell EMC Isilon hardveres NAS-rendszereivel
* Kompatibilis az Azure-blobokkal
* NFSv3 vagy SMB2 protokollt használ

Az Avere vFXT for Azure a következő Azure-erőforrásokat használja:

|Azure-összetevő|   |
|----------|-----------|
|Virtuális gépek|3 vagy több E32s_v3|
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Adatháttér-tárolás (nem kötelező) | Egy üres LRS-blobtároló |

## <a name="next-steps"></a>További lépések

Olvassa el ezeket a cikkeket, hogy tervezze meg és hozza létre saját Avere vFXT azure-telepítéshez.

* [Tervezze meg a rendszert](avere-vfxt-deploy-plan.md)
* [Az üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md)
* [A vFXT-fürt létrehozása](avere-vfxt-deploy.md)
