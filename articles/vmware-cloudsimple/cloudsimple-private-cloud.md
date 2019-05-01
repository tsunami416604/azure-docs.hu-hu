---
title: A VMware megoldás CloudSimple – Azure által a privát felhők
description: Ismerje meg a privát felhők CloudSimple és fogalmak.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577045"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple magánfelhő áttekintése

CloudSimple alakítja át, és kiterjeszti a VMware nyilvános felhők számítási feladatokat percek alatt. A CloudSimple szolgáltatást használja, telepítheti a VMware natív módon az Azure-operációs rendszer nélküli infrastruktúrán. Az üzembe helyezés az Azure-helyen találhatók, és teljes mértékben integrálható az Azure-felhő a többi.

* A CloudSimple megoldás biztosítja a teljes VMware folyamatos működésének. Ez a megoldás lehetővé teszi a nyilvános felhő előnyei:
  * Rugalmasság
  * Innováció
  * Hatékonyság
* CloudSimple, az előnyökkel jár egy felhőalapú fogyasztási modell, amely csökkenti a teljes birtoklási költségét. Igény szerinti üzembe helyezési, használatalapú-as--növekedés és a kapacitás optimalizálása is kínál.
* CloudSimple teljes mértékben kompatibilis a következő:
  * Meglévő eszközökkel
  * Képességek
  * Folyamatok
* Ez a kompatibilitási lehetővé teszi a különböző csoportok kezelése az Azure-felhőben, a számítási feladatok a szabályzatok megszakítása nélkül:
  * Network (Hálózat)
  * Biztonság  
  * Adatvédelem  
  * Naplózás
* CloudSimple az infrastruktúra és az összes szükséges hálózati és a felügyeleti szolgáltatás kezeli. A CloudSimple szolgáltatás lehetővé teszi, hogy arra koncentrálhasson, a csapat:
  * Üzleti érték
  * Alkalmazások üzembe helyezése
  * Az üzletmenet folytonossága
  * Támogatás
  * Szabályzatbetartatás

## <a name="private-cloud-environment-overview"></a>Magánfelhő-környezet áttekintése

A magánfelhő esetében egy elkülönített VMware-környezetet, például az ezekben a környezetekben:

* ESXi-gazdagépek
* vCenter
* vSAN
* NSX

Magánfelhők a saját felügyeleti tartományhoz a vCenter-kiszolgáló felügyeli.

A verem fut:

* Dedikált csomópontok
* Operációs rendszer nélküli elkülönített hardver csomópontok

A felhasználók használhatják fel a stack keresztül natív VMware-eszközök, többek között:

* vCenter
* NSX Manager

Dedikált csomópontok az Azure-helyen telepíthet. Majd kezelheti őket az Azure és a CloudSimple. Magánfelhő áll egy vagy több vSphere-fürtöket, és minden egyes fürt 3 – 16 csomópontot tartalmaz.

Csomópontok vásárolt privát felhő használatával is létrehozhat:

* Használatalapú fizetés csomópontok
* Fenntartott, dedikált csomópontok

A magánfelhő csatlakozhat a helyszíni környezet és az Azure-hálózatot használ a következő kapcsolatok:

* Biztonságos
* Személyes VPN
* Azure ExpressRoute

A magánfelhő-környezethez kiküszöbölése a meghibásodási pont kellene célja:

* ESXi-fürtök vSphere magas rendelkezésre állású vannak konfigurálva, és vannak méretezve, hogy a rugalmassághoz legalább egy tartalék csomóponttal rendelkezik.
* vSAN redundáns elsődleges tárolót biztosít. vsan-hoz szükséges legalább három csomópontok meghibásodása elleni védelem biztosításához. Beállíthatja, hogy nagyobb rugalmasságot biztosítanak a nagyobb fürtök esetén a vsan-hoz.
* A segítségével konfigurálhat vCenter PSC és NSX Manager virtuális gépek RAID 10-es tárolt házirend tárolási meghibásodása elleni védelem érdekében. Ezt követően, hogy védettek a magas rendelkezésre ÁLLÁSÚ vSphere csomópont- és hálózati hibák ellen.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Forgatókönyvek a magánfelhő üzembe helyezése

