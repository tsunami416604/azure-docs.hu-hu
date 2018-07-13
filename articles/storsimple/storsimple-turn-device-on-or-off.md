---
title: A StorSimple 8000 sorozatú eszköz be- és kikapcsolása |} A Microsoft Docs
description: Azt ismerteti, hogyan kapcsolja be egy új StorSimple-eszköz, kapcsolja be egy eszközt, hogy leállította vagy áramellátása megszűnt, és kapcsolja ki a futó eszköz.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95fd00608be9cfafb4c703c32ec3ed4713855ca5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670966"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Kapcsolja be, vagy kapcsolja ki a StorSimple 8000 sorozatú eszköz

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple-eszköz leáll, nem szükséges normál rendszer művelet részeként. Azonban szüksége lehet, hogy kapcsolja be egy új vagy egy eszköz, akinek le kell állítani. Általában egy leállítási szükséges azokban az esetekben, amelyben le kell cserélnie sikertelen hardver, fizikailag helyezze át egy egységet, vagy igénybe vehet egy eszköz nem működik. Ebben az oktatóanyagban bekapcsolása és a leállítás a StorSimple-eszköz különböző helyzetekben szükséges eljárását ismerteti.

## <a name="turn-on-a-new-device"></a>Új eszköz bekapcsolása
Először a StorSimple eszköz bekapcsolása lépései eltérőek attól függően, hogy az eszköz egy 8100-as vagy egy 8600-as modell. A 8100-as rendelkezik egy önálló elsődleges ház, mivel a 8600-as elsődleges ház és a egy EBOD ház kettős-lemezház eszköz. A részletes lépéseket, mind a két modellben a következő szakaszokban terjed ki.

