---
title: "A StorSimple 8000 Series Update 4 kibocsátási megjegyzései |} Microsoft Docs"
description: "Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 4."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: f1c0734b07287d95a2f5031e010f0e407d3a816f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>A StorSimple 8000 Series Update 4 kibocsátási megjegyzései
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](storsimple-8000-move-azure-portal-faq.md) szakaszt.


## <a name="overview"></a>Áttekintés

Az alábbi kibocsátási megjegyzések új szolgáltatásait ismerteti, és a StorSimple 8000 Series Update 4 a kritikus megnyitott problémák azonosításához. Ebben a kiadásban szereplő StorSimple szoftverfrissítések listáját is tartalmaznak. 

4. frissítés bármely kiadásban (GA) vagy frissítés 0,1 futtató keresztül frissítés 3.1 StorSimple eszközre alkalmazhatók. Az eszköz Update 4 társított verziója 6.3.9600.17820.

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * Update 4 eszköz szoftver, a legpontosabb Beállításhoz belső vezérlőprogram, LSI illesztőprogram és belső vezérlőprogramjának, lemez belső vezérlőprogram, Storport és Spaceport, biztonsági és más operációs rendszer frissítése érdekében rendelkezik. Ez a frissítés körülbelül 4 órát vesz igénybe. Vezérlőprogram-frissítés zavaró frissítésről, és az eszköz egy leállásának eredményez. Azt javasoljuk, hogy az eszköz naprakész állapotban tartása érdekében Update 4 alkalmaz. 
> * Új kiadásokban nem láthatók frissítések azonnal, mert a frissítések fázisokra bontva történő bevezetéséhez végezzük. Néhány napot várni, és a frissítések újra ezeket a majd vizsgálatát hamarosan elérhető lesz.

## <a name="whats-new-in-update-4"></a>What's new in Update 4

A következő fontos fejlesztést tartalmaz, és hibajavítások Update 4 végzett.

* **Azure ad automatikus terület-visszanyerést algoritmusok** – a frissítés 4, az automatizált terület-visszanyerést algoritmusok továbbfejlesztett úgy, hogy a hely visszanyerése ciklusok alapján a várt regenerált rendelkezésre álló terület a felhőben. 

* **A helyileg rögzített kötetekhez a teljesítményt javító** – Update 4 forgatókönyvekben, amelyek magas adatfeldolgozást (kötetméretet hasonló adatok) helyileg rögzített kötetekhez teljesítménye javult.

