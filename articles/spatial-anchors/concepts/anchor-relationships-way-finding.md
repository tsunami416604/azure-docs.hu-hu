---
title: Forráshorgony kapcsolatok és az Azure térbeli horgonyok módon-megállapítás |} A Microsoft Docs
description: További információ a fogalmi modellhez forráshorgony kapcsolatok mögött. Ismerje meg belül adhatja horgonyok csatlakozni és teljesítéséhez úgy-találja a forgatókönyv az közeli API használatára.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863338"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Forráshorgony kapcsolatok és az Azure térbeli horgonyok módon-megállapítás

Forráshorgony kapcsolatok használatával csatlakoztatott horgonyokat létrehozni szóközzel, és majd az alábbiakhoz hasonló kérdéseket:

* Horgonyok közeli hasznos helyek vannak?
* Milyen távol vannak azokat?

## <a name="examples"></a>Példák

Ilyen esetekben használhatja a csatlakoztatott horgonyok:

* A feldolgozó el kell végeznie egy feladatot, amely magában foglalja a különböző helyeken az ipari factory látogat. A gyári térbeli horgonyok mindkét helyen van. A HoloLens vagy mobilalkalmazás segítségével a feldolgozó útmutató egyik helyről a másikra. Az alkalmazás a közeli térbeli horgonyok először kéri, és ezután végigvezeti a feldolgozó a következő helyre. Az alkalmazás általános iránya és a következő helyre távolság vizuálisan mutatja.

* Múzeumi létrehoz térbeli horgonyok nyilvános jeleníti meg. Ezek a kapcsolatok alapjainak együttesen alkotják a óránként Ismerkedjen meg a múzeumi alapvető nyilvános jeleníti meg. Egy nyilvános megjelenítése, látogatók megnyithatja a múzeumi vegyes valósághoz alkalmazással a mobileszközén. A telefonos kamera majd a hely általános irányát és a más nyilvános célhelyének távolság megtekintéséhez a bemutató körül mutassanak. A felhasználó felé a nyilvános megjelenítő mutatja be, az alkalmazás frissíti az általános irányát és segít a felhasználó távolság.

## <a name="set-up-way-finding"></a>Állítsa be úgy-keresés

Vonal-, üzemel irányát, valamint útmutatást nyújt a horgonyok közötti távolság használó alkalmazás által használt *módon felmérő*. Felmérő módja eltér navigációs kapcsolja kapcsolja be. Navigációs menüben kapcsolja kapcsolja be felhasználók haladnak falak, ajtó keresztül, valamint emeleteken körül. Módon-keresés, az a felhasználó élvezheti a cél általános irányát mutató. De következtetésekhez vagy a hely ismerete is segít, a felhasználó a struktúrát, a cél közötti navigáláshoz.

Olyan módon felmérő környezetet hozhat létre, először szóközzel előkészítése a felhasználói élményt és fejlesztése, hogy a felhasználók használni fog. Az általános lépések a következők:

