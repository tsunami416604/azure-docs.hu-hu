---
title: Forráshorgony kapcsolatok és az Azure térbeli horgonyok módon-megállapítás |} A Microsoft Docs
description: A fogalmi modellhez mögött forráshorgony kapcsolatokat ismerteti. Horgonyok belül egy szóközt, és a folyamat teljesítéséhez úgy felmérő forgatókönyv közeli API használatával való kapcsolódáson ismertetik. Után elmagyarázza a fogalmi modellhez, mutasson a fejlesztők a mintaalkalmazások, amelyeket a közeli így kezdhetik megvalósítása ebben a forgatókönyvben a saját alkalmazásokban.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3d1ee0b25fbbf0ef895bdf6ff8afad71ff82de25
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217172"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Forráshorgony kapcsolatok és az Azure térbeli horgonyok módon-megállapítás

Forráshorgony kapcsolatok engedélyezése, hogy a csatlakoztatott horgonyokat létrehozni szóközzel, és ezután kérdéseket őket, mint például:

* Horgonyok közeli hasznos helyek vannak?
* Milyen távol vannak azokat?

## <a name="examples"></a>Példák

Néhány példa használati esetek engedélyezheti a csatlakoztatott horgonyok a következők:

1. Egy feldolgozói kell végrehajtani az eljárást, amely magában foglalja a különböző helyeken az ipari factory felkeresése. A gyár helyezte térbeli horgonyok minden helyen eljárásban részt. A HoloLens vagy mobilalkalmazás segítségével a feldolgozó útmutató egyik helyről a másikra. A térbeli horgonyok közeli, és ezután útmutató a következő helyre a feldolgozó esetében először felkéri. Az alkalmazás visual mutatók kapcsolatos általános irányát és távolságot a feladat végrehajtásához a következő helyre jelenít meg.

2. Múzeumi térbeli horgonyok, nyilvános megjelenítése, amelyek együtt hozzon létre egy adott bemutató – például az "Egy órás elindíthatja alapvető nyilvános jeleníti meg az" a múzeumi hoz létre. Amikor a látogatók egy nyilvános megjelenítése, akkor megnyithatják a múzeumi vegyes valósághoz alkalmazással a mobileszközén. Ezután azok lenne telefont pont körül a hely és a kamera hírcsatorna keresztül, és tekintse meg az általános irányát és közötti távolságot a más nyilvános megjelenik a bemutató. A felhasználó felé a nyilvános láthatók az egyik és megtudhatja, hogyan indulásakor, az alkalmazás fokozatosan frissíti a általános irány és távolságra van a felhasználók az útmutató segítségével.

## <a name="way-finding"></a>Így-keresés

Tegyük fel, hogy az alkalmazás használja "line-az-üzemel" irányát és kapcsolatok alapjainak útmutatást tegyen a felhasználók közötti távolság. Nevezzük a teljes forgatókönyv, módon-találja. Fontos megjegyezni felmérő módja eltér a navigációs kapcsolja kapcsolja be. Navigációs menüben kapcsolja kapcsolja be a felhasználók haladnak falak, ajtó keresztül, valamint emeleteken körül. A felhasználó módon-keresés, az a cél általános irányát mutató biztosítunk. De a felhasználó következtetésekhez vagy a hely ismerete is segít haladjon végig a struktúra a célhelyre.

Létrehozását olyan módon felmérő környezetet magában foglalja a szóközzel előkészítése a felhasználói élményt és a egy alkalmazás fejlesztésével, amely a végfelhasználók működjön együtt. A fogalmi folyamat lépései a következők:

