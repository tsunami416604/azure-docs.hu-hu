---
title: Azure-hoz készült Avere vFXT
description: Bevezetés az Azure-hoz készült Avere vFXT, a HPC felhőalapú gyorsítótárazási rétegének használatába
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: b0b835dde4b98619238eaac9449432aaa9acffd3
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670487"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Mi az Azure-hoz készült Avere vFXT? 

Az Azure-hoz készült Avere vFXT egy adatigényes, nagy teljesítményű feldolgozási (HPC-) feladatokra szabott fájlrendszer-gyorsítótárazási megoldás. E megoldás segítségével a felhőalapú számítástechnika méretezhetőségét kihasználva ott és akkor teheti elérhetővé az adatokat (akár a saját helyszíni hardverén tárolt adatokat is), ahol és amikor arra szükség van.

Az Avere vFXT a következő gyakori számítási megoldásokat támogatja: 

* Hibrid felhőarchitektúra: az Azure-hoz készült Avere vFXT kompatibilis a hardveralapú tárolórendszerekkel. Ennek révén anélkül használhatja ki a felhőalapú számítástechnika előnyeit, hogy fájlokat kellene hozzá áthelyeznie. 
* Felhőalapú teljesítménynövelés: az Azure-hoz készült Avere vFXT segítségével adatokat helyezhet át a felhőbe egy adott projekt esetében, vagy véglegesen „átemelhet” teljes munkafolyamatokat is. 

![Blob Storage-hez és helyszíni adatközponthoz csatlakoztatott, Azure-előfizetésbeli Avere vFXT rendszer részleteit bemutató ábra](media/avere-vfxt-hybrid.png)

Az Azure-hoz készült Avere vFXT legmegfelelőbb felhasználási területei a következők: 

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

Az Avere vFXT segítségével a kutatók az Azure Compute-ban futtathatják másodlagos elemzési munkafolyamataikat, és a tárolási helytől függetlenül hozzáférhetnek a genomadatokhoz.

A gyógyszerészeti kutatások esetében felgyorsítható a kutatási folyamat, mivel az Avere vFXT-fürtök alkalmazása segíti a kutatókat a gyógyszerhatás előrejelzésében és a kutatási adatok elemzésében.

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Avere vFXT-fürtökkel felgyorsíthatók a mennyiségi elemzési számítások, ezáltal a pénzügyi szolgáltatók megalapozottabb stratégiai döntéseket hozhatnak. 

## <a name="features-and-specifications"></a>Funkciók és specifikációk

Az Avere vFXT rendszer három vagy több, fürtben konfigurált virtuális peremhálózati szűrőcsomópontból áll. Elhelyezhető az ügyfélszámítógépek közelében, amelyek így a fürthöz, és nem közvetlenül a tárolóhoz csatlakoznak. 

Az Avere vFXT-fürt igény szerint gyorsítótárazza a fájlokat. Az ismételt kérelmek az idő több mint 80%-ában kiszolgálhatók a gyorsítótárból.

### <a name="compatibility"></a>Kompatibilitás 

* Kompatibilis a NetApp és a Dell EMC Isilon hardveres NAS-rendszereivel
* Kompatibilis az Azure-blobokkal
* NFSv3 vagy SMB2 protokollt használ

Az Avere vFXT az alábbi Azure-erőforrásokat használja: 

|Azure-összetevő|   |
|----------|-----------|
|Virtuális gépek|3 vagy több (D16s_v3 vagy E32s_v3)|
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Háttérbeli adattárolás (nem kötelező) | Egy üres LRS-blobtároló |

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokkal elérhető információkkal hozzákezdhet saját üzemelő Avere vFXT-példányának létrehozásához. 

* [A rendszer megtervezése](avere-vfxt-deploy-plan.md)
* [Az üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md)
* [A vFXT-fürt létrehozása](avere-vfxt-deploy.md)