1. **A hely megtervezése**: Döntse el, melyik helyen belül a hely módja felmérő tapasztalatok része lesz. A forgatókönyvekben a gyári felügyelő vagy a múzeumi bemutató koordinátor dönthet, mely hatálya alá tartozó módon felmérő tapasztalatok helyeket.
2. **Csatlakozás a horgonyok**: Látogasson el a kiválasztott helyek térbeli horgonyokat létrehozni. Ezt megteheti egy rendszergazdai módban, a végfelhasználói alkalmazás, vagy egy másik alkalmazásban teljes egészében. Csatlakozás lesz, vagy minden forráshorgony vonatkoznak a többi. A szolgáltatás kezeli a kapcsolatokat.
3. **Indítsa el a végfelhasználói élmény**: Felhasználók futtatnák az alkalmazást található horgonyra, amely a kiválasztott helyen lehet. Az általános tervezési szempontok kell meghatározni, hogy a helyek, ahol a felhasználók megadhatják a felhasználói élményt.
4. **Horgonyok közeli hasznos helyek keresése**: Miután a felhasználó megtalálja a horgony, az alkalmazás kérhetnek közeli hasznos helyek a központi jellegűek. Ez az eljárás egy testtartás között az eszközt, és ezek a kapcsolatok alapjainak adja vissza.
5. **A felhasználói útmutató**: Az alkalmazás az egyes ezek horgonyok testtartás segítségével a felhasználó általános irányát és távolság kapcsolatos útmutatást. A kamera adásának az alkalmazásban például előfordulhat, hogy megjelenítése egy ikont és a nyílra, hogy minden potenciális célhelyéhez, amelyek az alábbi képen látható módon.
6. **Útmutatás finomíthatja**: A felhasználó mutatja be, mivel az alkalmazás rendszeres időközönként kiszámíthatja az eszköz és a cél jegyzetobjektum között egy új testtartás. Az alkalmazás továbbra is fennáll, pontosítsa a útmutatást mutatók, amelyek segítségével a felhasználó érkeznek a célhelyre.

    ![Példa bemutatja, hogyan alkalmazás módon felmérő útmutatást jeleníti meg](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Horgonyok csatlakoztatása

Olyan módon felmérő környezetet hozhat létre, először a kiválasztott helyen helyezi el a központi jellegűek. Ebben a szakaszban feltételezzük lesz, az alkalmazás-rendszergazda ezt a munkát már befejeződött.

### <a name="connect-anchors-in-a-single-session"></a>Csatlakozás a központi jellegűek egyetlen munkamenetben

Horgonyok csatlakozni:

1. Megtudhatja, hogyan végzi az első helyen, és hozza létre A Forráshorgony egy CloudSpatialAnchorSession használatával.
2. Megtudhatja, hogyan végzi a második helyre. Az alapul szolgáló MR/AR platform áthelyezését követi nyomon.
3. Az azonos CloudSpatialAnchorSession Forráshorgony B létrehozása. Ezzel csatlakozott A és B horgonyok. A térbeli horgonyok szolgáltatás kezeli ezt a kapcsolatot.
4. Folytassa a fennmaradó kapcsolatok alapjainak eljárását.

### <a name="connect-anchors-in-multiple-sessions"></a>Több munkamenetet a kapcsolatok alapjainak csatlakoztatása

Térbeli horgonyok több munkamenetet keresztül kapcsolódhat. Ezzel a módszerrel hozzon létre és néhány horgonyok csatlakozzon egy időben, és ezután később létrehozása és további horgonyok csatlakoztatása. 

Horgonyok több munkamenetet keresztül csatlakozni:

1. Az alkalmazás egyes horgonyok egy CloudSpatialAnchorSession hoz létre. 
2. Egy másik időpontra az alkalmazás megkeresi ezeket horgonyok (például a Forráshorgony A) egyik új CloudSpatialAnchorSession használatával.
3. Megtudhatja, hogyan új helyre. A vegyes valóságon alapuló vagy kibővített valóság alapul szolgáló platform áthelyezését követi nyomon.
4. Az azonos CloudSpatialAnchorSession Forráshorgony C létrehozása. Ezzel csatlakozott A, B és C horgonyok. A térbeli horgonyok szolgáltatás kezeli ezt a kapcsolatot.

Ez az eljárás további horgonyok és további előadások idővel továbbra is.

### <a name="verify-anchor-connections"></a>Jegyzetobjektum kapcsolatok ellenőrzése

Az alkalmazás ellenőrizheti, hogy két kapcsolati alapok egy lekérdezést a közeli kapcsolatok alapjainak kiállításával csatlakoznak. Ha a lekérdezés eredménye a cél jegyzetobjektum tartalmaz, a forráshorgony-kapcsolat ellenőrzése. A horgonyok nem csatlakoznak, ha az alkalmazás megpróbálhatja újból csatlakoztathatja őket. 

Az alábbiakban néhány ok, miért horgonyok csatlakozása sikertelen lehet:

* Az alapul szolgáló vegyes valóságon alapuló vagy kibővített valóság platform követési megszakadt a horgonyok kapcsolódás folyamata során.
* A térbeli horgonyok szolgáltatással való kommunikáció közben hálózati hiba miatt a forráshorgony kapcsolat nem őrizhető meg.

### <a name="find-sample-code"></a>Mintakód keresése

Mintakód bemutatja, hogyan horgonyok csatlakozni, és hajtsa végre a lekérdezéseket közeli hasznos helyek megkereséséhez lásd: [térbeli horgonyok mintaalkalmazások](https://github.com/Azure/azure-spatial-anchors-samples).