* [Új eszköz csak az elsődleges ház](#new-device-with-primary-enclosure-only)
* [Új eszköz EBOD ház](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Új eszköz csak az elsődleges ház
A StorSimple 8100-as modell egyetlen lemezház eszköz. Az eszköz redundáns energiagazdálkodási és hűtéssel modulok (PCMs) tartalmaz. Mindkét PCMs telepítve legyen, és csatlakozik a különböző áramforrásokhoz magas rendelkezésre állás biztosítása érdekében.

A következő lépésekkel bekábelezése teljesítményhez.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Eszközbeállítások végrehajtása és a kábelezést utasításokat, Ugrás [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md). Győződjön meg arról, pontosan kövesse az utasításokat.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Új eszköz EBOD ház
A StorSimple 8600-as modell elsődleges ház és a egy EBOD ház is van. Ehhez a soros csatlakozású SCSI (SAS) kapcsolódási és power együtt bekábelezte egységeket.

Az eszköz beállítása az első alkalommal, amikor a SAS kábelezést először hajtsa végre a lépéseket, és végezze el a kábelezést power.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Eszközbeállítások végrehajtása és a kábelezést utasításokat, Ugrás [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md). Győződjön meg arról, pontosan kövesse az utasításokat.

## <a name="turn-on-a-device-after-shutdown"></a>Egy eszköz bekapcsolása leállást követően
Után le lett állítva a StorSimple eszköz bekapcsolása lépései eltérőek attól függően, hogy az eszköz egy 8100-as vagy egy 8600-as modell. A 8100-as rendelkezik egy önálló elsődleges ház, mivel a 8600-as elsődleges ház és a egy EBOD ház kettős-lemezház eszköz.

* [Az eszköz csak az elsődleges ház](#device-with-primary-enclosure-only)
* [EBOD ház eszköz](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Az eszköz csak az elsődleges ház
A leállítása után az alábbi eljárás segítségével kapcsolja be a StorSimple-eszköz elsődleges ház és nem EBOD ház.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Egy eszköz csak egy elsődleges ház bekapcsolása
1. Ellenőrizze, hogy a teljesítmény bekapcsol mindkét Power, és hűtéssel modulok (PCMs) OFF állásban van. Ha a kapcsolók nem OFF állásban, fordítsa meg őket a OFF helyre, és várjon, amíg a jelzőfény ugorhat.
2. Kapcsolja be az eszközt a tükrözés, a kiemelt kapcsolók a mindkét PCMs ON helyére. Az eszköz kapcsolja.
3. Annak ellenőrzéséhez, hogy az eszköz teljes mértékben a a következőket ellenőrizze:
   
   1. Az OK LED-ek mindkét PCM modulokat a rendszer zöld.
   2. Mindkét vezérlő állapota LED-ek szilárd zöld.
   3. A kék LED az egyik vezérlő villogó, amely azt jelzi, hogy a vezérlő aktív.
      
      Ha ezek a feltételek nem teljesülnek, majd az eszköz állapota nem megfelelő. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>EBOD ház eszköz
A leállítása után az alábbi eljárás segítségével kapcsolja be a StorSimple-eszköz elsődleges ház és a egy EBOD ház. Hajtsa végre az egyes lépések sorrendben a pontosan leírtak szerint. Ezt az adatvesztést eredményezhet.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Egy elsődleges és a egy EBOD ház eszköz bekapcsolása
1. Győződjön meg arról, hogy a EBOD ház csatlakozik-e az elsődleges ház. További információkért lásd: [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md).
2. Győződjön meg arról, hogy a teljesítmény és a hűtési modulok (PCMs) az EBOD és az elsődleges ház OFF állásban van. Ha a kapcsolók nem OFF állásban, fordítsa meg őket a OFF helyre, és várjon, amíg a jelzőfény ugorhat.
3. Az EBOD ház-es először tükrözés, hatékonysága által a mindkét PCMs vált, amennyiben az ON pozíciója. A PCM LED-ek zöld kell lennie. Egy zöld az EBOD-vezérlő LED ezt a kiegészítő egységet a azt jelzi, hogy a EBOD ház be van-e.
4. Kapcsolja be az elsődleges ház által a kiemelt kapcsolók visszalapozok a mindkét PCMs ON helyére. A teljes rendszer most meg kell lennie.
5. Ellenőrizze, hogy a SAS LED-ek zöld, gondoskodik arról, hogy készen áll-e a kapcsolat a EBOD ház és az elsődleges ház között.

## <a name="turn-on-a-device-after-a-power-loss"></a>Egy az áramellátás megszakadása után eszköz engedélyezése
A StorSimple eszköz egy áramkimaradás vagy megszakítás leállíthatja. A teljesítmény kimaradás fordulhat elő, a tápegységek egyik vagy mindkét tápegységek. A helyreállítási lépések eltérőek aszerint, hogy az eszköz egy 8100-as vagy 8600-as modell. A 8100-as rendelkezik egy önálló elsődleges ház, mivel a 8600-as elsődleges ház és a egy EBOD ház kettős-lemezház eszköz. Ez a szakasz ismerteti az egyes forgatókönyvek helyreállítási eljárását.

* [Az eszköz csak az elsődleges ház](#8100)
* [EBOD ház eszköz](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Az eszköz csak az elsődleges ház <a name="8100">
A rendszer továbbra is a normál működés, az egyik hozzá tartozó tápegységek áramkimaradás esetén. Azonban ahhoz, hogy az eszköz magas rendelkezésre állású, állítsa vissza a power az áramellátás minél hamarabb.

Ha egy áramkimaradás vagy áramkimaradás a mindkét tápegységek, a rendszer le fog állni a szabályos és szabályozott módon. Ha a teljesítmény helyreáll, a rendszer automatikusan bekapcsolása.

### <a name="device-with-ebod-enclosure-a-name8600"></a>EBOD ház eszköz <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>A egy kapacitást az áramellátás megszakadása fogja tartalmazni.
A rendszer továbbra is a normál működés, a tápegységek az elsődleges ház- és a EBOD ház az egyik az áramellátás megszakadása esetén. Azonban ahhoz, hogy az eszköz magas rendelkezésre állású, állítsa vissza a power, az áramellátás minél hamarabb.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Az elsődleges, a házak EBOD mindkét tápegységek az áramellátás megszakadása
Ha egy áramkimaradás vagy áramkimaradás a mindkét tápegységek, a EBOD ház azonnal leállítja, és az elsődleges ház le fog állni a szabályos és szabályozott módon. Amikor power helyreáll, a készülék automatikusan elindul.

Ha a teljesítmény manuálisan váltott, majd a következő lépésekkel power visszaállítani a rendszer.

1. Kapcsolja be a EBOD ház.
2. Miután a EBOD ház a, kapcsolja be az elsődleges ház.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Az EBOD ház mindkét tápegységek Power veszteség
Ha beállította a kábelek, biztosítania kell, hogy a EBOD soha nem csatlakozik önmagában egy külön PDU. Ha a EBOD és elsődleges ház nem egy időben, a rendszer állítja helyre.

Ha csak a EBOD ház mindkét tápegységek meghiúsul, a rendszer nem tudja automatikusan a rendszer. A következő lépésekkel kapcsolja be a rendszer, és állítsa vissza megfelelő állapotba való:

1. Ha az elsődleges ház be van kapcsolva, kapcsolja ki a Power és a hűtési modulok (PCMs).
2. Várjon néhány percet, amíg a rendszer leállítása.
3. Kapcsolja be a EBOD ház.
4. Miután a EBOD ház a, kapcsolja be az elsődleges ház.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Egy eszköz bekapcsolása után az elsődleges és a EBOD ház kapcsolat a streamcsatornával
Ha megszakad a kapcsolat a készenléti állapotban lévő vezérlőnek és a megfelelő az EBOD-vezérlő között, az eszköz továbbra is működik. A rendszer aktív vezérlő és a megfelelő az EBOD-vezérlő közötti kapcsolat megszakad, ha feladatátvétel történjen, és az eszköz továbbra is a megszokott módon működnek.

Ha mindkét soros csatlakozású SCSI (SAS) kábelek el lesznek távolítva, vagy a EBOD ház és az elsődleges ház közötti kapcsolat daraboltak van, az eszköz nem fog működni. Ezen a ponton a következő lépésekkel.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Az eszköz bekapcsolása, miután a kapcsolat a streamcsatornával
1. A háttérben az eszköz eléréséhez.
2. Az EBOD ház és az elsődleges ház között a SAS kábeles kapcsolata megszakad, ha az összes SAS lane LED-ek a EBOD tárolóeszközön lesz kapcsolva.
3. Állítsa le a Power és a hűtési modulok (PCMs) a EBOD ház és az elsődleges ház.
4. Várjon, amíg mindkét a ház-jének összes lámpa kikapcsolása.
5. Helyezze be újra a SAS-kábel, és győződjön meg arról, hogy nincs-e a megfelelő kapcsolat a EBOD ház és az elsődleges ház között.
6. Kapcsolja be a EBOD ház első mindkét PCM kapcsolók átállításával ON helyére.
7. Győződjön meg arról, hogy a EBOD ház annak ellenőrzéséhez, hogy a zöld LED ON szerint ki van kapcsolva.
8. Kapcsolja be az elsődleges ház.
9. Győződjön meg arról, hogy az elsődleges ház annak ellenőrzéséhez, hogy a tartományvezérlő zöld LED ON szerint ki van kapcsolva.
10. Ellenőrizze, hogy a EBOD ház-kapcsolatot az elsődleges ház jó szerint győződjön meg róla, hogy a SAS lane LED-ek (EBOD-vezérlő 4) minden ON.

> [!IMPORTANT]
> A SAS-kábel hibás, vagy a kapcsolat a EBOD ház és az elsődleges ház között nem nem jó, ha bekapcsolja a rendszer, ha azt lépnek helyreállítási módba. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) Ha ez történik.


## <a name="turn-off-a-running-device"></a>Futó eszköz letiltása
Egy futó StorSimple-eszköz le kell állítani, ha azt áthelyezik, kivehetők a szolgáltatás, vagy van egy nem megfelelően működő összetevője, amely kell cserélni kell. A lépések eltérnek attól függően, hogy-e a StorSimple-eszközt egy 8100-as vagy egy 8600-as modell. A 8100-as rendelkezik egy önálló elsődleges ház, mivel a 8600-as elsődleges ház és a egy EBOD ház kettős-lemezház eszköz. Ez a szakasz részletesen ismerteti egy futó eszköz leállítása lépéseit.

* [Eszköz elsődleges ház](#8100a)
* [EBOD ház eszköz](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Eszköz elsődleges ház <a name="8100a">
Az a szabályos és szabályozott módon állítsa le az eszközt, ezt megteheti az Azure Portalon keresztül, vagy a Windows PowerShell storsimple-höz készült. 

> [!IMPORTANT]
> Ne állítsa le a futó eszköz főkapcsoló az eszköz-jének használatával.
> 
> Mielőtt az eszköz a Leállítás, ellenőrizze, hogy az eszköz-összetevők kifogástalan állapotú. Az Azure Portalon lépjen **eszközök** > **figyelő** > **hardverállapot**, és győződjön meg arról, hogy az összes összetevő állapota zöld. Ez a csak kifogástalan állapotú rendszerhez. Ha a rendszer pedig leáll le a cserélje le a hibás összetevő, hibás (piros), vagy csökkentett teljesítményű megfelelő összetevőjének (sárga) állapot a **hardverállapot**.
> 
> 

Ha hozzáfér a Windows PowerShell StorSimple vagy az Azure Portalon, kövesse a [állítsa le a StorSimple eszköz](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>EBOD ház eszköz <a name="8600a">
> [!IMPORTANT]
> Az elsődleges ház és a EBOD ház leállítása előtt győződjön meg arról, hogy az eszköz-összetevők kifogástalan állapotú. Az Azure Portalon lépjen **eszközök** > **figyelő** > **hardverállapot**, és ellenőrizze, hogy az összes összetevő kifogástalan állapotú.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>EBOD ház futó eszköz leállítása
1. Kövesse a felsorolt lépéseket [állítsa le a StorSimple eszköz](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) az elsődleges ház számára.
2. Az elsődleges ház leáll, miután leállította; a EBOD tükrözés, teljesítmény és a hűtési modul (PCM) kapcsolók ki.
3. Győződjön meg arról, hogy a EBOD le lett állítva, ellenőrizze, hogy a EBOD ház-jének összes lámpa van-e kapcsolva.

> [!NOTE]
> A EBOD ház csatlakozni az elsődleges ház használt SAS-kábel csomóponttól sem, amíg, miután a rendszer leállítása.

## <a name="next-steps"></a>További lépések
[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) Ha problémákat, amikor ne tudják bekapcsolni a, vagy a StorSimple eszköz leállítása.

