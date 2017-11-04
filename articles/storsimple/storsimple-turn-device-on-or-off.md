---
title: "A StorSimple 8000 series eszköz be- és kikapcsolása |} Microsoft Docs"
description: "Egy új StorSimple eszközön beállítás bekapcsolását, leállt vagy megszakadt power eszköz bekapcsolása és tiltsa le a futó eszközt ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0577c837e0c47ba37a4f586603b0f5b951f1b549
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Kapcsolja be, vagy kapcsolja ki a StorSimple 8000 series eszköz
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple eszköz leállítása nincs szükség a szokásos működésének részeként. Azonban szükség lehet egy új vagy egy eszköz, amelynek le kell állítani a bekapcsolása. Általában a leállás szükséges azokban az esetekben, ahol le kell cserélnie sikertelen hardver, fizikailag helyezze át egy egység, vagy igénybe vehet egy eszköz nem működik. Ez az oktatóanyag leírja bekapcsolása és a különböző alkalmazási helyzetek a StorSimple eszköz leállítás szükséges eljárását.

## <a name="turn-on-a-new-device"></a>Új eszköz bekapcsolása
A StorSimple eszköz bekapcsolása első lépései eltérőek attól függően, hogy-e az eszköz egy 8100 vagy egy 8600 modellt. A 8100 rendelkezik egy önálló elsődleges ház, mivel a 8600 rendelkező elsődleges ház és egy EBOD ház kettős-ház eszköz. A részletes lépéseket, mind a két modellben a következő szakaszok ismertetnek.

