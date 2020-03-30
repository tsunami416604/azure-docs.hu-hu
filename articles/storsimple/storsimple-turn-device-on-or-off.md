---
title: A StorSimple 8000 sorozatú eszköz be- és kikapcsolása
description: A cikk bemutatja, hogyan kapcsolhatja be az új StorSimple-eszközt, hogyan kapcsolhatja be a leállított vagy áramkimaradást okozó eszközt, és hogyan kapcsolhatja ki a futó eszközt.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254624"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz be- és kikapcsolása

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple-eszközök leállítása nem szükséges a normál rendszerműködés részeként. Előfordulhat azonban, hogy be kell kapcsolnia egy új eszközt vagy egy leállítandó eszközt. Általában le kell állítani az eseményeket olyan esetekben, amikor a meghibásodott hardvert ki kell cserélni, fizikailag át kell helyezni e egy egységet, vagy ki kell venni egy eszközt a szolgáltatásból. Ez az oktatóanyag ismerteti a StorSimple-eszköz különböző esetekben történő be- és leállításához szükséges eljárást.

## <a name="turn-on-a-new-device"></a>Új eszköz bekapcsolása
A StorSimple-eszközök első bekapcsolásának lépései attól függően változnak, hogy az eszköz 8100-as vagy 8600-as típusú modellről van-e szó. A 8100 egyetlen elsődleges házzal rendelkezik, míg a 8600 egy kétházas eszköz, elsődleges házzal és EBOD házzal. A két modell részletes lépéseit a következő szakaszok ismertetik.

