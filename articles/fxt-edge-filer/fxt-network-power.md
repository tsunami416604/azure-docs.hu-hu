---
title: 'Oktatóanyag: Kábelek csatlakoztatása egy Azure FXT Edge Filer-hez'
description: A hálózati portok kábelezése és az Azure FXT Edge Filer hardvertá válása
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239783"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Oktatóanyag: Hálózati kapcsolatok létesítése és tápellátás az Azure FXT Edge Filer csomóponthoz

Ez az oktatóanyag bemutatja, hogyan kábelezheti a hálózati kapcsolatokat egy Azure FXT Edge Filer hardvercsomóponthoz.

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Hogyan válasszuk ki a hálózati kábel típusát a környezetben
> * Azure FXT Edge filer csomópont csatlakoztatása az adatközponti hálózathoz
> * A kábelek átvezetése a kábelkezelő karon (CMA)
> * Hogyan csatlakoztassuk az áramot a kínzott eszközhöz, és kapcsoljuk be

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt az Azure FXT Edge Filer kell telepíteni egy szabványos berendezés rack. A CMA-t telepíteni kell a fájlkezelő csomópontra. 

## <a name="identify-ports"></a>Portok azonosítása

Azonosítsa a különböző portokat az Azure FXT Edge Filer hátoldalán. 
 
