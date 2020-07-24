---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: cc0dd5e76227c0814659b74afc2ac46c8ca4de73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102713"
---
## <a name="common-scenarios"></a>Gyakori forgatókönyvek
A következő forgatókönyvek nagy mértékben kihasználhatják a betörést:
- A **rendszerindítási időpontok javítása** – a betörést követően a példány jelentősen gyorsabb ütemben fog indulni. A Premium-kompatibilis virtuális gépek alapértelmezett operációsrendszer-lemeze például a P4-lemez, amely legfeljebb 120 IOPS és 25 MB/s kiépített teljesítményű. A Betöréssel a P4 akár 3500 IOPS-t és 170 MB/s-ot is biztosít, ami lehetővé teszi a rendszerindítási időt a 6X felgyorsításához.
- **Kötegelt feladatok feldolgozása** – egyes alkalmazások számítási feladatai ciklikus jellegűek, és az idő nagy részében alapvető teljesítményt igényelnek, és rövid időn belül nagyobb teljesítményt igényelnek. Erre példa egy olyan könyvelési program, amely naponta dolgozza fel a tranzakciókat, amelyek kis mennyiségű lemezterületet igényelnek. A hónap végén a olyan jelentések egyeztetését végzi el, amelyek sokkal nagyobb mennyiségű lemezes forgalmat igényelnek.
- **Felkészülés a forgalmi csúcsokra** – a webkiszolgálók és alkalmazásaik bármikor felhasználhatják a forgalmat. Ha a webkiszolgálót a virtuális gépek vagy lemezek a burst használatával végzik, a kiszolgálók jobban fel vannak szerelve a forgalmi tüskék kezelésére. 

## <a name="bursting-flow"></a>Feltört folyamat
A feltört kreditrendszer a virtuális gép szintjén és a lemez szintjén is azonos módon alkalmazható. Az erőforrás, amely egy virtuális gép vagy egy lemez, teljes értékű Kredittel fog kezdődni. Ezek a kreditek lehetővé teszik, hogy 30 percen belül a maximális burst sebességgel feltörten. A feltört kreditek akkor halmozódnak fel, ha az erőforrás a teljesítményük lemezes tárterületének korlátai szerint fut. Az összes olyan IOPS és MB/s esetében, amelyet az erőforrás a teljesítmény korlátozása alatt használ, elkezdi a kreditek összegyűjtését. Ha az erőforrás felhalmozott kreditekkel rendelkezik, és a számítási feladathoz szükség van az extra teljesítményre, akkor az erőforrás ezeket a krediteket a teljesítmény korlátja fölé helyezheti, hogy a lemez i/o-teljesítménye megfeleljen az igényeknek.



![Feltört gyűjtő diagramja](media/managed-disks-bursting/bucket-diagram.jpg)

Akár egészen addig, ahogy szeretné, hogy a 30 perces kitörést kívánja használni. Akár 30 percet is igénybe vehet a nap folyamán, vagy szórványosan. A termék üzembe helyezése után a rendszer készen áll a teljes értékű kreditekre, és ha a kreditek elvégzése egy napnál kevesebb időt vesz igénybe, a kreditek teljes összegét újra fel kell venni. Saját belátása szerint felhalmozhatja és elköltheti a feltört krediteket, és a 30 perces gyűjtőnek nem kell megismételni a feltört időt. Az egyik dolog, ami azt jelzi, hogy a burst felhalmozódása különbözik az egyes erőforrásoktól, mert a használaton kívüli IOPS, valamint a teljesítményük alatti MB/s értéken alapul. Ez azt jelenti, hogy a magasabb alapszintű teljesítményű termékek az alacsonyabb alapkonfigurációt használó termékeknél gyorsabban felmerülhetnek a feltört mennyiségű terméknél. Egy tevékenység nélküli P1 lemez üresjárati ideje például 120 IOPS, míg a P20-lemezek másodpercenként 2 300 IOPS-t kapnak, és tevékenység nélkül üresjáratban vannak.

## <a name="bursting-states"></a>Feltört állapotok
Három állapottal rendelkezhet, ha az erőforrás a kitört állapotban van:
- **Felhalmozás** – az erőforrás IO-forgalma kevesebb, mint a teljesítményre vonatkozó cél. A IOPS és a MB/s feltört kreditek összegyűjtése egymástól elkülönítve történik. Az erőforrás felhalmozhat IOPS krediteket, és megoszthat MB/s kreditet, vagy fordítva.
- **Burst** – az erőforrás forgalma több, mint a teljesítmény célját használja. A burst forgalom egymástól függetlenül fogja felhasználni a IOPS vagy a sávszélességtől kapott krediteket.
- **Állandó** – az erőforrás forgalma pontosan a teljesítmény célpontja.

