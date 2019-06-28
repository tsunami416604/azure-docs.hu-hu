---
title: A Microsoft Azure FXT Edge Filer hálózati kapcsolatok és tápegység
description: A hálózati portok kábelezése és kiemelt Azure FXT Edge Filer hardver csatolása
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 444835a94559a352bfd749cfa1cb2cd8c3a39373
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450309"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Oktatóanyag: Hálózati kapcsolatok létrehozása, és adja meg az Azure FXT Edge Filer csomópontra power

Ez az oktatóanyag bemutatja, hogyan kábelezése egy Azure FXT Edge Filer hardver csomópont esetében a hálózati kapcsolatokat.

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * A hálózati kábel a környezet típusát kiválasztása
> * Kapcsolódás az Azure FXT Edge Filer csomópont az adatközponti hálózat
> * Útmutató a kábelek keresztül a kábel (CMA) felügyeleti ága
> * Csatlakozás a power az racked eszközre, és kapcsolja be

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt telepíteni kell az Azure FXT Edge Filer egy standard berendezések állványra. A CMA kell telepíteni a filer csomóponton. 

## <a name="identify-ports"></a>Portok azonosítása

A különböző portok az Azure FXT Edge Filer hátoldalán azonosítani. 
 
![Cabled eszköz oldalán](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Az eszköz bekábelezése

* Csatlakozás a RJ-45 portokat az Adatközpont hálózati forrás leírtak szerint [hálózati portok](#network-ports).  
* Biztonságos kapcsolódás a [iDRAC port](#idrac-port) egy biztonságos DHCP-kiszolgáló különálló hálózathoz. 
* Az USB-porttal és a VGA-port használatával csatlakoztassa a billentyűzetet és a figyelő a csomópontra a kezdeti beállítás. Meg kell elindítania a csomópont és [egy kezdeti jelszó megadása](fxt-node-password.md) aktiválása a csomópont a más portok. Olvasási [kezdeti jelszó](fxt-node-password.md) részleteiről. 

Ez a cikk is ismerteti, hogyan [csatlakozás hálózati áramellátás Visszaállt](#connect-power-cables) a csomópont. 

Ez a cikk is ismerteti a csomóponthoz csatlakozás [soros port](#serial-port-only-when-necessary), de a soros port csak speciális hibaelhárításhoz. 

### <a name="network-ports"></a>Hálózati portok 

Minden Azure FXT Edge Filer csomópont tartalmazza az alábbi hálózati portok: 

* Hat 25GbE/darab 10 gbe sebességű kettős forgalmi adatok portok: 

  * Két kétportos beépülő modul hálózati adapter által biztosított négy port
  * "Mezzanine" formátumú alaplapi hálózati adapter által biztosított két port 

* "Mezzanine" formátumú alaplapi hálózati adapter által biztosított két 1 gbe portok 

A nagy sebességű 25GbE/darab 10 gbe-adatok portokat szabványos SFP28-kompatibilis célra rendelkezik. Optikai kábelek használatához telepítenie kell a SFP28 optikai adó modulok (nincs megadva).

Az 1 gbe portok RJ-45 standard szintű összekötők rendelkezik.

Támogatott adó, kábelek és kapcsolók consult teljes listáját a [Cavium FastlinQ 41000 sorozat együttműködési mátrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

A rendszer használandó kapcsolattípusokat attól függ, hogy az adatközponti környezetben.

* Ha 25GbE hálózathoz csatlakozik, kábelezése a nagy sebességű adatok portok minden egyes a következő kábel típusok egyikével:

  * Optikai kábel, és optikai adó SFP28 25GbE vagy kettős arány 25GbE/darab 10 gbe képesség
  * SFP28 típus 25GbE képességgel közvetlen Iker koaxiális kábelt csatolása

* Ha a 10 gbe hálózati csatlakozással, kábelezése minden, a nagy sebességű adatok portok az alábbiak egyikét: 

  * Optikai kábel, és optikai adó SFP28 darab 10 gbe vagy kettős arány 25GbE/darab 10 gbe képesség.
  * SFP28 típus 25GbE képességgel közvetlen Iker koaxiális kábelt csatolása
  * SFP28 típus 10 gbe-kompatibilis közvetlen Iker koaxiális kábelt csatolása

* Az 1 gbe hálózati portok szolgálnak a fürt felügyeleti forgalmat. Ellenőrizze a **1 GB-os mgmt hálózati** lehetőséget a fürt létrehozásakor (ismertetett [konfigurálása a felügyeleti hálózat](fxt-cluster-create.md#configure-the-management-network)). Kábelezése a portokat szabványos Cat5 vagy jobb kábellel, a támogatott kábelek lista leírtak szerint.

  Hagyhatja az uncabled, ha azt tervezi, hogy a nagy sebességű portok használata az összes forgalom 1gbe-es portokat. Alapértelmezés szerint az 1 gbe hálózati portok nem használ, ha egy nagyobb sebességű adatok port érhető el.  

### <a name="idrac-port"></a>iDRAC port  

A címkével ellátott iDRAC port, amely lehetővé teszi a kommunikációt egy távelérési vezérlővel használt hardver felügyeleti és monitorozási 1 GB-os kapcsolat. FXT szoftver használ az Intelligent Platform Management Interface (IPMI) Ez a vezérlő hibaelhárításhoz és a helyreállítás. Használhatja a beépített [iDRAC felület](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) figyelése hardver ezen a porton keresztül. iDRAC és IPMI hozzáférés alapértelmezés szerint engedélyezve vannak. 

> [!Note]
> A iDRAC port figyelmen kívül hagyása az operációs rendszer és a hardver, a csomópont közvetlenül kommunikál. 

Ezek a biztonsági stratégiák csatlakoztatása, vagy a iDRAC port konfigurálása során használja:

* Csak kapcsolódás iDRAC portok egy, a fürt eléréséhez használt hálózati adatokat fizikailag elkülönített hálózattal.
* Egy biztonságos iDRAC rendszergazdai jelszó beállítását minden egyes csomóponton. Ezt a jelszót, aktiválja a hardvert – a kapott utasításokat követve be kell [hardver jelszó](fxt-node-password.md).
* Az alapértelmezett iDRAC port konfigurációja használ a DHCP- és IPv4 IP-cím hozzárendelését. Ellenőrizze, hogy a DHCP-környezet jól védett és, hogy a kapcsolatok korlátozva, DHCP-ügyfelek és a DHCP-kiszolgáló között. (A fürt Vezérlőpult tartalmazza a beállítások módosításához a csomópontok cím konfigurációs módszer a fürt létrehozása után.)
* Hagyja üresen a iDRAC port beállítása "dedikált módba" (alapértelmezett), amely korlátozza a iDRAC/IPMI hálózati forgalmat a dedikált RJ-45 port.

A iDRAC port nem igényel nagy sebességű hálózati kapcsolatot.
  
### <a name="serial-port-only-when-necessary"></a>Soros port (csak szükség esetén)

Bizonyos esetekben a Microsoft Service és a támogatás kiderülhet, való csatlakozáshoz parancsot egy terminálban a csomópont a soros port a probléma diagnosztizálása érdekében.  

Csatlakoztassa a konzolt:

1. Keresse meg az FXT Edge Filer csomópont hátsó sorosport (COM1).
1. Egy NULL értékű modem kábellel ANSI-115 200 bit-8N1 konfigurált parancsot egy terminálban a soros port csatlakozni.
1. Jelentkezzen be a konzolt, és más lépésekkel útmutatása szerint a támogatási csapattal.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>A kábel (CMA) felügyeleti ága útvonal kábelek

Minden Azure FXT Edge Filer csomópont tartalmaz egy választható kábel felügyeleti ága. A CMA leegyszerűsíti a kábel útválasztást, és lehetővé teszi a könnyebb elérhetőség érdekében a váz hátuljára anélkül, hogy leválasztja a kábelek. 

Kövesse ezeket az utasításokat a kábelek keresztül a CMA útvonal: 

1. Használja a megadott tie burkolja, csomagot a kábelek együtt, adja meg, és lépjen ki a kosarak, így azok nem befolyásolják a szomszédos rendszerek (1).
1. A szolgáltatás helyzetben CMA, az útvonal a kábel csomagot a belső és külső kosarak (2) keresztül.
1. A kosarak végén az előre telepített hook és loop hevederek használatával biztonságossá tétele a kábelek (3).
1. A CMA párhuzamos visszaimportálni a tálca (4) helyen.
1. Az előre telepített állapotát mutató kábellel hátulján a rendszerben található, és gondoskodhat a kábel átirányítására a CMA keresztül. A külső CMA kosárhoz (5) sarkában csatolása a kábel másik végén. 

   > [!CAUTION]
   > A kábelek kiálló potenciális károknak elkerülése érdekében biztonságos bármely Slack-en az állapotát jelző kábelen után ez kábelen keresztül a CMA útválasztást. 

![Telepített kábelekkel CMA ábrája](media/cma-cabling-scan-400.png)

> [!NOTE]
>  Ha nem telepíti a CMA, használja a két hook, és a sín Kit irányíthatja a kábelek, a rendszer hátulján található a megadott hevederek ikonjához.
> 
>  1. Keresse meg a külső CMA szögletes mindkét rack karimával belső oldalára.
>  2. A kábelek óvatosan, kötegeli őket lekérése, és a bal oldalon, a rendszer összekötők törlése.
>  3. A hozzászóláslánc a hook és loop hevederek tooled tárolóhely a a külső CMA szögletes zárójelek, a rendszer a kábel csomagjaiból biztonságossá minden oldalán keresztül.
> 
>     ![Átirányítva a CMA nélkül kábelek](media/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>IP-cím követelményeiről

Hardveres csomópontok egy Azure FXT Edge Filer hibrid tárolási gyorsítótárban, az IP-címeket a fürt szoftvere kezeli.

Minden egyes csomópont szükséges legalább egy IP-címet, de a csomópont-címeket rendel csomópontok hozzáadásakor vagy eltávolítja a fürtről. 

A szükséges IP-címek teljes száma attól függ, hogy a gyorsítótár alkotó a csomópontok számát. 

A Vezérlőpult szoftver használatával, a csomópontok telepítése után állítsa be az IP-címtartományt. További tudnivalókért olvassa el a [Információgyűjtés a fürt](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Tápkábelek csatlakoztatása

Minden egyes Azure FXT Edge Filer csomópont két ellátási egységek (PSUs) használ. 

> [!TIP] 
> A két redundáns PSUs kihasználásához csatolása minden AC tápkábelt egy power terjesztési egységek (PDU) egy független ág kapcsolatcsoportban.  
> 
> UPS használhatja a PDU-k, az extra védelem adatelérésnek köszönhetően. 

1. A csomagban foglalt tápkábelek csatlakozhat a PSUs a váz a. Győződjön meg arról, hogy a zsinór és PSUs teljes illeszkedik. 
1. A tápkábelek csatlakoztatása az Áramelosztó egységek az a készülék állvány. Ha lehetséges használjon két különböző áramforrásokhoz esetében az két összes vezetéket. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Az Azure FXT Edge Filer csomópont bekapcsolása

A csomópont bekapcsolására, nyomja meg az előtérben, a rendszer. A gomb a jobb oldalon Vezérlőpulton nem. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Az Azure FXT Edge Filer csomópont leállítása

Főkapcsoló használható a tesztelés során, és a egy fürthöz való hozzáadása előtt a rendszer leállítása. Azonban után az Azure FXT Edge Filer csomópont van egy fürt részeként, használja a fürt panel szoftver a hardver leállítása. Olvasási [Azure FXT Edge Filer hardver biztonságosan leállására hogyan](fxt-power-off.md) részleteiről. 

## <a name="next-steps"></a>További lépések

Miután Befejezés kábelezést hardver, az összes csomóponton a power és inicializálása azokat a legfelső szintű jelszavuk megadásával. 
> [!div class="nextstepaction"]
> [Kezdeti jelszó beállítása](fxt-node-password.md)
