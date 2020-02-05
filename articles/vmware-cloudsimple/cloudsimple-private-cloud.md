---
title: Azure VMware-megoldások (AVS) – privát felhők – AVS
description: Ismerkedjen meg az AVS Private Felhőkkel és fogalmakkal.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024948"
---
# <a name="avs-private-cloud-overview"></a>Az AVS Private Cloud áttekintése

Az AVS percek alatt átalakítja és kiterjeszti a VMware-alapú számítási feladatokat a nyilvános felhőkbe. Az AVS szolgáltatás használatával natív módon telepítheti a VMware-et az Azure operációs rendszer nélküli számítógépek infrastruktúráján. Az üzembe helyezés az Azure-beli helyszíneken működik, és teljes mértékben integrálódik az Azure-felhő többi részébe.

Az AVS-megoldás teljes körű VMware működési folytonosságot biztosít. Ez a megoldás a következő nyilvános Felhőbeli előnyöket nyújtja:

* Rugalmasságát
* Innováció
* Hatékonyság

Az AVS-vel előnyt élvez a Felhőbeli fogyasztási modell, amely csökkenti a teljes tulajdonlási költségeket. Igény szerinti üzembe helyezést, utólagos elszámolást és kapacitás-optimalizálást is kínál.

Az AVS teljes mértékben kompatibilis a rel:

* Meglévő eszközök
* Ismereteit
* Folyamatok

Ez a kompatibilitási funkció lehetővé teszi, hogy a csapatok az Azure-felhőben kezeljék a munkaterheléseket, anélkül, hogy megzavarják az ilyen típusú szabályzatokat:

* Network (Hálózat)
* Biztonság  
* Adatvédelem  
* Naplózás

Az AVS felügyeli az infrastruktúrát és az összes szükséges hálózatkezelési és felügyeleti szolgáltatást. Az AVS szolgáltatás lehetővé teszi, hogy csapata a következőkre koncentráljon:

* Üzleti érték
* Alkalmazás kiépítés
* Üzleti folyamatok fenntarthatósága
* Támogatás
* Szabályzatbetartatás

## <a name="avs-private-cloud-environment-overview"></a>Az AVS Private Cloud Environment áttekintése

Az AVS Private Cloud egy elkülönített VMware-verem, amely a következőket támogatja:

* ESXi-gazdagépek
* vCenter
* vSAN
* NSX

Az AVS privát felhők kezelése az AVS-portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományában.

A verem a következőn fut:

* Dedikált csomópontok
* Elszigetelt operációs rendszer nélküli hardveres csomópontok

A felhasználók a natív VMware-eszközökön keresztül használják a veremet, beleértve a következőket:

* vCenter
* NSX-kezelő

A dedikált csomópontok üzembe helyezése az Azure-beli helyszíneken végezhető el. Ezután felügyelheti őket az Azure-ban és az AVS-ben. Az AVS Private Cloud egy vagy több vSphere-fürtből áll, és minden fürt 3 – 16 csomópontot tartalmaz.

A megvásárolt, utólagos elszámolású csomópontok vagy fenntartott dedikált csomópontok használatával létrehozhat egy AVS Private-felhőt.

Az AVS Private Cloud a helyszíni környezethez és az Azure-hálózathoz a következő kapcsolatok használatával csatlakoztatható:

* Biztonságos
* Privát VPN
* Azure ExpressRoute

Az AVS Private Cloud-környezet úgy lett kialakítva, hogy elkerülje az egyes meghibásodási pontokat:

* Az ESXi-fürtök vSphere magas rendelkezésre állással vannak konfigurálva, és a rugalmasság érdekében a méretük legalább egy tartalék csomópontot tartalmaz.
* a vSAN redundáns elsődleges tárterületet biztosít. a vSan legalább három csomópontot igényel az egyetlen hiba elleni védelem biztosításához. A vSAN konfigurálásával nagyobb rugalmasságot biztosíthat a nagyobb fürtök számára.
* A vCenter, a PSC és a NSX Manager virtuális gépeket RAID-10 tárolási házirenddel is konfigurálhatja a tárolási hibák elleni védelem érdekében. a vSphere HA védelmet biztosít a csomópont-és hálózati hibák ellen.

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>Az AVS Private Cloud üzembe helyezésének forgatókönyvei

