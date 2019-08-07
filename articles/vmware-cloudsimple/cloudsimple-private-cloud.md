---
title: Privát felhők a VMware-megoldásban a CloudSimple által – Azure
description: Ismerje meg a privát felhők és fogalmak CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9885366d5987870fe2739083ff47abaae9ef6ed1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816206"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple – a privát felhő áttekintése

A CloudSimple percek alatt átalakítja és kiterjeszti a VMware-alapú számítási feladatokat a nyilvános felhőkbe. A CloudSimple szolgáltatás használatával natív módon telepítheti a VMware-et az Azure operációs rendszer nélküli számítógépek infrastruktúráján. Az üzembe helyezés az Azure-beli helyszíneken működik, és teljes mértékben integrálódik az Azure-felhő többi részébe.

* A CloudSimple-megoldás teljes körű VMware működési folytonosságot biztosít. Ez a megoldás a következő nyilvános Felhőbeli előnyöket nyújtja:
  * Rugalmasságát
  * Innováció
  * Hatékonyság
* A CloudSimple a Felhőbeli fogyasztási modellek előnyeit kihasználva csökkenti a teljes tulajdonlási költségeket. Igény szerinti üzembe helyezést, utólagos elszámolást és kapacitás-optimalizálást is kínál.
* A CloudSimple teljes mértékben kompatibilis az alábbiakkal:
  * Meglévő eszközök
  * Képességek
  * Folyamatok
* Ez a kompatibilitási funkció lehetővé teszi, hogy a csapatok az Azure-felhőben kezeljék a munkaterheléseket, a szabályzatok megszakítása nélkül:
  * Network (Hálózat)
  * Biztonság  
  * Adatvédelem  
  * Naplózás
* A CloudSimple kezeli az infrastruktúrát és az összes szükséges hálózatkezelési és felügyeleti szolgáltatást. A CloudSimple szolgáltatás lehetővé teszi, hogy csapata a következőkre koncentráljon:
  * Üzleti érték
  * Alkalmazás kiépítés
  * Az üzletmenet folytonossága
  * Támogatás
  * Szabályzat érvénybe léptetése

## <a name="private-cloud-environment-overview"></a>A saját felhőalapú környezet áttekintése

A privát felhő egy elszigetelt VMware-verem, például a következő környezetek:

* ESXi-gazdagépek
* vCenter
* vSAN
* NSX

A privát felhőket a saját felügyeleti tartományában lévő vCenter-kiszolgáló kezeli.

A verem a következőn fut:

* Dedikált csomópontok
* Elszigetelt operációs rendszer nélküli hardveres csomópontok

A felhasználók a natív VMware-eszközökön keresztül használják a veremet, beleértve a következőket:

* vCenter
* NSX Manager

A dedikált csomópontok üzembe helyezése az Azure-beli helyszíneken végezhető el. Ezt követően kezelheti őket az Azure-ban és a CloudSimple-ban is. A privát felhő egy vagy több vSphere-fürtből áll, és minden fürt 3 – 16 csomópontot tartalmaz.

Létrehozhat egy privát felhőt a kiépített csomópontok használatával:

* Utólagos elszámolású csomópontok
* Fenntartott, dedikált csomópontok

A privát felhő a következő kapcsolatok használatával csatlakoztatható a helyszíni környezethez és az Azure-hálózathoz:

* Biztonságos
* Privát VPN
* Azure ExpressRoute

A privát felhőalapú környezet úgy lett kialakítva, hogy egyetlen meghibásodási ponttal ne legyenek kizárva:

* Az ESXi-fürtök vSphere magas rendelkezésre állással vannak konfigurálva, és a rugalmasság érdekében a méretük legalább egy tartalék csomópontot tartalmaz.
* a vSAN redundáns elsődleges tárterületet biztosít. a vSan legalább három csomópontot igényel az egyetlen hiba elleni védelem biztosításához. A vSAN konfigurálásával nagyobb rugalmasságot biztosíthat a nagyobb fürtök számára.
* A vCenter, a PSC és a NSX Manager virtuális gépeket RAID-10 tárolási házirenddel is konfigurálhatja a tárolási hibák elleni védelem érdekében. Ezután a vSphere a csomópont-és hálózati hibák ellen védettek.

