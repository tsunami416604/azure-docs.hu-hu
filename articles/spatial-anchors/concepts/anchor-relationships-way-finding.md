---
title: Horgonykapcsolatok és útkeresés
description: Ismerje meg a horgonykapcsolatok mögötti koncepcionális modellt. Ismerje meg, hogyan lehet egy helyen belül horgonyokat csatlakoztatni, és a Nearby API-t használni egy útkeresési forgatókönyv teljesítéséhez.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270605"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Kapcsolatok és útkeresés rögzítése az Azure Spatial Anchors ban

Horgonykapcsolatok használatával összekapcsolt horgonyokat hozhat létre egy szóközben, majd az alábbihoz hasonló kérdéseket tehet fel:

* Vannak horgonyok a közelben?
* Milyen messze vannak?

## <a name="examples"></a>Példák

Az alábbi esetekben használhat csatlakoztatott horgonyokat:

* A munkavállalónak el kell végeznie egy feladatot, amely magában foglalja az ipari gyár különböző helyszíneinek meglátogatását. A gyár ban térbeli horgonyok minden helyen. A HoloLens vagy a mobilalkalmazás segítségével a dolgozó egyik helyről a másikra irányíthatja a dolgozót. Az alkalmazás először a közeli térbeli horgonyokat kéri, majd a következő helyre irányítja a dolgozót. Az alkalmazás vizuálisan mutatja az általános irányt és a távolságot a következő helyre.

* A múzeum térbeli horgonyokat hoz létre a nyilvános bemutatókon. Ezek a horgonyok együtt egyórás túrát alkotnak a múzeum alapvető nyilvános bemutatóiról. A látogatók egy nyilvános kiállításon megnyithatják a múzeum vegyes valóság alkalmazását a mobileszközükön. Ezután pont a telefon kamera körül a teret, hogy az általános irányát és távolságát a többi nyilvános kijelzők a túra. Ahogy a felhasználó egy nyilvános kijelző felé sétál, az alkalmazás frissíti az általános irányt és távolságot, hogy segítse a felhasználót.

## <a name="set-up-way-finding"></a>Útkeresés beállítása

Az útkereső alkalmazás, amely a horgonyok közötti irányvonal irányt és távolságot használja útmutatásként, útkereső módon *történik.* Az útkeresés különbözik a turn-by-turn navigációtól. A turn-by-turn navigáció, a felhasználók köré falak, ajtók, és a padlók között. Az útkereséssel a felhasználó tippeket kap a cél általános irányáról. De következtetés vagy a tudás a tér is segít a felhasználónak navigálni a szerkezet a cél.

A megoldáskereséshez először készítsen elő egy helyet a felhasználói élményszámára, és fejlesszen ki egy olyan alkalmazást, amelyet a felhasználók interakcióba lépnek. Ezek a fogalmi lépések:

1. **Tervezze meg a helyet:** Döntse el, hogy a térmely helyei lesznek az útkereső élmény részei. Forgatókönyveinkben a gyárfelügyelő vagy a múzeumi túrakoordinátor döntheti el, hogy mely helyszíneket foglalja bele az útkereső élménybe.
2. **Connect horgonyok**: Látogasson el a kiválasztott helyeken, hogy hozzon létre térbeli horgonyok. Ezt a végfelhasználói alkalmazás felügyeleti módban vagy teljesen egy másik alkalmazásban teheti meg. Az egyes horgonyok a többihez kapcsolódnak vagy kapcsolódnak. A szolgáltatás fenntartja ezeket a kapcsolatokat.
3. **A végfelhasználói élmény indítása**: A felhasználók az alkalmazás futtatásával megkereshetik a horgonyt, amely bármelyik kiválasztott helyen lehet. A teljes tervnek meg kell határoznia azokat a helyeket, ahol a felhasználók beírhatják a felhasználói felületet.
4. **Közeli horgonyok keresése**: Miután a felhasználó talált egy horgonyt, az alkalmazás kérheti a közeli horgonyokat. Ez az eljárás az eszköz és a horgonyok közötti pózt ad vissza.
5. **Útmutató a felhasználó**: Az alkalmazás használhatja a jelent, hogy minden ilyen horgonyok, hogy útmutatást adjon a felhasználó általános irányát és távolságát. Előfordulhat például, hogy az alkalmazás kameracsatornája egy ikont és egy nyilat jelenít meg, amely az egyes lehetséges célpontokat ábrázolja, ahogy az alábbi képen is látható.
6. **Az útmutató finomítása:** A felhasználó sétái közben az alkalmazás rendszeres időközönként kiszámíthat egy új pózt az eszköz és a célhorgony között. Az alkalmazás továbbra is finomítja az okat a tippeket, amelyek segítségével a felhasználó megérkezik a célhoz.

    ![Példa arra, hogy egy alkalmazás hogyan jelenítheti meg az útkeresésre vonatkozó útmutatást](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Horgonyok csatlakoztatása

A megoldáskereséshez először a horgonyokat a kiválasztott helyekre kell helyeznie. Ebben a szakaszban feltételezzük, hogy az alkalmazás rendszergazdája már befejezte ezt a munkát.

### <a name="connect-anchors-in-a-single-session"></a>Horgonyok csatlakoztatása egyetlen munkamenetben

Horgonyok csatlakoztatása:

1. Sétáljon az első helyre, és hozzon létre A horgonyt egy CloudSpatialAnchorSession használatával.
2. Sétáljon a második helyre. Az alapul szolgáló MR/AR platform nyomon követi a mozgást.
3. Hozzon létre B horgonyt ugyanazzal a CloudSpatialAnchorSession-rel. Az A és B horgonyok most már csatlakoztatva vannak. A Térbeli horgonyok szolgáltatás fenntartja ezt a kapcsolatot.
4. Folytassuk az eljárást a többi horgonyesetében.

### <a name="connect-anchors-in-multiple-sessions"></a>Horgonyok csatlakoztatása több munkamenetben

Térbeli horgonyok több munkamenethez is csatlakoztathatók. Ezzel a módszerrel egyszerre hozhat létre és csatlakoztathat néhány horgonyt, majd később további horgonyokat hozhat létre és csatlakoztathat.

Horgonyok több munkamenethez való csatlakoztatása:

1. Az alkalmazás létrehoz néhány horgonyt egy CloudSpatialAnchorSession.The app creates some anchors in one CloudSpatialAnchorSession.
2. Egy másik időpontban az alkalmazás megkeresi az egyik ilyen horgonyok (például Anchor A) egy új CloudSpatialAnchorSession használatával.
3. Sétáljon egy új helyre. A mögöttes vegyes valóság vagy kiterjesztett valóság platform nyomon követi a mozgást.
4. Hozzon létre horgony t ugyanazzal a CloudSpatialAnchorSession használatával. Az A, B és C horgonyok most már csatlakoztatva vannak. A Térbeli horgonyok szolgáltatás fenntartja ezt a kapcsolatot.

Ezt az eljárást több horgony és több munkamenet esetén folytathatja.

### <a name="verify-anchor-connections"></a>Horgonykapcsolatok ellenőrzése

Az alkalmazás ellenőrizheti, hogy két horgony csatlakozik-e a közeli horgonyok lekérdezésének kibocsátásával. Ha a lekérdezés eredménye tartalmazza a célhorgonyt, a horgonykapcsolat ellenőrzése lesz. Ha a horgonyok nincsenek csatlakoztatva, az alkalmazás megpróbálhatja újra csatlakoztatni őket.

Íme néhány ok, amiért a horgonyok nem tudnak csatlakozni:

* A mögöttes vegyes valóság vagy kiterjesztett valóság platform elvesztette nyomon követését a horgonyok összekapcsolásának folyamata során.
* A Spatial Anchors szolgáltatással való kommunikáció során fellépő hálózati hiba miatt a horgonykapcsolat nem tartható meg.

### <a name="find-sample-code"></a>Mintakód keresése

A horgonyok csatlakoztatását és a közeli lekérdezéseket bemutató mintakód megkereséséhez olvassa el [a Térbeli horgonyok mintaalkalmazások ban](https://github.com/Azure/azure-spatial-anchors-samples)található.