![Kábeles eszköz háttere](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Az eszköz bekábelezése

* Csatlakoztassa az RJ-45 portokat az adatközpont hálózati forrásához a [Hálózati portok](#network-ports)című részben leírtak szerint.  
* Az [iDRAC-port](#idrac-port) biztonságos csatlakoztatása egy biztonságos DHCP-kiszolgálóval rendelkező külön hálózathoz. 
* Az USB-portok és a VGA-port segítségével csatlakoztasson billentyűzetet és monitort a csomóponthoz a kezdeti beállításhoz. El kell indítania a csomópontot, és [be kell állítania egy kezdeti jelszót](fxt-node-password.md) a csomópont többi portjának aktiválásához. A részletekért olvassa [el a Kezdeti jelszavak beállítása](fxt-node-password.md) című szöveget. 

Ez a cikk azt is ismerteti, hogyan [lehet csatlakoztatni a hálózati árama](#connect-power-cables) a csomóponthoz. 

Ez a cikk azt is ismerteti, hogyan csatlakozhat a csomópont [soros portjához,](#serial-port-only-when-necessary)ha ez speciális hibaelhárításhoz szükséges. 

### <a name="network-ports"></a>Hálózati portok 

Minden Azure FXT Edge Filer csomópont a következő hálózati portokat tartalmazza: 

* Hat nagy sebességű 25GbE/10GbE kettős sebességű adatport: 

  * Négy port, amelyet két kétportos beépülő modul hálózati adapter biztosít
  * Két port által biztosított alaplap mezzanine hálózati adapter 

* Két 1GbE port, amelyet az alaplap mezzanine hálózati adaptere biztosít 

A nagy sebességű 25GbE/10GbE adatportok szabványos SFP28-kompatibilis ketrecekkel rendelkeznek. Az optikai kábelek használatához telepítenie kell az SFP28 optikai adó-vevő modulokat (nincs megadva).

Az 1GbE portok szabványos RJ-45 csatlakozókkal rendelkeznek.

A támogatott kábelek, kapcsolók és adó-vevők teljes listáját a [Cavium FastlinQ 41000 sorozatú együttműködési mátrixban talál.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)

A rendszerhez használandó kapcsolatok típusa az adatközponti környezettől függ.

* Ha 25 GbE-hálózathoz csatlakozik, kábelezze az egyes nagy sebességű adatportokat az alábbi kábeltípusok egyikével:

  * Optikai kábel és SFP28 optikai adó-vevő 25GbE vagy dual rate 25GbE/10GbE képességgel
  * SFP28 típusú 25GbE-képes közvetlen csatolja twinaxialkábel

* Ha 10 GbE-hálózathoz csatlakozik, kábelezze az egyes nagy sebességű adatportokat az alábbi adatok egyikével: 

  * Optikai kábel és SFP28 optikai adó-vevő 10GbE vagy dual rate 25GbE/10GbE képesség.
  * SFP28 típusú 25GbE-képes közvetlen csatolja twinaxialkábel
  * SFP28 típusú 10GbE-képes közvetlen csatolja twinaxialkábel

* Az 1GbE hálózati portok fürtkezelési forgalomhoz használatosak. Ha a fürt létrehozásakor a fürt létrehozásakor ellenőrizze az **1 Gb-os mgmt hálózat használata** jelölőnégyzetet, ha fizikailag különálló hálózatot szeretne létrehozni a fürtkonfigurációhoz [(a leírás: A felügyeleti hálózat konfigurálása).](fxt-cluster-create.md#configure-the-management-network) Kábelezze a portokat szabványos Cat5 vagy jobb kábellel a támogatott kábelek listájában leírtak szerint.

  Az 1GbE portokat nem lehet kábelen hagyni, ha a nagy sebességű portokat kívánja használni az összes forgalomhoz. Alapértelmezés szerint az 1GbE hálózati portok nem használatosak, ha nagyobb sebességű adatport áll rendelkezésre.  

### <a name="idrac-port"></a>iDRAC port  

A port feliratú iDRAC egy 1 Gb-os kapcsolat, amely lehetővé teszi a kommunikációt a hardverkezeléséhez és figyeléséhez használt távelérési vezérlővel. Az FXT szoftver az Intelligent Platform Management Interface (IPMI) vezérlőt használja a hibaelhárításhoz és a helyreállításhoz. A beépített [iDRAC-csatoló](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) segítségével figyelheti a hardvert ezen a porton keresztül. Az iDRAC és az IPMI-hozzáférés alapértelmezés szerint engedélyezve van. 

> [!Note]
> Az iDRAC-port megkerülheti az operációs rendszert, és közvetlenül kapcsolatba léphet a csomóponton lévő hardverekkel. 

Az iDRAC-port csatlakoztatásakor és konfigurálásakor használja ezeket a biztonsági stratégiákat:

* Csak olyan hálózathoz csatlakoztasson iDRAC-portokat, amely fizikailag elkülönül a fürt höz való hozzáféréshez használt adathálózattól.
* Állítson be egy biztonságos iDRAC rendszergazdai jelszót minden csomóponton. Be kell állítani ezt a jelszót, hogy aktiválja a hardver - kövesse az utasításokat [a Hardver jelszavak beállítása](fxt-node-password.md).
* Az alapértelmezett iDRAC-portkonfiguráció a DHCP és az IPv4 protokollt használja az IP-címhozzárendeléshez. Győződjön meg arról, hogy a DHCP-környezet jól védett, és hogy a DHCP-ügyfelek és a DHCP-kiszolgáló között korlátozott a kapcsolat. (A fürt vezérlőpultján a csomópontok címkonfigurációs módszerének a fürt létrehozása után módosítható kontitúdóbeállításai tanak.)
* Hagyja az iDRAC portot "dedikált módra" (az alapértelmezettre), amely korlátozza az iDRAC/IPMI hálózati forgalmat a dedikált RJ-45 portra.

Az iDRAC-port nem igényel nagy sebességű hálózati kapcsolatot.
  
### <a name="serial-port-only-when-necessary"></a>Soros port (csak szükség esetén)

Bizonyos esetekben előfordulhat, hogy a Microsoft szolgáltatás és támogatás azt mondja, hogy a probléma diagnosztizálásához csatlakoztasson egy terminált egy csomópont soros portjához.  

A konzol csatlakoztatása:

1. Keresse meg az FXT Edge Filer csomópont hátulján található soros (COM1) portot.
1. Null modemkábel lelhető fel a soros port nak az ANSI-115200-8N1-hez konfigurált terminálhoz való csatlakoztatásához.
1. Jelentkezzen be a konzolra, és hajtsa végre a kisegítő személyzet utasításainak megfelelő egyéb lépéseket.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Útvonalkábelek a kábelkezelő karon (CMA)

Minden Azure FXT Edge Filer csomópont hoz egy opcionális kábelkezelő kar. A CMA leegyszerűsíti a kábelvezetést, és megkönnyíti a hozzáférést a ház hátuljához anélkül, hogy le kellene választania a kábeleket. 

Kövesse az alábbi utasításokat a kábelek cma-n keresztül történő átirányításához: 

1. A mellékelt rögzítőcsomagolások segítségével csomagolja össze a kábeleket, amint belépnek a kosarakba és kilépnek, hogy ne zavarja a szomszédos rendszereket (1).
1. Ha a CMA üzemi helyzetben van, irányítsa át a kábelköteget a belső és külső kosarakon (2).
1. A kábelek (3) rögzítéséhez használja az előszerelt horog- és hurokpántokat a kosarak mindkét végén.
1. Lendítse vissza a CMA-t a tálcára (4).
1. Szerelje fel az állapotjelző kábelt a rendszer hátuljára, és rögzítse a kábelt a CMA-n keresztül. Csatlakoztassa a kábel másik végét a külső CMA kosár (5) sarkához. 

   > [!CAUTION]
   > A kiálló kábelek által okozott esetleges károk elkerülése érdekében biztosítsa az állapotjelző kábel lazaságát, miután ezt a kábelt a CMA-n keresztül irányította. 

![Cma illusztráció kábelekkel felszerelve](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Ha nem telepítette a CMA-t, használja a sínkészletben található két horog- és hurokpántot a rendszer hátulján lévő kábelek irányításához.
> 
>  1. Keresse meg a külső CMA konzolok a belső oldalán mindkét rack karimák.
>  2. Óvatosan csomagolja össze a kábeleket, és húzza ki őket a rendszercsatlakozóktól a bal és a jobb oldalra.
>  3. Fűzze át a kampós és hurkos pántokat a rendszer mindkét oldalán lévő külső CMA konzolokon lévő szerszámozott nyílásokon, hogy biztosítsa a kábelkötegeket.
> 
>     ![CMA nélkül irányított kábelek](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Az IP-címre vonatkozó követelmények

Az Azure FXT Edge Filer hibrid tárolási gyorsítótárában lévő hardvercsomópontok esetében az IP-címeket a fürtszoftver kezeli.

Minden csomóponthoz legalább egy IP-cím szükséges, de a csomópontcímek hozzárendelése a csomópontok fürthöz való hozzáadásakor vagy eltávolításakor lesz hozzárendelve. 

A szükséges IP-címek teljes száma a gyorsítótárat felhasználó csomópontok számától függ. 

Konfigurálja az IP-címtartományt a Vezérlőpult szoftverével a csomópontok telepítése után. További információt a [Fürt adatainak összegyűjtése](fxt-cluster-create.md#gather-information-for-the-cluster)című olvasni tud.  

## <a name="connect-power-cables"></a>Hálózati kábelek csatlakoztatása

Minden Egyes Azure FXT Edge Filer csomópont két tápegységet (PSUs) használ. 

> [!TIP] 
> A két redundáns tápegység kihasználásához csatlakoztasson minden hálózati kábelt egy áramelosztó egységhez (PDU) egy független elágazási áramkörön.  
> 
> Az UPS segítségével a PDU-kat is belehet kapcsolni az extra védelem érdekében. 

1. Csatlakoztassa a mellékelt tápkábeleket a tápegységekhez a házban. Győződjön meg arról, hogy a kábelek és a PSUs-ok teljesen be vannak ülve. 
1. Csatlakoztassa a tápkábeleket a készülékállvány energiaelosztó egységeihez. Ha lehetséges, két külön áramforrást használjon a két kábelhez. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Bekapcsolás egy Azure FXT Edge Filer csomóponton

A csomópont bekapcsolásához nyomja meg a rendszer elején található bekapcsológombot. A gomb a jobb oldali kezelőpanelen található. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Az Azure FXT Edge Filer-csomópont kikapcsolása

A bekapcsológomb segítségével leállíthatja a rendszert a tesztelés során, és mielőtt hozzáadná egy fürthöz. Azonban miután egy Azure FXT Edge Filer csomópont van használatban egy fürt részeként, a fürt vezérlőpult szoftver segítségével állítsa le a hardvert. A részletekért olvassa el [Az Azure FXT Edge Filer hardverbiztonságos kikapcsolása](fxt-power-off.md) című elolvassa. 

## <a name="next-steps"></a>További lépések

Miután befejezte a hardver kábelezését, kapcsolja be az egyes csomópontokat, és inicializálja őket a gyökérjelszavak beállításával. 
> [!div class="nextstepaction"]
> [Kezdeti jelszavak beállítása](fxt-node-password.md)