Íme néhány példa az AVS Private Cloud üzemelő példányának használati eseteire.

### <a name="data-center-retirement-or-migration"></a>Adatközpont-kivonulás vagy áttelepítés

* Ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert, további kapacitást érhet el.
* Adja hozzá a szükséges kapacitást a felhőben, és távolítsa el a hardveres frissítések kezelésének fejfájását.
* Csökkentse a felhőbe való Migrálás kockázatát és költségeit az időigényes átalakítások vagy az újraarchitektúrák összehasonlításával.
* A felhőbe történő Migrálás felgyorsításához használja a jól ismert VMware-eszközöket és-képességeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait a tempóban.

### <a name="expand-on-demand"></a>Kibontás igény szerint

* Bontsa ki a felhőt a nem várt igények kielégítéséhez, például az új fejlesztési környezetekhez vagy a szezonális kapacitások kitöréséhez.
* Igény szerint új kapacitást hozhat létre, és megtarthatja, amíg szüksége van rá.
* Csökkentse a kezdeti befektetéseket, gyorsítsa fel a kiépítés sebességét, és csökkentse a bonyolultságot ugyanazzal az architektúrával és házirenddel mind a helyszínen, mind a felhőben.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Vész-helyreállítási és virtuális asztalok az Azure-felhőben

* Távoli hozzáférés létrehozása az Azure-felhőben tárolt adatelemekhez, alkalmazásokhoz és asztali gépekhez. A nagy sávszélességű kapcsolatokkal gyorsan tölthet le/tölthet le adatokat az incidensekről. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak a felhasználók számára egy asztali alkalmazástól.

* Az AVS-portál és a jól ismert VMware-eszközök használatával replikálhatja az összes házirendjét és hálózatát a felhőben. A replikáció csökkenti a DR-és VDI-implementációk létrehozásának és kezelésének erőfeszítéseit és kockázatait.

### <a name="high-performance-applications-and-databases"></a>Nagy teljesítményű alkalmazások és adatbázisok

* Futtassa a legigényesebb számítási feladatokat az AVS által biztosított hiperkonvergens architektúrával.
* Az Oracle, a Microsoft SQL Server, a köztes rendszerek és a nagy teljesítményű, nem SQL-adatbázisok futtatása.
* A felhőt saját adatközpontként, nagy sebességű, 25 GB/s-os hálózati kapcsolattal tapasztalhatja. A nagy sebességű kapcsolatok lehetővé teszik, hogy olyan hibrid alkalmazásokat futtasson, amelyek a helyszíni, a VMware-en és az Azure-beli privát számítási feladatokon alapulnak, és nem veszélyeztethetik a teljesítményt.

### <a name="true-hybrid"></a>Valódi hibrid

* A DevOps egyesítheti a VMware-és az Azure-szolgáltatások között.
* Optimalizálja a VMware-felügyeletet az Azure-szolgáltatásokhoz és-megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók.
* A nyilvános felhőalapú szolgáltatásokat anélkül érheti el, hogy kibővíti az adatközpontot, vagy újratervezi az alkalmazásokat.
* Az Azure-beli VMware-alkalmazások identitásának, hozzáférés-vezérlési házirendjeinek, naplózásának és figyelésének központosítása.

## <a name="limits"></a>Korlátozások

Az alábbi táblázat az AVS Private Cloud erőforrásainak csomópontokra vonatkozó korlátozásait sorolja fel.

| Erőforrás | Korlát |
|----------|-------|
| A csomópontok minimális száma egy AVS Private-felhő létrehozásához | 3 |
| Fürtben lévő csomópontok maximális száma egy AVS Private-felhőben | 16 |
| Csomópontok maximális száma egy AVS Private-felhőben | 64 |
| Csomópontok minimális száma egy új fürtön | 3 |

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [hozhat létre egy AVS Private-felhőt](create-private-cloud.md)
* Megtudhatja, hogyan [konfigurálhat egy AVS Private Cloud Environment-környezetet](quickstart-create-private-cloud.md)
