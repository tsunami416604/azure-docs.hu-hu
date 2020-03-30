---
title: StorSimple 8000 Series Update 4 release megjegyzések | Microsoft dokumentumok
description: A cikk a StorSimple 8000 series 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254585"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 series Update 4 kiadási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 sorozat 4.The following release notes describe the new features and identify the critical open issues for StorSimple 8000 Series Update 4. Tartalmaznak továbbá egy listát a StorSimple szoftverfrissítések szerepelnek ebben a kiadásban. 

A 4-es frissítés bármely Release (GA) vagy Update 0.1 update (3.1- frissítés) rendszerű StorSimple-eszközre alkalmazható. A 4.frissítéshez tartozó eszközverzió a 6.3.9600.17820.

Kérjük, tekintse át a kiadási megjegyzésekben található információkat, mielőtt telepítené a frissítést a StorSimple-megoldásban.

> [!IMPORTANT]
> * A 4-es frissítés eszközszoftverrel, USM firmware-rel, LSI illesztőprogrammal és firmware-rel, lemezfirmware-rel, Storport és Spaceport, biztonsági és egyéb operációs rendszerfrissítésekkel rendelkezik. A frissítés telepítése körülbelül 4 órát vesz igénybe. A lemez belső vezérlőprogramjának frissítése zavaró frissítés, amely az eszköz leállását eredményezi. Azt javasoljuk, hogy az eszköz naprakészen tartásához alkalmazza a 4. 
> * Az új kiadások esetében előfordulhat, hogy nem jelennek meg azonnal a frissítések, mert a frissítések fokozatos bevezetését tesszük. Várjon néhány napot, majd keresse meg újra a frissítéseket, mivel ezek hamarosan elérhetővé válnak.

## <a name="whats-new-in-update-4"></a>A 4.

A következő kulcsfontosságú fejlesztések és hibajavítások történtek a 4- es frissítésben.

* **Intelligensebb automatizált térvisszanyerési algoritmusok** – A 4-es frissítésben az automatikus térvisszanyerési algoritmusok attovábbfejlesztettek, hogy a felhőben rendelkezésre álló várható helyreállító terület alapján módosítsák a térvisszanyerési ciklusokat. 

* **A helyileg rögzített kötetek teljesítményjavítása** – A 4.

