---
title: Azure VMware-megoldás a CloudSimple-től – privát felhők
description: Ismerje meg a CloudSimple privát felhők és fogalmak.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024948"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple private cloud – áttekintés

A CloudSimple percek alatt átalakítja és kiterjeszti a VMware-számítási feladatokat a nyilvános felhőkre. A CloudSimple szolgáltatás használatával natív módon telepítheti a VMware-t az Azure operációs rendszer nélküli féminfrastruktúrán. A központi telepítés az Azure-beli helyeken él, és teljes mértékben integrálható az Azure-felhő többi részével.

A CloudSimple megoldás teljes Körű VMware-működési folytonosságot biztosít. Ez a megoldás a következő nyilvános felhőbeli előnyöket biztosítja:

* Rugalmasság
* Innováció
* Hatékonyság

A CloudSimple segítségével élvezheti a felhőfelhasználási modell előnyeit, amely csökkenti a teljes tulajdonlási költséget. Igény szerinti kiépítést, a felosztó-kiosztó növést és a kapacitásoptimalizálást is kínál.

A CloudSimple teljes mértékben kompatibilis a következőkkel:

* Meglévő eszközök
* Készségek
* Folyamatok

Ez a kompatibilitás lehetővé teszi a csapatok számára, hogy az Azure-felhőben lévő számítási feladatokat az ilyen típusú szabályzatok megszakítása nélkül kezeljék:

* Network (Hálózat)
* Biztonság  
* Adatvédelem  
* Naplózás

A CloudSimple kezeli az infrastruktúrát és az összes szükséges hálózati és felügyeleti szolgáltatást. A CloudSimple szolgáltatás lehetővé teszi, hogy csapata a következőkre összpontosítson:

* Üzleti érték
* Alkalmazások üzembe helyezése
* Az üzletmenet folytonossága
* Támogatás
* Szabályzatbetartatás

## <a name="private-cloud-environment-overview"></a>Privát felhőkörnyezet – áttekintés

A privát felhő egy elkülönített VMware-verem, amely támogatja a következőket:

* ESXi házigazdák
* vCenter
* vSAN között
* Nsx

A privát felhők kezelése a CloudSimple portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományban.

A verem fut:

* Dedikált csomópontok
* Elkülönített, csupasz fém hardvercsomópontok

A felhasználók a vermet natív VMware eszközökkel használják fel, többek között a következőkkel:

* vCenter
* NSX menedzser

Dedikált csomópontokat helyezhet üzembe az Azure-helyeken. Ezután kezelheti őket az Azure-ral és a CloudSimple-nel. A magánfelhő egy vagy több vSphere-fürtből áll, és minden fürt 3–16 csomópontot tartalmaz.

Létrehozhat egy privát felhőt megvásárolt, használatalapú fizetési csomópontok vagy fenntartott, dedikált csomópontok használatával.

A magánfelhőt a helyszíni környezethez és az Azure-hálózathoz csatlakoztathatja a következő kapcsolatok használatával:

* Biztonságos
* Privát VPN
* Azure ExpressRoute

A privát felhő környezet célja, hogy megszüntesse az egyes meghibásodási pontokat:

* Az ESXi-fürtök vSphere magas rendelkezésre állásúak, és úgy vannak méretezve, hogy legalább egy tartalék csomóponttal rendelkeznek a rugalmasság érdekében.
* A vSAN redundáns elsődleges tárolást biztosít. A vSan legalább három csomópontot igényel, hogy védelmet nyújtson egyetlen hiba ellen. A vSAN konfigurálható úgy, hogy nagyobb rugalmasságot biztosítson a nagyobb fürtök számára.
* A RAID-10 tárolási házirenddel rendelkező vCenter, PSC és NSX-kezelő virtuális gépek et a tárolási hibák elleni védelem érdekében konfigurálhatja. A vSphere HA védelmet nyújt a csomópont- és hálózati hibák ellen.

## <a name="scenarios-for-deploying-a-private-cloud"></a>A magánfelhő üzembe helyezésének forgatókönyvei