* **Data center kivezetni vagy áttelepítés**

  * Akkor kaphat további kapacitást, ha eléri növelje meglévő adatközpontjának korlátait, vagy frissítse a hardvert.
  * Adja hozzá a szükséges kapacitás a felhőben, és megszüntetheti a hardver-frissítések kezelésével fejfájást okozhat.
  * Csökkentse a kockázata és költsége is a migrálás a felhőbe, időigényes átalakítások vagy rearchitecture képest.
  * Ismerős VMware-eszközök és képességek segítségével felgyorsíthatja a felhőbe történő migrálásról. A felhőben az Azure-szolgáltatások modernizálhatja alkalmazásait, hogy tempójában.

* **Bontsa ki az igény szerinti**

  * Bontsa ki a nem várt igényeinek, például az új fejlesztési környezetek vagy szezonális kapacitás adatlöketekkel a felhőbe.
  * Új kapacitás igény szerint hozhat létre, és tartsa csak is szüksége lesz rá.
  * Csökkentse a kezdeti beruházást, gyorsabb üzembe helyezésének sebességét és a rendszerek összetettségének csökkentése az architektúra és a szabályzatok a helyszíni és a felhőben egyaránt.

* **Vészhelyreállítás és virtuális asztalok az Azure-felhőben**

  * Állítsa be adatok, alkalmazások és az Azure-felhőben asztalok távoli elérését. A nagy sávszélességű kapcsolatokkal, feltöltése / letöltése az adatok gyors szeretné használni az incidensek. Kis késleltetésű hálózatok számára, a gyors válasz időkorlátja, amely többek között elvárják egy asztali alkalmazásból.

  * A házirendek és a hálózatkezelés, a felhőben CloudSimple Portallal és az ismerős VMware-eszközök replikálni. A replikáció kombinálásával erőfeszítés létrehozását és kezelését a Vészhelyreállítási és VDI-megvalósítások kockázatát.

* **Nagy teljesítményű alkalmazások és adatbázisok**

  * A legnagyobb erőforrás-igényű számítási feladatok, futtassa a hiperkonvergens architektúrája CloudSimple által biztosított.
  * Futtassa az Oracle, a Microsoft SQL server, a közbenső szoftver rendszerek és a nagy teljesítményű nem-SQL-adatbázisok.

  * A felhőalapú környezetre a saját adatközpont nagy sebességű 25-Gbps hálózati kapcsolatokkal rendelkező felhasználói élményt. Nagy sebességű kapcsolatokra lehetővé teszi hibrid alkalmazások, amelyek részben a helyszínen, a VMware Azure-on futtatásához és az Azure privát feladatokhoz, teljesítmény csökkenése nélkül.

* **Hibrid**

  * Fejlesztési és üzemeltetési csoportfelügyeleti VMware és az Azure-szolgáltatások.
  * VMware-felügyelet az Azure-szolgáltatásokat és megoldásokat, amelyek alkalmazhatók a számítási feladatok optimalizálása.
  * Hozzáférés a nyilvános felhőszolgáltatások, bontsa ki az adatközpontot, és az alkalmazások újratervezése nélkül.
  * Identitások, a hozzáférés-vezérlési házirendeket, naplózás és figyelés a VMware Azure-beli alkalmazások központosítása.

## <a name="limits"></a>Limits

Az alábbi táblázat a csomópont korlátait a magánfelhő erőforrásait.

| Erőforrás | Korlát |
|----------|-------|
| Magánfelhő létrehozása a csomópontok minimális száma | 3 |
| A magánfelhő a fürtben található csomópontok maximális száma | 16 |
| A magánfelhőben csomópontok maximális száma | 64 |
| Új fürt a csomópontok minimális száma | 3 |

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [magánfelhő létrehozása](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [egy magánfelhő-környezet konfigurálása](quickstart-create-private-cloud.md)