## <a name="examples-of-bursting"></a>Példák a kitörésre
Az alábbi példák azt mutatják be, hogyan működik a kitörés a különböző virtuális gépekkel és lemez-kombinációkkal. Ahhoz, hogy a példák könnyen követhető legyenek, a MB/s-ra fogunk összpontosítani, de ugyanezt a logikát a IOPS függetlenül alkalmazza a rendszer.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Nem betört virtuális gép, amely feltört lemezekkel rendelkezik
**VIRTUÁLIS gépek és lemezek kombinációja:** 
- Standard_D8as_v4 
    - Gyorsítótár nélküli MB/s: 192
- P4 operációsrendszer-lemez
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 
- 2 P10 adatlemez 
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170

 Amikor a virtuális gép elindul, az operációs rendszer lemezéről kéri le az adatok beolvasását. Mivel az operációsrendszer-lemez egy olyan virtuális gép része, amely az első lépésekből áll, az operációsrendszer-lemez tele lesz a feltört kreditekkel. Ezek a kreditek lehetővé teszik, hogy az operációsrendszer-lemez elindítsa az indítást 170 MB/s-on, ahogy az alábbi képen látható:

![Nem feltört virtuális gép rendszerindítási lemezének indítása](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

A rendszerindítás befejezése után egy alkalmazás fut a virtuális gépen, és nem kritikus fontosságú számítási feladattal rendelkezik. Ehhez a munkaterheléshez 15 MB/S érték szükséges, amely egyenletesen oszlik el az összes lemez között:

![Nem feltört virtuális gép, amely üresjáratban van](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Ezután az alkalmazásnak fel kell dolgoznia egy batch-feladatot, amely 192 MB/s memóriát igényel. az operációsrendszer-lemez 2 MB/s-ot használ, a többi pedig egyenlően oszlik meg az adatlemezek között:

![Nem feltört virtuális gép kipukkanása lemezről](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Feltört virtuális gép nem feltört lemezekkel
**VIRTUÁLIS gépek és lemezek kombinációja:** 
- Standard_L8s_v2 
    - Gyorsítótár nélküli MB/s: 160
    - Maximális burst MB/s: 1 280
- P50 operációsrendszer-lemez
    - Kiépített MB/s: 250 
- 2 P10 adatlemez 
    - Kiépített MB/s: 250

 A kezdeti rendszerindítás után egy alkalmazás fut a virtuális gépen, és nem kritikus fontosságú számítási feladattal rendelkezik. Ez a munkaterhelés 30 MB/s memóriát igényel, amely egyenletesen oszlik el az összes lemezen: a ![ virtuális gép nem feltört lemezének üresjárata](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Ezután az alkalmazásnak fel kell dolgoznia egy batch-feladatot, amely 600 MB/s memóriát igényel. A Standard_L8s_v2 az igények kielégítése érdekében, majd a lemezekre irányuló kérések egyenletesen oszlanak meg a P50-lemezeken:

![A virtuális gép nem feltört lemezének kitörése](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Leválasztható virtuális gép feltört lemezekkel
**VIRTUÁLIS gépek és lemezek kombinációja:** 
- Standard_L8s_v2 
    - Gyorsítótár nélküli MB/s: 160
    - Maximális burst MB/s: 1 280
- P4 operációsrendszer-lemez
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 
- 2 P4 adatlemez 
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 

Ha a virtuális gép elindul, a rendszer az operációsrendszer-lemezről 1 280 MB/s-OS burst-korlátot kér, és az operációsrendszer-lemez a 170 MB/s burst teljesítményével fog válaszolni:

![A virtuális gép burst lemezének indítása](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Ezután a rendszerindítás befejezése után egy alkalmazás fut a virtuális gépen. Az alkalmazás olyan nem kritikus fontosságú számítási feladattal rendelkezik, amely 15 MB/s memóriát igényel, amely egyenletesen oszlik el az összes lemez között:

![A virtuális gép felszakadási lemezének üresjárata](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Ezután az alkalmazásnak fel kell dolgoznia egy batch-feladatot, amely 360 MB/s memóriát igényel. A Standard_L8s_v2 az igények kielégítése érdekében felrobban, majd kéri. Az operációsrendszer-lemez csak 20 MB/s memóriát igényel. A fennmaradó 340 MB/s-ot a kitört P4 adatlemezek kezelik:  

![A virtuális gép kipukkanása a lemezről](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