* **Heatmap-alapú visszaállítási** -a következő a vészhelyreállítás (DR), a korábbi kiadásokban az adatok a felhőben, a lassú teljesítmény eredményezve hozzáférési minták alapján lett visszaállítva. 

    Egy új szolgáltatást tartalmazza az Update 4, hogy nyomon követi a gyakran használt adatok egy heatmap létrehozásához, ha az eszköz használata előtt a vész-Helyreállítási (az adattömbök leggyakrabban használt rendelkezik nagy kisebb adattömbök szolgálja, míg az alacsony tűz rendelkezik). StorSimple DR, miután a heatmap használja a automatikusan visszaállítani, és az adatokat a felhőből rehydrate. 

    A visszaállítás jelenleg alapú heatmap szolgálhatnak. Lekérdezési és alapú heatmap visszaállítása és rehidratálása szakítsa további információkért látogasson el [StorSimple parancsmag-referencia a Windows PowerShell](https://technet.microsoft.com/library/dn688168.aspx).

* **StorSimple diagnosztikai eszköz** – az Update 4, a StorSimple diagnosztikai eszköz kiadásra könnyen diagnosztizálására engedélyezéséhez és a rendszer, a hálózati, a teljesítmény és a hardver összetevő állapota kapcsolatos problémák hibaelhárításához. Ezt az eszközt a StorSimple fut a Windows PowerShell segítségével. További információkért látogasson el [StorSimple diagnosztikai eszköz használata – hibaelhárítás](storsimple-8000-diagnostics.md).

* **Felhasználóifelület-alapú StorSimple áttelepítési eszköz** -előtti ebben a kiadásban az áttelepítést 5000-7000-es sorozatból szükséges a felhasználók egy része az az Azure PowerShell felületéről áttelepítési munkafolyamat végrehajtásához. Ebben a kiadásban egy könnyen kezelhető grafikus felhasználói felületű StorSimple áttelepítési eszköz szeretné elérhetővé tenni a áttelepítési ugyanabban a munkafolyamatban elősegítésére támogatáshoz. Ez az eszköz a helyreállítási gyűjtők összevonása lehetővé. 

* **FIPS-kapcsolatos módosítások** - e és újabb verziók esetében a kiadási, FIPS a StorSimple 8000 sorozat eszközeire alapértelmezés szerint engedélyezve van, az a Microsoft Azure Government és az Azure nyilvánosfelhő-fiókokat.

* **Módosításainak frissítésére** -ebben a kiadásban hibák frissítése kapcsolatos hibák kijavítása.

* **Riasztás a lemezhibák** -egy új riasztás, amely figyelmezteti a felhasználót a közelgő lemezhiba jelenik meg ebben a kiadásban. Ha ezt a riasztást, lépjen kapcsolatba a Microsoft Support küldje el a cserelemezt. További információkért látogasson el [hardver riasztások a StorSimple eszköz](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Tartományvezérlő helyettesítő módosítások** -parancsmag, amely lehetővé teszi a felhasználó a vezérlő cseréjét állapotának lekérdezése egészül ki ebben a kiadásban. További információkért látogasson el a [lekérdezés tartományvezérlő helyettesítő állapotát parancsmag](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Az Update 4 megoldott problémák

A következő táblázat összefoglalja az Update 4 javított problémák.    

| Nem | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Feladatátvétel |A korábbi verzióban a feladatátvétel történt a megfigyelt az ügyfél telephelyén tisztítással kapcsolatos problémát. Ez a probléma fennáll ebben a kiadásban. |Igen |Igen |
| 2 |Helyileg rögzített kötetek |A korábbi változatban egy problémát a kapcsolódó kötetek létrehozását a helyileg rögzített kötetekhez, amely kötet létrehozása sikertelen volt. Ez a probléma volt legfelső szintű okozott, és ebben a kiadásban rögzített. |Igen |Nem |
| 3 |Támogatási csomag |Az előző kiadása nem voltak támogatási csomag, amely egy System.OutOfMemory kivétel vagy más hibák, és egy támogatási csomag létrehozását hibát okozott kapcsolatos problémák. Ezek a hibák ebben a kiadásban rögzítettek. |Igen |Igen |
| 4 |Figyelés |Korábbi változatban nincs diagramok helyileg figyelésével kapcsolatos problémát rögzített kötetek ahol EB fogyasztás volt látható. Ez a hiba megoldását az ebben a kiadásban. |Igen |Igen |
| 5 |Migrálás |Korábbi változatban több problémák merültek 5000-7000-es sorozatból 8000 sorozat eszközeire való áttelepítés megbízhatóságát kapcsolódik. Ebben a kiadásban rendelkezik foglalkoztak. ezeket a problémákat. |Igen |Igen |
| 6 |Frissítés |A korábbi kiadásokban, ha egy frissítés sikertelen volt, a tartományvezérlők kerülne helyreállítási módba, és ezért a felhasználó a frissítés nem folytatható, és forduljon a Microsoft Support kell. <br> Ez a viselkedés ebben a kiadásban megváltozott. Ha a felhasználó egy frissítési hiba után mindkét vezérlőhöz (Update 4) azonos verziója fut, a tartományvezérlők ne lépje helyreállítási módba. Ha a felhasználó ezt a hibát észlel, azt javasoljuk, hogy azok egy kis türelmet, és ismételje meg a frissítést. Az ismételt próbálkozás sikeres volt. Ha nem sikerül az újra gombra, majd azok lépjen kapcsolatba Microsoft Support. |Igen |Igen |


## <a name="known-issues-in-update-4-from-previous-releases"></a>A korábbi kiadásokból származó Update 4 kapcsolatos ismert problémák

Nincsenek új ismert problémák a frissítési 4. A korábbi kiadásokból származó Update 4 átkerülnek problémák listáját itt [Update 3 kibocsátási megjegyzések](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Soros csatlakozású SCSI (SAS) vezérlő és a belső vezérlőprogram frissítések Update 4

Ebben a kiadásban a vezérlő és LSI illesztőprogram és a belső vezérlőprogram frissítések rendelkezik. Ezek a frissítések telepítéséről további információkért lásd: [Update 4 telepítése](storsimple-install-update-4.md) a StorSimple eszköz.

## <a name="virtual-device-updates-in-update-4"></a>Az Update 4 virtuális eszköz frissítése

A frissítés nem alkalmazható a StorSimple felhő készülék (más néven a virtuális eszköz). Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés

Megtudhatja, hogyan [Update 4 telepítése](storsimple-install-update-4.md) a StorSimple eszköz.