Íme néhány példa a magánfelhő központi telepítéséhez használt esetekre.

### <a name="data-center-retirement-or-migration"></a>Adatközpontok kivonása vagy áttelepítése

* További kapacitást kaphat, ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert.
* Adja hozzá a szükséges kapacitást a felhőben, és szüntesse meg a hardverfrissítések kezelésének fejfájását.
* Csökkentse a felhőbe való migrálás kockázatát és költségét az időigényes konverziókhoz vagy újraarchitektúrákhoz képest.
* A felhőbe való bevetés felgyorsításához használja a jól ismert VMware eszközöket és készségeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait az Ön tempójában.

### <a name="expand-on-demand"></a>Igény szerinti bővítés

* Bontsa ki a felhőt a nem várt igények, például az új fejlesztési környezetek vagy a szezonális kapacitásadatlöketek kielégítésére.
* Igény szerint hozzon létre új kapacitást, és csak addig őrizheti meg, amíg szüksége van rá.
* Csökkentse az előzetes befektetést, gyorsítsa fel a kiépítés sebességét, és csökkentse az összetettséget ugyanazzal az architektúrával és szabályzatokkal mind a helyszíni, mind a felhőben.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Katasztrófa utáni helyreállítás és virtuális asztalok az Azure-felhőben

* Távoli hozzáférést hozhat létre az adatokhoz, alkalmazásokhoz és asztalokhoz az Azure-felhőben. A nagy sávszélességű kapcsolatok, feltöltése / letöltése adatok gyors an-át, hogy visszaszerezze az eseményeket. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak, amelyet a felhasználók elvárnak egy asztali alkalmazástól.

* Replikálja az összes szabályzatot és hálózatot a felhőben a CloudSimple portál és a jól ismert VMware eszközök használatával. A replikáció csökkenti a DR és VDI implementációk létrehozásának és kezelésének erőfeszítéseit és kockázatát.

### <a name="high-performance-applications-and-databases"></a>Nagy teljesítményű alkalmazások és adatbázisok

* Futtassa a legigényesebb számítási feladatokat a CloudSimple által biztosított hiperkonvergens architektúrával.
* Futtassa az Oracle, a Microsoft SQL server, a middleware rendszerek és a nagy teljesítményű, nem SQL adatbázisok futtatását.
* Tapasztalja meg a felhőt saját adatközpontként a nagy sebességű, 25 Gbit/s sebességű hálózati kapcsolatokkal. A nagy sebességű kapcsolatok lehetővé teszik a helyszíni, az Azure-beli VMware és az Azure privát számítási feladatainak hibrid alkalmazásait a teljesítmény veszélyeztetése nélkül.

### <a name="true-hybrid"></a>Valódi hibrid

* A DevOps egyesítése a VMware és az Azure-szolgáltatások között.
* Optimalizálja a VMware felügyeletét az Azure-szolgáltatásokhoz és -megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók.
* Az adatközpont bővítése vagy az alkalmazások újratervezése nélkül is elérheti a nyilvános felhőszolgáltatásokat.
* Központosíthatja az identitásokat, a hozzáférés-vezérlési szabályzatokat, a naplózást és a figyelést az Azure-beli VMware-alkalmazásokhoz.

## <a name="limits"></a>Korlátok

Az alábbi táblázat a magánfelhő erőforrásaira vonatkozó csomópontkorlátokat sorolja fel.

| Erőforrás | Korlát |
|----------|-------|
| A magánfelhő létrehozásához szükséges csomópontok minimális száma | 3 |
| A fürt csomópontjainak maximális száma magánfelhőben | 16 |
| A csomópontok maximális száma a magánfelhőben | 64 |
| Az új fürt csomópontjainak minimális száma | 3 |

## <a name="next-steps"></a>További lépések

* További információ a [magánfelhő létrehozásáról](create-private-cloud.md)
* További információ a [magánfelhő-környezet konfigurálásáról](quickstart-create-private-cloud.md)