* [Új eszköz csak elsődleges burkolattal](#new-device-with-primary-enclosure-only)
* [Új eszköz EBOD házzal](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Új eszköz csak elsődleges burkolattal
A StorSimple 8100 modell egyetlen ház eszköz. A készülék redundáns táp- és hűtőmodulokat (PCM-eket) tartalmaz. A magas rendelkezésre állás biztosítása érdekében mindkét PCM-et különböző áramforrásokhoz kell telepíteni és csatlakoztatni.

Hajtsa végre az alábbi lépéseket a kábelezéshez.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> A teljes eszközbeállítási és kábelezési utasításokért válassza [a StorSimple 8100 eszköz telepítése című témakört.](storsimple-8100-hardware-installation.md) Győződjön meg róla, hogy pontosan követi az utasításokat.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Új eszköz EBOD házzal
A StorSimple 8600 modell rendelkezik egy elsődleges ház és egy EBOD ház. Ehhez az egységeket össze kell kapcsolni a soros csatlakoztatott SCSI (SAS) kapcsolathoz és tápellátáshoz.

Az eszköz első beállításakor először hajtsa végre a SAS-kábelezés lépéseit, majd hajtsa végre a kábelezés lépéseit.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> A teljes eszközbeállítási és kábelezési utasításokért válassza [a StorSimple 8600 eszköz telepítése című témakört.](storsimple-8600-hardware-installation.md) Győződjön meg róla, hogy pontosan követi az utasításokat.

## <a name="turn-on-a-device-after-shutdown"></a>Eszköz bekapcsolása leállítás után
A StorSimple-eszköz leállítása utáni bekapcsolásának lépései attól függően változnak, hogy az eszköz 8100-as vagy 8600-as típusú modellről van-e szó. A 8100 egyetlen elsődleges házzal rendelkezik, míg a 8600 egy kétházas eszköz, elsődleges házzal és EBOD házzal.

* [Csak elsődleges burkolattal rendelkező eszköz](#device-with-primary-enclosure-only)
* [Eszköz EBOD házzal](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Csak elsődleges burkolattal rendelkező eszköz
A leállítás után az alábbi eljárással kapcsolja be az elsődleges házzal rendelkező StorSimple-eszközt, és nincs EBOD-ház.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Csak elsődleges burkolattal rendelkező eszköz bekapcsolása
1. Győződjön meg arról, hogy a táp- és a hűtőmodulok (PCM- ek) áramellátása KI állásban van. Ha a kapcsolók nincsenek KI állásban, fordítsa őket OFF állásba, és várja meg, amíg a fények kialszanak.
2. Kapcsolja be a készüléket úgy, hogy mindkét PCM-en bekapcsolja a tápkapcsolókat. A készüléknek be kell kapcsolnia.
3. Ellenőrizze az alábbiakat, hogy az eszköz teljesen be van-e kapcsolva:
   
   1. Mindkét PCM-modul OK LED-je zöld.
   2. Az állapot LED-ek mindkét vezérlőn folyamatos zöld.
   3. Az egyik vezérlő kék LED-je villog, ami azt jelzi, hogy a vezérlő aktív.
      
      Ha a feltételek bármelyike nem teljesül, akkor az eszköz nem kifogástalan. Forduljon [a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)

### <a name="device-with-ebod-enclosure"></a>Eszköz EBOD házzal
A leállítás után az alábbi eljárással kapcsolja be az elsődleges házzal és ebod-házzal rendelkező StorSimple-eszközt. Minden lépést a leírtaknak megfelelően hajtson végre egymás után. Ennek elmulasztása adatvesztéshez vezethet.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Elsődleges és EBOD-házzal rendelkező eszköz bekapcsolása
1. Győződjön meg arról, hogy az EBOD ház csatlakozik az elsődleges ház. További információ: [A StorSimple 8600 eszköz telepítése.](storsimple-8600-hardware-installation.md)
2. Győződjön meg arról, hogy a táp- és hűtőmodulok (PCM-ek) mind az EBOD, mind az elsődleges házon KI állásban vannak. Ha a kapcsolók nincsenek KI állásban, fordítsa őket OFF állásba, és várja meg, amíg a fények kialszanak.
3. Először kapcsolja be az EBOD házat úgy, hogy mindkét PCM-en bekapcsolja a tápkapcsolókat. A PCM LED-eknek zöldnek kell lenniük. Az egységen lévő zöld EBOD vezérlő LED azt jelzi, hogy az EBOD ház be van kapcsolva.
4. Kapcsolja be az elsődleges burkolatot úgy, hogy mindkét PCM-en lévő tápkapcsolót BE állásba fordítja. Az egész rendszernek be kell kapcsolva.
5. Ellenőrizze, hogy a SAS LED-ek zöld, amely biztosítja, hogy a kapcsolat az EBOD ház és az elsődleges ház jó.

## <a name="turn-on-a-device-after-a-power-loss"></a>Áramkimaradás után az eszköz bekapcsolása
Áramkimaradás vagy megszakítás leállíthatja a StorSimple-eszközt. Az áramkimaradás történhet az egyik tápegységen vagy mindkét tápegységen. A helyreállítási lépések attól függően eltérőek, hogy az eszköz 8100-as vagy 8600-as modell. A 8100 egyetlen elsődleges házzal rendelkezik, míg a 8600 egy kétházas eszköz, elsődleges házzal és EBOD házzal. Ez a szakasz az egyes esetek helyreállítási eljárását ismerteti.

* [Csak elsődleges burkolattal rendelkező eszköz](#8100)
* [Eszköz EBOD házzal](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Csak elsődleges burkolattal rendelkező eszköz<a name="8100">
A rendszer folytathatja a normál működést, ha az egyik tápegysége áramkimaradást okoz. A készülék magas rendelkezésre állásának biztosítása érdekében azonban a lehető leghamarabb állítsa vissza a tápegység áramellátását.

Ha mindkét tápegységen áramkimaradás vagy áramkimaradás van, a rendszer rendezetten és ellenőrzött módon fog leállni. Amikor az áramellátás helyreáll, a rendszer automatikusan bekapcsol.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Eszköz EBOD házzal<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Áramkimaradás egy tápegységen
A rendszer folytathatja a normál működést, ha az elsődleges ház on vagy az EBOD ház egyik tápegysége áramkimaradást okoz. A készülék magas rendelkezésre állásának biztosítása érdekében azonban a lehető leghamarabb állítsa vissza a tápegység áramellátását.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Áramkimaradás mind az elsődleges, mind az EBOD házak tápegységein
Ha mindkét tápegységen áramkimaradás vagy áramkimaradás van, az EBOD ház azonnal leáll, és az elsődleges ház rendezett és ellenőrzött módon leáll. Amikor az áramellátás helyreáll, a készülék automatikusan elindul.

Ha a tápellátás manuálisan van kikapcsolva, tegye meg a következő lépéseket a rendszer áramellátásának visszaállításához.

1. Kapcsolja be az EBOD-ház.
2. Miután az EBOD ház be van kapcsolva, kapcsolja be az elsődleges ház.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Áramkimaradás mindkét tápegységen az EBOD házon
A kábelek beállításakor gondoskodnia kell arról, hogy az EBOD soha ne csatlakozzon egyedül egy külön PDU-hoz. Ha az EBOD és az elsődleges ház nem egyszerre, a rendszer helyreáll.

Ha csak az EBOD ház nem működik mindkét tápegységek, a rendszer nem automatikusan helyreáll. A következő lépésekkel kapcsolja be a rendszert, és állítsa vissza kifogástalan állapotúra:

1. Ha az elsődleges ház be van kapcsolva, kapcsolja ki a táp- és hűtőmodulokat (PCM).
2. Várjon néhány percet, amíg a rendszer leáll.
3. Kapcsolja be az EBOD-ház.
4. Miután az EBOD ház be van kapcsolva, kapcsolja be az elsődleges ház.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Eszköz bekapcsolása az elsődleges és az EBOD-ház kapcsolatának megszűnése után
Ha megszakad a kapcsolat a készenléti vezérlő és a megfelelő EBOD vezérlő között, az eszköz továbbra is működik. Ha a rendszer aktív vezérlője és a megfelelő EBOD-vezérlő közötti kapcsolat megszakad, a feladatátvételnek meg kell történnie, és az eszköznek a szokásos módon kell működnie.

Ha mindkét soros csatlakoztatott SCSI (SAS) kábelt eltávolítja, vagy az EBOD ház és az elsődleges ház közötti kapcsolat megszakad, az eszköz leáll. Ezen a ponton hajtsa végre a következő lépéseket.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Az eszköz bekapcsolása a kapcsolat megszakadása után
1. Hozzáférés az eszköz hátuljához.
2. Ha az EBOD ház és az elsődleges ház közötti SAS-kábelkapcsolat megszakad, az EBOD ház összes SAS sávLED-je ki lesz kapcsolva.
3. Állítsa le mind a power és a hűtési modulok (PCMs) az EBOD ház és az elsődleges ház.
4. Várja meg, amíg mindkét ház hátoldalán lévő összes lámpa kikapcsol.
5. Helyezze vissza a SAS-kábeleket, és győződjön meg arról, hogy jó kapcsolat van az EBOD ház és az elsődleges ház között.
6. Először kapcsolja be az EBOD házat úgy, hogy mindkét PCM-kapcsolót BE állásba fordítja.
7. Győződjön meg arról, hogy az EBOD ház be van kapcsolva, ellenőrizze, hogy a zöld LED be van-e kapcsolva.
8. Kapcsolja be az elsődleges burkolatot.
9. Győződjön meg arról, hogy az elsődleges burkolat be van kapcsolva, ellenőrizze, hogy a vezérlő zöld LED-je be van-e kapcsolva.
10. Ellenőrizze, hogy az EBOD ház kapcsolat az elsődleges ház jó-e, ellenőrizze, hogy a SAS sáv LED-ek (négy EBOD vezérlő) mind ON.

> [!IMPORTANT]
> Ha a SAS-kábelek hibásak, vagy az EBOD ház és az elsődleges ház közötti kapcsolat nem jó, amikor bekapcsolja a rendszert, akkor helyreállítási módba lép. Ha ez történik, [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)


## <a name="turn-off-a-running-device"></a>Futó eszköz kikapcsolása
Előfordulhat, hogy egy futó StorSimple-eszközt le kell állítani, ha áthelyezik, üzemen kívül helyezik, vagy hibásan működő összetevővel rendelkezik, amelyet ki kell cserélni. A lépések eltérőek attól függően, hogy a StorSimple eszköz egy 8100 vagy egy 8600 modell. A 8100 egyetlen elsődleges házzal rendelkezik, míg a 8600 egy kétházas eszköz, elsődleges házzal és EBOD házzal. Ez a szakasz a futó eszköz leállításának lépéseit részletezi.

* [Eszköz elsődleges burkolattal](#8100a)
* [Eszköz EBOD házzal](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Eszköz elsődleges burkolattal<a name="8100a">
Az eszköz rendezett és ellenőrzött módon történő leállításához az Azure Portalon vagy a Windows PowerShell storSimple-alapú rendszeren keresztül is megteheti. 

> [!IMPORTANT]
> Ne állítsa le a futó eszközt a készülék hátoldalán található bekapcsológombbal.
> 
> Az eszköz leállítása előtt győződjön meg arról, hogy az eszköz összes összetevője kifogástalan. Az Azure Portalon keresse meg az **Eszközök** > **hardverállapota** > **Hardware health**, és ellenőrizze, hogy az összes összetevő állapota zöld. Ez csak az egészséges rendszerre igaz. Ha a rendszer leállítása egy hibás összetevő cseréje érdekében történik, a **hardverállapota**nem megfelelő (piros) vagy leromlott (sárga) állapot jelenik meg az adott összetevőnél.
> 
> 

Miután hozzáfért a Windows PowerShell for StorSimple vagy az Azure Portal, kövesse a lépéseket [a storSimple-eszközök leállítása.](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Eszköz EBOD házzal<a name="8600a">
> [!IMPORTANT]
> Az elsődleges ház és az EBOD-ház leállítása előtt győződjön meg arról, hogy az eszköz összes összetevője kifogástalan állapotban van. Az Azure Portalon keresse meg az **Eszközök** > **hardverállapota** > **Hardware health**, és ellenőrizze, hogy az összes összetevő kifogástalan állapotú.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Futó eszköz leállítása az EBOD házzal
1. Kövesse az összes leírt lépéseket [leállítani egy StorSimple-eszköz](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) az elsődleges ház.
2. Az elsődleges ház leállítása után állítsa le az EBOD-t a power és a cooling module (PCM) kapcsolók kikapcsolásával.
3. Annak ellenőrzéséhez, hogy az EBOD leállt, ellenőrizze, hogy az EBOD-ház hátulján lévő összes jelzőfény ki van-e kapcsolva.

> [!NOTE]
> Az EBOD-ház elsődleges házhoz való csatlakoztatásához használt SAS-kábeleket csak a rendszer leállítása után kell eltávolítani.

## <a name="next-steps"></a>További lépések
[Lépjen kapcsolatba a Microsoft támogatási szolgálatával,](storsimple-8000-contact-microsoft-support.md) ha problémákat tapasztal a StorSimple-eszközök bekapcsolásakor vagy leállításakor.