* [Új eszköz csak az elsődleges ház](#new-device-with-primary-enclosure-only)
* [Új eszköz EBOD ház](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Új eszköz csak az elsődleges ház
A StorSimple 8100 modell a ház egyetlen eszközről szó. Az eszköz redundáns energia- és hűtési modulok (PCMs) tartalmazza. Mindkét PCMs telepítve legyen, és csatlakozik a különböző áramforrásokból magas rendelkezésre állásának biztosításához.

A következő lépésekkel bekapcsolási bekábelezésére.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Eszköz telepítésének befejezése és kábelek mennyiségét utasításokat, látogasson el [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md). Győződjön meg arról, hogy pontosan hajtsa végre az utasításokat.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Új eszköz EBOD ház
A StorSimple 8600 modellnek egy elsődleges ház- és egy EBOD ház. Ehhez a soros csatlakozású SCSI (SAS) kapcsolódási és power együtt kábel egységeket.

Ez az eszköz beállításához először, SAS kábelek először hajtsa végre a lépést, és fejezze be az energiagazdálkodási kábelek lépéseit.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Eszköz telepítésének befejezése és kábelek mennyiségét utasításokat, látogasson el [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md). Győződjön meg arról, hogy pontosan hajtsa végre az utasításokat.

## <a name="turn-on-a-device-after-shutdown"></a>Egy eszköz bekapcsolása leállást követően
A StorSimple eszköz bekapcsolása után le lett állítva a lépései eltérőek attól függően, hogy-e az eszköz egy 8100 vagy egy 8600 modell. A 8100 rendelkezik egy önálló elsődleges ház, mivel a 8600 rendelkező elsődleges ház és egy EBOD ház kettős-ház eszköz.

* [Csak az elsődleges ház eszköz](#device-with-primary-enclosure-only)
* [EBOD ház eszköz](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Csak az elsődleges ház eszköz
A leállás után az alábbi eljárás segítségével kapcsolja be a StorSimple eszköz elsődleges ház és nem EBOD ház.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Csak egy elsődleges ház rendelkező eszköz bekapcsolása
1. Győződjön meg arról, hogy mindkét működik vált a power, és hűtési modulok (PCMs) OFF állásban van. Ha a kapcsolók nem OFF állásban, fordítsa meg őket a OFF állásba, és várja meg a fény ugorhat.
2. A mindkét PCMs az ON-pozíció az energiagazdálkodási kapcsolók átállításával kapcsolja be az eszközt. Az eszköz bekapcsolása kell.
3. Ellenőrizze a következőket annak ellenőrzéséhez, hogy az eszköz teljes mértékben meg:
   
   1. Az OK gombra LED mindkét PCM modulokat a rendszer zöld.
   2. Az állapot LED mindkét tartományvezérlőn teli zöld.
   3. A tartományvezérlők egyik kék LED villogó, amely azt jelzi, hogy a vezérlő aktív.
      
      Ha ezek a feltételek nem teljesülnek, majd az eszköz nincs kifogástalan állapotban. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>EBOD ház eszköz
A leállás után az alábbi eljárás segítségével kapcsolja be a StorSimple eszköz elsődleges ház és egy EBOD ház. Pontosan leírt sorrendben végrehajtani a műveletet. Elmulasztása adatvesztést eredményezhet.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Egy elsődleges és egy EBOD ház eszköz bekapcsolása
1. Győződjön meg arról, hogy a EBOD ház csatlakozik-e az elsődleges ház. További információkért lásd: [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md).
2. Győződjön meg arról, hogy a energia- és hűtési modulok (PCMs) EBOD mind az elsődleges ház megfelelő jogkörrel OFF. Ha a kapcsolók nem OFF állásban, fordítsa meg őket a OFF állásba, és várja meg a fény ugorhat.
3. Kapcsolja be a EBOD ház első által az energiagazdálkodási kapcsolók mindkét PCMs az ON-pozíció a tükrözés. A PCM LED zöld kell lennie. Ez egységen egy zöld EBOD vezérlő LED azt jelzi, hogy a EBOD ház meg.
4. Kapcsolja be az elsődleges ház által az energiagazdálkodási kapcsolók mindkét PCMs az ON-pozíció a tükrözés. A teljes rendszer most kell lennie.
5. Ellenőrizze, hogy a biztonsági Társítások LED zöld, amely biztosítja, hogy a kapcsolatot a EBOD ház és az elsődleges ház helyes.

## <a name="turn-on-a-device-after-a-power-loss"></a>Egy eszköz bekapcsolása után egy áramellátás megszakadása miatt
A StorSimple eszköz egy áramkimaradás vagy megszakítás leállíthat. A áramkimaradás esetén fordulhat elő, a áramforrások egyik vagy mindkét áramforrással rendelkezik. A helyreállítási lépések eltérőek attól függően, hogy az eszköz egy 8100-as vagy 8600 modellt. A 8100 rendelkezik egy önálló elsődleges ház, mivel a 8600 rendelkező elsődleges ház és egy EBOD ház kettős-ház eszköz. Ez a szakasz ismerteti az egyes forgatókönyvek esetében a helyreállítási folyamatot.

* [Csak az elsődleges ház eszköz](#8100)
* [EBOD ház eszköz](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Csak az elsődleges ház eszköz<a name="8100">
A rendszer továbbra is a normál működés, egy a áramforrással rendelkezik, az áramellátás megszakadása esetén. Azonban az eszköz magas rendelkezésre állásának biztosításához, állítsa vissza az energiagazdálkodási a tápegység amint lehetséges.

Ha egy áramkimaradás vagy mindkét áramforrással rendelkezik power szolgáltatásainak megszakítása nélkül, a rendszer le fog állni rendezett és szabályozott módon. A power helyreáll, amikor a rendszer automatikusan kapcsol be.

### <a name="device-with-ebod-enclosure-a-name8600"></a>EBOD ház eszköz<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Adjon egy működik az áramellátás megszakadása miatt
A rendszer továbbra is a normál működés, egy a áramforrással rendelkezik, az elsődleges ház vagy a EBOD ház az áramellátás megszakadása esetén. Azonban ahhoz, hogy az eszköz magas rendelkezésre állású, állítsa vissza a power a tápegység a lehető leghamarabb.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Az elsődleges és EBOD ház mindkét áramforrással rendelkezik az áramellátás megszakadása miatt
Ha egy áramkimaradás vagy mindkét áramforrással rendelkezik power szolgáltatásainak megszakítása nélkül, a EBOD ház azonnal leáll, és az elsődleges ház rendezett és szabályozott módon le fog állni. Energiagazdálkodási helyreáll, ha a készülék automatikusan elindul.

Ha hatványra manuálisan kapcsolható ki, majd a következő lépéseket a rendszer visszaállítása a power.

1. Kapcsolja be a EBOD ház.
2. Után a EBOD ház van kapcsolva, kapcsolja be az elsődleges ház.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>A mindkét áramforrással rendelkezik, a EBOD ház az áramellátás megszakadása miatt
A kábelek beállításakor győződjön meg róla, hogy a EBOD soha nem kapcsolódik önmagában egy külön PDU. Ha a EBOD és elsődleges ház meghibásodik, egy időben, a rendszer állítja helyre.

Ha csak a EBOD ház mindkét áramforrások nem sikerül, a rendszer nem automatikusan helyre fog. A következő lépéseket a rendszer be-és visszaállítja a kifogástalan állapotban:

1. Ha az elsődleges ház be van kapcsolva, kapcsolja ki a teljesítmény és a hűtési modulok (PCMs).
2. Várjon, amíg a rendszer le kell állítania néhány percig.
3. Kapcsolja be a EBOD ház.
4. Után a EBOD ház van kapcsolva, kapcsolja be az elsődleges ház.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Egy eszköz bekapcsolása után az elsődleges és EBOD ház kapcsolat elvész.
A készenléti vezérlő és a megfelelő EBOD vezérlő megszakad a kapcsolat, ha az eszköz továbbra is működik. A rendszer aktív vezérlő és a megfelelő EBOD vezérlő közötti kapcsolat nem vesztek el, ha feladatátvételi jöjjön létre, és az eszköz is működnek a normál.

Ha mindkét soros csatlakozású SCSI (SAS) kábelek eltávolítása, vagy a kapcsolatot a EBOD ház és az elsődleges ház daraboltak van, az eszköz nem fog működni. Ezen a ponton a következő lépésekkel.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Kapcsolat megszakadása után az eszköz bekapcsolása
1. Az eszköz vissza a hozzáférést.
2. Ha a biztonsági Társítások a EBOD ház és az elsődleges ház közötti kapcsolat megszakad, a EBOD ház összes SAS lane LED lesz.
3. Állítsa le a teljesítmény és a hűtési modulok (PCMs) a EBOD ház és az elsődleges ház.
4. Várjon, amíg mind a ház hátulján lévő összes fény kikapcsolása.
5. A SAS-kábel be újra, és győződjön meg arról, hogy van-e a megfelelő kapcsolat a EBOD ház és az elsődleges ház között.
6. Kapcsolja be a EBOD ház először mindkét PCM átállításával vált, tovább pozícióját.
7. Gondoskodjon arról, hogy a EBOD ház úgy, hogy a zöld LED ON értékre állítva.
8. Kapcsolja be az elsődleges ház.
9. Gondoskodjon arról, hogy az elsődleges ház úgy, hogy a vezérlő zöld LED ON értékre állítva.
10. Ellenőrizze, hogy a ház EBOD kapcsolatot az elsődleges ház jó úgy, hogy a biztonsági Társítások lane LED (négy EBOD vezérlőnként)-e az összes ON.

> [!IMPORTANT]
> Ha a SAS-kábel hibás vagy a kapcsolatot a EBOD ház és az elsődleges ház nem helyes, ha a rendszer bekapcsolja, helyreállítási módba kerül. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) Ha ez történik.


## <a name="turn-off-a-running-device"></a>Egy futó eszközök kikapcsolása
Fut a StorSimple eszköz esetleg le kell állítani, ha áthelyezik, végrehajtott nem működik, vagy van egy nem megfelelően működő összetevője, amely le kell cserélni. A lépések eltérőek attól függően, hogy a StorSimple eszköz-e egy 8100 vagy egy 8600 modellt. A 8100 rendelkezik egy önálló elsődleges ház, mivel a 8600 rendelkező elsődleges ház és egy EBOD ház kettős-ház eszköz. Ez a szakasz részletesen bemutatja egy futó eszközt le kell állítania.

* [Elsődleges ház eszköz](#8100a)
* [EBOD ház eszköz](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Elsődleges ház eszköz<a name="8100a">
A leállítás előtt az eszköz zavartalan és szabályozott módon ezt megteheti a klasszikus Azure portálon keresztül a Windows PowerShell vagy a StorSimple. 

> [!IMPORTANT]
> Nem állnak le egy futó eszköz a főkapcsoló hátulján olvasható az eszköz használatával.
> 
> Az eszköz leállítása, előtt ellenőrizze, hogy az eszköz összetevők kifogástalan. A klasszikus Azure portálon lépjen a **eszközök** > **karbantartási** > **hardverállapot**, és győződjön meg arról, hogy az összes összetevő állapota a rendszer zöld. Ez igaz csak egy kifogástalan rendszer. Ha a rendszer épp most állítja le a hibás összetevő lecseréléséhez jelenik meg (piros) sikertelen vagy csökkentett teljesítményű megfelelő összetevőjének (sárga) állapota a **hardverállapot**.
> 
> 

Ha hozzáfér a Windows PowerShell StorSimple vagy a klasszikus Azure portálra, kövesse a [állítsa le a StorSimple eszköz](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>EBOD ház eszköz<a name="8600a">
> [!IMPORTANT]
> Az elsődleges ház és a EBOD ház leállítása előtt gondoskodjon arról, hogy az eszköz összetevők kifogástalan. Az Azure-portálon lépjen a **eszközök** > **figyelő** > **hardver állapotának**, és ellenőrizze, hogy minden összetevő kifogástalan.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>A leállítás előtt EBOD ház futó eszközök
1. Kövesse a felsorolt lépéseket [állítsa le a StorSimple eszköz](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) az elsődleges ház.
2. Miután az elsődleges ház leállítja, állítja le a EBOD tükrözés, energia- és hűtési modul (PCM) kapcsolók ki.
3. Annak ellenőrzéséhez, hogy a EBOD le van állítva, ellenőrizze, hogy az összes fény a EBOD ház hátulján olvasható ki.

> [!NOTE]
> A SAS-kábel, amely segítségével a EBOD ház csatlakozzon az elsődleges ház tilos megvonni amíg után a rendszer leállítása.

## <a name="next-steps"></a>Következő lépések
[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) Ha problémába ütközik a bekapcsolását, vagy a StorSimple eszköz leállítása.