## <a name="scenarios-for-deploying-a-private-cloud"></a>A privát felhő üzembe helyezésének forgatókönyvei

* **Adatközpont-kivonulás vagy áttelepítés**

  * Ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert, további kapacitást érhet el.
  * Adja hozzá a szükséges kapacitást a felhőben, és távolítsa el a hardveres frissítések kezelésének fejfájását.
  * Csökkentse a Felhőbeli áttelepítések kockázatát és költségeit, az időigényes átalakításokhoz vagy az újraarchitektúrához képest.
  * A felhőbe történő Migrálás felgyorsításához használja a jól ismert VMware-eszközöket és-képességeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait a tempóban.

* **Kibontás igény szerint**

  * Bontsa ki a felhőt a nem várt igények kielégítéséhez, például az új fejlesztési környezetekhez vagy a szezonális kapacitások kitöréséhez.
  * Igény szerint új kapacitást hozhat létre, és megtarthatja, amíg szüksége van rá.
  * Csökkentse a kezdeti befektetéseket, gyorsítsa fel a kiépítés sebességét, és csökkentse a bonyolultságot ugyanazzal az architektúrával és házirenddel mind a helyszínen, mind a felhőben.

* **Vész-helyreállítási és virtuális asztalok az Azure-felhőben**

  * Távoli hozzáférés létrehozása az Azure-felhőben tárolt adatelemekhez, alkalmazásokhoz és asztali gépekhez. A nagy sávszélességű kapcsolatokkal gyorsan tölthet le/tölthet le adatokat az incidensekről. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak a felhasználók számára egy asztali alkalmazástól.

  * A CloudSimple-portál és a jól ismert VMware-eszközök használatával replikálhatja az összes szabályzatot és hálózatkezelést a felhőben. Ez a replikálás csökkenti a DR-és VDI-implementációk létrehozásának és kezelésének erőfeszítéseit és kockázatait.

* **Nagy teljesítményű alkalmazások és adatbázisok**

  * Futtassa a legigényesebb számítási feladatokat a CloudSimple által biztosított hiperkonvergens architektúrával.
  * Az Oracle, a Microsoft SQL Server, a köztes rendszerek és a nagy teljesítményű, nem SQL-adatbázisok futtatása.

  * A felhőt saját adatközpontként, nagy sebességű, 25 GB/s-os hálózati kapcsolattal tapasztalhatja. A nagy sebességű kapcsolatok lehetővé teszik, hogy olyan hibrid alkalmazásokat futtasson, amelyek a helyszíni, a VMware-en és az Azure-beli privát számítási feladatokon alapulnak, és nem veszélyeztethetik a teljesítményt.

* **Valódi hibrid**

  * A DevOps egyesítheti a VMware-és az Azure-szolgáltatások között.
  * Optimalizálja a VMware-felügyeletet az Azure-szolgáltatásokhoz és-megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók.
  * A nyilvános felhőalapú szolgáltatásokat anélkül érheti el, hogy kibővíti az adatközpontot, vagy újratervezi az alkalmazásokat.
  * Az Azure-beli VMware-alkalmazások identitásának, hozzáférés-vezérlési házirendjeinek, naplózásának és figyelésének központosítása.

## <a name="limits"></a>Korlátok

Az alábbi táblázat a privát felhő erőforrásaihoz tartozó csomópontokra vonatkozó korlátozásokat mutatja be.

| Resource | Korlát |
|----------|-------|
| A privát felhő létrehozásához szükséges csomópontok minimális száma | 3 |
| Fürtben lévő csomópontok maximális száma egy privát felhőben | 16 |
| A privát felhőben lévő csomópontok maximális száma | 64 |
| Csomópontok minimális száma egy új fürtön | 3 |

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [hozhat létre saját felhőt](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)