* **Heatmap-alapú visszaállítás** – A korábbi kiadásokban, egy vész-helyreállítási (DR) után, az adatok vissza lett állítva a felhőből a hozzáférési minták eredményeként a lassú teljesítményt eredményező. 

    Egy új funkció van megvalósítva a 4-es frissítésben, amely nyomon követi a gyakran használt adatokat, hogy hőtérképet hozzon létre, amikor az eszköz a DR előtt használatban van (a leggyakrabban használt adattömbök nagy hővel rendelkeznek, míg a kevésbé használt adattömbök alacsony hővel rendelkeznek). A DR után a StorSimple a hőtérkép segítségével automatikusan visszaállítja és rehidratálja az adatokat a felhőből. 

    Mostantól az összes visszaállítás hőtérkép alapú visszaállítás. A heatmap alapú visszaállítási és rehidratálási feladatok lekérdezéséről és megszakításáról a [Windows PowerShell storSimple-parancsmag-hivatkozási oldalán talál további információt.](https://technet.microsoft.com/library/dn688168.aspx)

* **StorSimple Diagnostics eszköz** – A 4-es frissítésben egy StorSimple Diagnosztikai eszköz kerül kiadásra, hogy lehetővé tegye a rendszer, a hálózat, a teljesítmény és a hardverösszetevők állapotával kapcsolatos problémák egyszerű diagnosztizálását és hibaelhárítását. Ez az eszköz a Windows PowerShell storSimple rendszeren keresztül fut. További információ: [Hibaelhárítás a StorSimple Diagnosztika eszközzel.](storsimple-8000-diagnostics.md)

* **UI-alapú StorSimple áttelepítési eszköz** – A kiadás előtt az 5000-7000-es sorozatból származó adatok áttelepítése szükségessé teszi a felhasználók számára, hogy az Azure PowerShell-felületen hajtsák végre az áttelepítési munkafolyamat egy részét. Ebben a kiadásban egy könnyen használható felhasználói felület-alapú StorSimple áttelepítési eszköz érhető el a támogatás számára, hogy megkönnyítse ugyanazt az áttelepítési munkafolyamatot. Ez az eszköz lehetővé tenné a helyreállítási gyűjtők konszolidációját is. 

* **FIPS-szel kapcsolatos módosítások** – Ez a kiadás ettől kezdve a FIPS alapértelmezés szerint engedélyezve van az összes StorSimple 8000 sorozatú eszközön a Microsoft Azure Government és az Azure nyilvános felhőalapú fiókjaiban.

* **Frissítési módosítások** – Ebben a kiadásban a frissítési hibákkal kapcsolatos hibák ki lettek javítva.

* **Riasztás a lemezhibák –** Ebben a kiadásban egy új riasztás tadki, amely figyelmezteti a felhasználót a közelgő lemezhibákra. Ha ezt a riasztást tapasztalja, lépjen kapcsolatba a Microsoft támogatási szolgálatával a cserelemez szállításához. További információért nyissa meg [a Hardverriasztásokat a StorSimple eszközön.](storsimple-8000-manage-alerts.md#hardware-alerts)

* **Vezérlő csere változások** - A parancsmag, amely lehetővé teszi a felhasználó számára, hogy lekérdezi az állapotát a vezérlő csere folyamat ad hozzá ebben a kiadásban. További információért nyissa meg a [parancsmatot a vezérlő csereállapotának lekérdezéséhez.](https://technet.microsoft.com/library/dn688168.aspx)


## <a name="issues-fixed-in-update-4"></a>A 4.

Az alábbi táblázat a 4.    

| Nem | Szolgáltatás | Probléma | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Feladatátvétel |A korábbi kiadásban, a feladatátvétel után, volt egy probléma kapcsolatos razzia az ügyfél helyén. Ez a probléma ebben a kiadásban kivan javítva. |Igen |Igen |
| 2 |Helyileg rögzített kötetek |Az előző kiadásban probléma volt a helyileg rögzített kötetek kapcsolódó kötetek létrehozásával kapcsolatban, ami kötetlétrehozási hibákat eredményezne. A probléma ebben a kiadásban gyökér okozta és kijavított. |Igen |Nem |
| 3 |Támogatási csomag |Az előző kiadásban problémák merültek fel a támogatási csomaggal kapcsolatban, amelyek System.OutOfMemory kivételt vagy más hibákat eredményeztek, amelyek támogatási csomag létrehozási hibáját eredményezték. Ezek a hibák ki vannak javítva ebben a kiadásban. |Igen |Igen |
| 4 |Figyelés |Az előző kiadásban a helyileg rögzített kötetek figyelésével kapcsolatos probléma, ahol a felhasználás az EB-ben volt látható. Ez a hiba ebben a kiadásban oldódik meg. |Igen |Igen |
| 5 |Migrálás |Az előző kiadásban számos probléma volt az 5000-7000 sorozatból a 8000-es sorozatú eszközökre való áttérés megbízhatóságával kapcsolatban. Ezekkel a problémákkal ebben a kiadásban foglalkoztunk. |Igen |Igen |
| 6 |Frissítés |A korábbi kiadásokban, ha frissítési hiba történt, a vezérlők helyreállítási módba lépnek, és így a felhasználó nem tudta folytatni a frissítést, és kapcsolatba kell lépnie a Microsoft támogatási szolgálatával. <br> Ez a viselkedés megváltozott ebben a kiadásban. Ha a felhasználó frissítési hibát észlelt, miután mindkét vezérlő ugyanazt a verziót futtatja (4. frissítés), a vezérlők nem váltanak helyreállítási módba. Ha a felhasználó ezt a hibát tapasztalja, azt javasoljuk, hogy várjon egy kicsit, majd próbálkozzon újra a frissítéssel. Az újrapróbálkozás sikeres lehet. Ha az újrapróbálkozás sikertelen, forduljon a Microsoft támogatási szolgálatához. |Igen |Igen |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Ismert problémák a 4.

Nincsenek új ismert problémák a 4. A korábbi kiadások ból a 4. [Update 3 release notes](storsimple-update3-release-notes.md#known-issues-in-update-3)

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Sorosscsi-vezérlő (SAS) vezérlő és belső vezérlőprogram frissítése a 4.

Ez a kiadás SAS-vezérlővel, lsi illesztőprogram- és belsővezérlő-frissítésekkel rendelkezik. A frissítések telepítéséről a [4.](storsimple-install-update-4.md)

## <a name="virtual-device-updates-in-update-4"></a>Virtuális eszközfrissítések a 4.

Ez a frissítés nem alkalmazható a StorSimple cloud appliance (más néven a virtuális eszköz). Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés

További információ a [4.](storsimple-install-update-4.md)