1. Tervezési terület: Határozza meg, hogy a helyek belül a hely, amely a módon felmérő élmény részt. A korábbi példákban Ez a tevékenység előfordulhat, hogy befejeződött a gyári felügyelő vagy a múzeumi bemutató koordinátor.
2. Kapcsolódás a horgonyok: Valaki a kiválasztott helyek látogatások, és létrehozza a térbeli horgonyok van. Ez a feladat teljes mértékben elvégezhető egy rendszergazdai módban, a végfelhasználói alkalmazás vagy egy másik alkalmazás. Ez a folyamat minden egyes forráshorgony csatlakoztatva, vagy kapcsolódik a többihez. Ezek a kapcsolatok megmaradnak a szolgáltatásban.
3. A végfelhasználói élmény indítása: Az első lépés a végfelhasználók számára, hogy keresse meg a központi jellegűek, használja az alkalmazást, amely a kiválasztott helyek bármelyikén lehetnek egyikét. Amely meghatározza, hogy a helyek, ahol a végfelhasználók adhat meg a felhasználói élményt az összteljesítmény tervezése részét képezi.
4. Horgonyok közeli hasznos helyek keresése: Miután a felhasználó egy kapcsolati alap található, az alkalmazás kérhetnek közeli hasznos helyek a központi jellegűek. Ez az eljárás egy testtartás között az eszközt, és ezek a kapcsolatok alapjainak adja vissza.
5. Irányítsa a felhasználót: Az alkalmazás egyes ezek horgonyok hasznos útmutatást mutatók általános irányát és távolságskála megjelenítése a testtartás kihasználhatják. Például előfordulhat, hogy egy ikont, és minden potenciális célhelyéhez, ahogyan az alábbi képen is látható jelölő mobilalkalmazásban hírcsatorna mutató nyílra a kamera.
6. Finomítás útmutatást: A felhasználó mutatja be, mivel az alkalmazás rendszeres időközönként kiszámíthatja az eszköz és a cél jegyzetobjektum között egy új testtartás. Az alkalmazás továbbra is fennáll, pontosítsa a útmutatást mutatók, amelyek segítségével a felhasználó érkeznek a célhelyre.

![Értekezlet helyét](./media/meeting-spot.png)

## <a name="connecting-anchors"></a>Horgonyok csatlakoztatása

Olyan módon felmérő környezetet hozhat létre, helyezze el a csatlakoztatott horgonyok a kiválasztott helyen kell. Az alábbiakban feltételezzük fog működni az alkalmazás rendszergazdája végzi el.

### <a name="connecting-anchors-in-a-single-session"></a>Kapcsolódás a központi jellegűek egyetlen munkamenetben

A horgonyok csatlakoztatásának lépései a következők:

1. A rendszergazda A Forráshorgony hoz létre, és az első helyen ismerteti egy CloudSpatialAnchorSession használatával.
2. Az alapul szolgáló MR/AR platform továbbra is nyomon követhetik a felhasználót a rendszergazda a második helyre mutatja be.
3. A rendszergazda az azonos CloudSpatialAnchorSession Forráshorgony B hoz létre. Horgonyok A és B kapcsolódik, és ezt a kapcsolatot a térbeli horgonyok Azure-szolgáltatás által kezelt.
4. Továbbra is a csatlakozáshoz használni kívánt összes horgonyok eljárását.

### <a name="multiple-sessions"></a>Több munkamenet

Térbeli horgonyok keresztül több munkamenetet is csatlakoztathat. Ez a módszer lehetővé teszi, és hozhat létre és néhány horgonyok csatlakozni egyszerre, és később hozzon létre további horgonyok csatlakozzon. Horgonyok csatlakozás több munkamenetet:

1. Az alkalmazás egyes horgonyok egy CloudSpatialAnchorSession hoz létre.
2. Később például különböző napon, az alkalmazás megkeresi ezeket egy új CloudSpatialAnchorSession (például a Forráshorgony A) a horgonyok egyikét.
3. A felhasználó mutatja be egy új helyre az alapul szolgáló MR/AR platform továbbra is nyomon követhetik a felhasználót.
4. A felhasználó hoz létre ugyanazon CloudSpatialAnchorSession használja, forráshorgony c horgonyok A, B és C most már csatlakoznak, és ezt a kapcsolatot Azure térbeli horgonyok munkaterheléseire.
5. Ez az eljárás további horgonyok és további előadások idővel továbbra is.

### <a name="verifying-anchor-connections"></a>Jegyzetobjektum kapcsolatok ellenőrzése

Az alkalmazás ellenőrizheti, hogy két kapcsolati alapok egy lekérdezést a közeli kapcsolatok alapjainak kiállításával csatlakoznak. Ha a lekérdezés eredménye a kívánt cél jegyzetobjektum tartalmaz, az alkalmazás még megerősítése, hogy csatlakozik-e a központi jellegűek. Ha azok nem csatlakoznak, az alkalmazás megpróbálhatja a kapcsolat létesítése újra. Az alábbiakban néhány ok, miért horgonyok csatlakozása sikertelen lehet:

1. Az alapul szolgáló MR/AR tracker követési megszakadt a horgonyok kapcsolódás folyamata során.
2. A térbeli horgonyok Azure szolgáltatással való kommunikáció hálózati hiba történt, és a forráshorgony kapcsolat nem őrizhető meg.

### <a name="sample-code"></a>Mintakód

Láthatja, amely bemutatja, hogyan horgonyok csatlakozhat, és hajtsa végre a lekérdezéseket közeli mintakódot. Tekintse meg a [Azure térbeli horgonyok mintaalkalmazások](https://github.com/Azure/azure-spatial-anchors-samples) a Githubon.
