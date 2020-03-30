---
title: Az SAP HANA elérhetősége az Azure-régiók között | Microsoft dokumentumok
description: Az SAP HANA azure-beli virtuális gépeken való futtatásakor rendelkezésre állási szempontok áttekintése több Azure-régióban.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078851"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Az SAP HANA elérhetősége az Azure-régiók között

Ez a cikk az SAP HANA különböző Azure-régiók közötti rendelkezésre állásával kapcsolatos forgatókönyveket ismerteti. Az Azure-régiók közötti távolság miatt az SAP HANA több Azure-régióban való rendelkezésre állásának beállítása speciális szempontokat is magában foglal.

## <a name="why-deploy-across-multiple-azure-regions"></a>Miért érdemes több Azure-régióban telepíteni?

Az Azure-régiókat gyakran nagy távolságok választják el egymástól. A geopolitikai régiótól függően az Azure-régiók közötti távolság több száz mérföld, vagy akár több ezer mérföld is lehet, például az Egyesült Államokban. A távolság miatt a két különböző Azure-régióban üzembe helyezett eszközök közötti hálózati forgalom jelentős hálózati oda-vissza késést tapasztal. A késés elég jelentős ahhoz, hogy kizárja a szinkron adatcsere két SAP HANA-példányok tipikus SAP számítási feladatok alatt. 

Másrészt a szervezetek gyakran rendelkeznek az elsődleges adatközpont és a másodlagos adatközpont helye közötti távolsági követelménnyel. A távolságkövetelmény segít a rendelkezésre állás biztosításában, ha egy természeti katasztrófa egy szélesebb földrajzi helyen következik be. Ilyenek például a 2017 szeptemberében és októberében a Karib-térséget és Floridát sújtó hurrikánok. Előfordulhat, hogy a szervezetnek legalább egy minimális távolsági követelménye van. A legtöbb Azure-ügyfél számára a minimális távolságdefiníció megköveteli, hogy tervezzen az [Azure-régiók](https://azure.microsoft.com/regions/)ban való rendelkezésre álláshoz. Mivel a két Azure-régió közötti távolság túl nagy a HANA szinkron replikációs mód használatához, az RTO és az RPO-követelmények arra kényszeríthetik, hogy egy régióban telepítse a rendelkezésre állási konfigurációkat, majd egy másodperc alatt további telepítésekkel egészítse ki Régió.

Egy másik szempont, hogy fontolja meg ebben a forgatókönyvben a feladatátvétel és az ügyfél átirányítása. A feltételezés az, hogy az SAP HANA-példányok két különböző Azure-régióban feladatátvétel mindig egy manuális feladatátvétel. Mivel az SAP HANA rendszer replikációs módja aszinkron, fennáll annak a lehetősége, hogy az elsődleges HANA-példányban véglegesített adatok még nem tették meg a másodlagos HANA-példányra. Ezért az automatikus feladatátvétel nem választható olyan konfigurációkhoz, ahol a replikáció aszinkron. Még manuálisan vezérelt feladatátvétel, mint egy feladatátvételi feladatátvételi feladat, meg kell tennie a szükséges intézkedéseket annak biztosítására, hogy az elsődleges oldalon lévő összes véglegesített adatok a másodlagos példány, mielőtt manuálisan áttérne a másik Azure-régióba.
 
Az Azure virtuális hálózat egy másik IP-címtartományt használ. Az IP-címek a második Azure-régióban vannak telepítve. Így vagy módosítania kell az SAP HANA ügyfélkonfigurációját, vagy lehetőleg létre kell hoznia a névfeloldás módosításához szükséges lépéseket. Így az ügyfelek átlesznek irányítva az új másodlagos hely kiszolgálójának IP-címére. További információ: AZ SAP cikk [Ügyfélkapcsolat helyreállítása az átvétel után](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Egyszerű elérhetőség két Azure-régió között

Előfordulhat, hogy nem hoz létre egyetlen régión belül egyetlen rendelkezésre állási konfigurációt, de továbbra is szükség van arra, hogy a számítási feladatok kiszolgálják, ha egy katasztrófa bekövetkezik. Az ilyen esetek tipikus esetei a nem termelési rendszerek. Bár miután a rendszer le fél napra, vagy akár egy nap fenntartható, nem engedheti meg, hogy a rendszer nem érhető el 48 óra vagy annál több. Ahhoz, hogy a telepítő kevésbé költséges, futtasson egy másik rendszert, amely még kevésbé fontos a virtuális gép. A másik rendszer célként működik. A másodlagos régióban is méretezheti a virtuális gép méretét, hogy kisebb legyen, és úgy dönt, hogy nem tölti be előre az adatokat. Mivel a feladatátvétel manuális, és sokkal több lépést jár a teljes alkalmazásverem feladatátvételéhez, a virtuális gép leállításához, átméretezéséhez, majd a virtuális gép újraindításához szükséges további idő elfogadható.

Ha a vész-ellenőrzési cél megosztásának forgatókönyvét egy virtuális gép minőségbiztosítási rendszerével osztja meg, figyelembe kell vennie ezeket a szempontokat:

- Két [üzemmód van](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) delta_datashipping és logreplay-rel, amelyek ilyen forgatókönyvhöz állnak rendelkezésre.
- Mindkét üzemmód eltérő memóriakövetelményekkel rendelkezik az adatok előzetes betöltése nélkül
- Delta_datashipping szükség lehet drasztikusan kevesebb memóriát anélkül, hogy az előtöltési lehetőség, mint a logreplay is szükség lehet. Lásd az [SAP-dokumentum](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf) 4.3.
- A logreplay működési mód memóriaigénye előtöltés nélkül nem determinisztikus, és az oszlopcentrikus struktúrák betöltésétől függ. Szélsőséges esetekben előfordulhat, hogy az elsődleges példány memóriájának 50%-ára van szükség. A logreplay üzemmód memóriája független attól, hogy az adatok előre betöltött beállítását választotta-e.


![Két virtuális gép diagramja két régióban](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Ebben a konfigurációban nem adhat meg RPO=0-t, mert a HANA rendszer replikációs módja aszinkron. Ha meg kell adnia egy RPO=0, ez a konfiguráció nem a konfiguráció a választás.

A konfigurációban egy kis módosítás lehet az adatok előtöltésként való konfigurálása. Azonban a feladatátvétel manuális jellege és az a tény, hogy az alkalmazásrétegek is kell mozgatni a második régióba, lehet, hogy nincs értelme az adatok előzetes betöltése. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Az egy régión belüli és a régiók közötti elérhetőség kombinálása 

A régiókon belüli és régiók közötti rendelkezésre állás kombinációját a következő tényezők vezérelhetik:

- RPO=0 követelmény egy Azure-régióban.
- A szervezet nem hajlandó vagy képes a globális műveletek által érintett jelentős természeti katasztrófa, amely hatással van egy nagyobb régióban. Ez volt a helyzet néhány hurrikánok, hogy elérje a Karib-térségben az elmúlt néhány évben.
- Olyan szabályozások, amelyek az elsődleges és másodlagos helyek közötti távolságokat igényelnek, amelyek egyértelműen meghaladják az Azure rendelkezésre állási zónái által nyújtott lehetőségeket.

Ezekben az esetekben beállíthatja, amit az SAP [sap HANA többrétegű rendszerreplikációs konfigurációnak](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) nevez hana rendszerreplikáció használatával. Az architektúra így nézne ki:

![Három virtuális gép diagramja két régióban](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Az SAP [többcélos rendszerreplikációt](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) vezetett be a HANA 2.0 SPS3-mal. A többcélzott rendszerreplikáció bizonyos előnyökkel jár a frissítési forgatókönyvekben. Például a VÉSZ-hely (2. régió) nem érinti, ha a másodlagos HA hely karbantartás vagy frissítések miatt nem működik. A HANA többcélzottrendszer-replikációról itt [here](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)olvashat bővebben.
A többcélos replikációval rendelkező lehetséges architektúra a következőkre néz ki:

![Három virtuális gép diagramja két régióban milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Ha a szervezet a második(DR) Azure-régióban a magas rendelkezésre állásra való alkalmasságra vonatkozó követelményekkel rendelkezik, akkor az architektúra így fog kinézni:

![Három virtuális gép diagramja két régióban milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


A logreplay-t működési módként használva ez a konfiguráció egy RPO=0-t biztosít, alacsony RTO-val, az elsődleges régión belül. A konfiguráció is biztosít tisztességes RPO, ha a lépés a második régióban is részt vesz. Az RTO-idők a második régióban attól függ, hogy az adatok előre be van-e töltve. Sok ügyfél használja a virtuális gép a másodlagos régióban egy tesztrendszer futtatásához. Ebben a felhasználási esetben az adatok nem tölthetők be előre.

> [!IMPORTANT]
> A különböző szintek közötti üzemmódoknak homogénnek kell lenniük. A logreply **nem** használható az delta_datashipping 1. Csak az egyik vagy a másik üzemmódot választhatja ki, amelynek minden rétegre egységesnek kell lennie. Mivel delta_datashipping nem alkalmas RPO=0-ra, az ilyen többrétegű konfigurációk egyetlen ésszerű működési módja a logreplay marad. A működési módokról és bizonyos korlátozásokról az [SAP-beli SAP HANA rendszerreplikáció működési módjai](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)című témakörben olvashat részletesen. 

## <a name="next-steps"></a>További lépések

A konfigurációk Azure-beli beállításával kapcsolatos részletes útmutatást a következő témakörökben talál:

- [SAP HANA-rendszerreplikáció beállítása az Azure virtuális gépein](sap-hana-high-availability.md)
- [Az SAP HANA magas rendelkezésre állása a rendszer replikációjával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
