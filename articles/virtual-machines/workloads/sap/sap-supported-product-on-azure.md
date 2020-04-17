---
title: 'SAP az Azure-ban: Milyen SAP-szoftver támogatott az Azure-ban'
description: Bemutatja, hogy milyen SAP-szoftverek támogatottak az Azure-ban való üzembe helyezéshez
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 56341affee721b3ee6676da401e03dbbca84e597
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538239"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Milyen SAP-szoftverek támogatottak az Azure-telepítésekhez?
Ez a cikk bemutatja, hogyan megtudhatja, hogy milyen SAP-szoftverek támogatottak az Azure-telepítésekhez, és mik a szükséges operációsrendszer-kiadások vagy DBMS-kiadások.

Kiértékelése, hogy a jelenlegi SAP-szoftver támogatott- e, és milyen operációsrendszer- és DBMS-kiadások támogatottak az Sap-szoftverrel az Azure-ban, hozzá kell férnie a következőkhöz:

- SAP támogatási megjegyzések
- SAP-termék rendelkezésre állási mátrixa



## <a name="general-restrictions-for-sap-workload"></a>Az SAP-munkaterhelés általános korlátozásai
Az SAP-munkaterheléshez használható Azure IaaS-szolgáltatások x86-64 vagy x64 hardverre korlátozódnak. Nincs sparc vagy power CPU alapú ajánlatok, amelyek az SAP számítási feladatokra vonatkoznak. Azoknak az ügyfeleknek, akik olyan hardverarchitektúráknak megfelelő operációs rendszereken futtatják az alkalmazásaikat, mint az IBM nagyszámítógép vagy az AS400, vagy ahol a HP-UX, a Solaris vagy az AIX operációs rendszerek vannak használatban, sap-alkalmazásaikat, beleértve a DBMS-t is, a következő operációs rendszerek egyikére kell változtatniuk:

- Windows server 64bit az x86-64 platformhoz
- SUSE linux 64bit az x86-64 platformhoz
- Red hat Linux 64Bit az x86-64 platform
- Oracle Linux 64bit az x86-64 platformhoz

Az SAP-szoftverrel kombinálva más operációs rendszer-kiadások vagy Linux-disztribúciók nem támogatottak. Az egyes verziók és esetek pontos részleteit a dokumentum későbbi részében dokumentálják.


## <a name="you-start-here"></a>Itt kezded.
A kiindulási pont az [SAP támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ahogy végig ezt az SAP-jegyzetet fentről lefelé, számos területen támogatott szoftver és virtuális gépek jelennek meg

Az első szakasz felsorolja a minimális követelményeket a működési kiadások, amelyek támogatják az SAP-szoftverek az Azure-beli virtuális gépek általában. Ha nem éri el ezeket a minimális követelményeket, és futtatja ezeknek az operációs rendszereknek a régebbi kiadásait, frissítenie kell az operációs rendszer kiadását egy ilyen minimális kiadásra vagy még újabb kiadásokra. Helyes, hogy az Azure általában támogatná néhány ilyen operációs rendszer régebbi kiadásait. A felsorolt korlátozások vagy minimális kiadások azonban a végrehajtott teszteken és képesítéseken alapulnak, és nem fognak tovább kiterjeszteni. 


> [!NOTE]
>Vannak bizonyos virtuálisgép-típusok, HANA nagy példányok vagy SAP-számítási feladatok, amelyek újabb operációsrendszer-kiadásokra lesznek szükség. Az ilyen esetek et az egész dokumentum ban megemlítik. Az ilyen esetek egyértelműen dokumentálva vannak az SAP-jegyzetekben vagy más SAP-kiadványokban.

Az alábbi szakasz az általános SAP-platformokat sorolja fel, amelyek támogatottak és fontosabbak a támogatott SAP kernelekkel. Felsorolja a NetWeaver/ABAP vagy Java halmokat, amelyek támogatottak, és amelyek minimális kernelkiadásokat igényelnek. Az Újabb ABAP-halmok támogatottak az Azure-ban, de nincs szükség minimális kernelkiadásokra, mivel az Azure-beli módosítások a legújabb halmok fejlesztésének kezdetétől bevezetve vannak.

Ellenőriznie kell:

- Függetlenül attól, hogy az SAP-alkalmazások futtatja, a megadott minimális kiadások hatálya alá tartozó. Ha nem, meg kell határoznia egy új célkiadást, ellenőrizze az SAP-termék rendelkezésre állási mátrixát, hogy milyen operációs rendszer-buildeket és DBMS-kombinációkat támogat az új célkiadás. Szóval, hogy kiválaszthatja a megfelelő operációs rendszer kiadása és DBMS kiadás
- Az, hogy frissítenie kell-e az SAP-kerneleket az Azure-ba való áthelyezéskor
- Hogy frissítenie kell-e az SAP támogatási csomagjait. Különösen alaptámogatási csomagok, amelyek szükségesek lehetnek olyan esetekben, amikor egy újabb DBMS kiadásra kell áthelyezni


A következő szakasz további részleteket talál más SAP-termékekről és DBMS-kiadásokról, amelyeket az SAP támogat Windows és Linux Azure-ban. 

> [!NOTE]
> A különböző ADATBÁZIS-rendszer minimális kiadásait gondosan választják ki, és előfordulhat, hogy nem mindig tükrözik a különböző DBMS-szállítók által támogatott különböző adatbázis-gyártók által az Azure-ban általában. Számos SAP-munkaterheléssel kapcsolatos szempontot figyelembe vettek a minimális kiadások meghatározásához. Nincs erőfeszítés a régebbi DBMS-kiadások tesztelésére és minősítésre. 

> [!NOTE]
> A felsorolt minimális kiadások az operációs rendszerek és az adatbázis-kiadások régebbi verzióját képviselik. Javasoljuk, hogy használja a legújabb operációs rendszer kiadások és adatbázis-kiadások. Sok esetben az újabb operációs rendszerek és az adatbázis-kiadások figyelembe vették a nyilvános felhőben való futtatás használati esetét, és a kódot úgy alakították ki, hogy optimalizálja a nyilvános felhőben vagy pontosabban az Azure-ban való futtatást.

## <a name="oracle-dbms-support"></a>Oracle DBMS támogatás
Az operációs rendszer, az Oracle DBMS-kiadások és az Azure-ban támogatott Oracle funkciók külön felsorolva vannak az [SAP támogatási #2039619.](https://launchpad.support.sap.com/#/notes/2039619) Essence ki, hogy a megjegyzés lehet összefoglalni, mint:

- A NetWeaver minősítéssel rendelkező Azure virtuális gépeken támogatott minimális Oracle-kiadás az Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Vendég operációs rendszerként csak a Windows és az Oracle Linux jogosult. Az operációs rendszer pontos kiadásai és a kapcsolódó minimális DBMS-kiadások a megjegyzésben találhatók.
- Az Oracle Linux támogatása az Oracle DBMS kliensre is kiterjed. Ez azt jelenti, hogy az Összes SAP-összetevőnek, például az ABAP vagy a Java Stack párbeszédpéldányainak is oracle Linuxon kell futnia. Csak az ilyen SAP rendszeren belüli OLYAN SAP-összetevők futtathatnak egy másik Linux operációs rendszert, amelyek nem csatlakoznának az Oracle DBMS-hez.
- Az Oracle RAC nem támogatott 
- Az Oracle ASM néhány esetben támogatott. A részletek a jegyzetben vannak felsorolva
- A nem Unicode SAP rendszereket csak a Windows vendég operációs rendszerrel futó alkalmazáskiszolgálók támogatják. A DBMS vendég operációs rendszere lehet Oracle Linux vagy Windows. Ennek a korlátozásnak az oka nyilvánvaló az SAP-termék rendelkezésre állási mátrixának (PAM) ellenőrzésekor. Az Oracle Linux esetében az SAP soha nem adott ki nem Unicode SAP kerneleket

A megcélzott Azure-infrastruktúrával támogatott DBMS-kiadások ismerete érdekében ellenőriznie kell az SAP-termék rendelkezésre állási mátrixát, hogy az operációs rendszer kiadásai és a szükséges DBMS-ek támogatottak-e a futtatni kívánt SAP-termékkiadások. 


## <a name="sap-hana-support"></a>SAP HANA támogatás
Az Azure-ban két szolgáltatás, amely a HANA-adatbázis futtatásához használható:

- Azure-alapú virtuális gépek
- [HANA nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Az SAP HANA futtatásához az SAP-nak több és erősebb feltételnek kell megfelelnie, mint a NetWeaver vagy más SAP-alkalmazások és DBMS futtatásához. Ennek eredményeképpen kisebb számú Azure-virtuális gépek jogosultak az SAP HANA DBMS futtatására. Az SAP HANA által támogatott támogatott Azure-infrastruktúra listája megtalálható az úgynevezett [SAP HANA hardverkönyvtárban.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

> [!NOTE]
> Az "S" betűvel kezdődő egységek [HANA nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) egységek. 

> [!NOTE]
> Az SAP nem rendelkezik az SAP HANA fő kiadásoktól függő specifikus minősítéssel. Ellentétben a közös vélemény, az oszlop **tanúsítási forgatókönyv** a [HANA tanúsított IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), az oszlop nem tesz **nyilatkozatot a HANA nagy vagy kisebb kiadás tanúsított**. Azt kell feltételezni, hogy a HANA 1.0 és hana 2.0-hoz használható összes felsorolt egységek, mindaddig, amíg az adott egységek hitelesített operációs rendszer kiadásait a HANA 1.0 kiadások is támogatják. 

Az SAP HANA használata, különböző minimális operációs rendszer kiadások vonatkozhatnak, mint az általános NetWeaver esetekben. Az egyes egységek támogatott operációs rendszereit külön-külön kell ellenőriznie, mivel ezek eltérőek lehetnek. Ön ezt kattintva minden egység. További részletek jelennek meg. A felsorolt részletek egyike az adott egységhez támogatott különböző operációs rendszerek.

> [!NOTE]
> Az Azure HANA nagypéldány-egységek az Azure virtuális gépekhez képest szigorúbbak a támogatott operációs rendszerekkel. Másrészt az Azure virtuális gépek kényszerítheti újabb működési kiadások minimális kiadások. Ez különösen igaz néhány nagyobb virtuálisgép-egységre, amelyek a Linux kernelek módosítását igényelik

Az Azure-infrastruktúra támogatott operációs rendszerének ismerete érdekében ellenőriznie kell az [SAP támogatási megjegyzését #2235581](https://launchpad.support.sap.com/#/notes/2235581) a pontos SAP HANA-kiadásokés javítási szintek, amelyek támogatják a célzásáltal támogatott Azure-egységek. 

> [!IMPORTANT]
> A lépés a pontos SAP HANA kiadások és a támogatott javítási szintek ellenőrzése nagyon fontos. Sok esetben egy bizonyos operációs rendszer kiadásának támogatása az SAP HANA végrehajtható fájlok adott javítási szintjétől függ.

Mivel tudja, hogy a célzott Azure-infrastruktúrán futtatható hana-kiadások, ellenőrizze az SAP-termék rendelkezésre állási mátrixát, hogy megtudja, vannak-e korlátozások az SAP-termékkiadásokkal, amelyek támogatják a kiszűrt HANA-kiadásokat


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Hitelesített Azure-virtuális gépek és HANA nagypéldány-egységek és üzleti tranzakciós átviteli
A támogatott operációsrendszer-kiadások, a DBMS-kiadások és az Azure-infrastruktúra-egységek függő sap-szoftverkiadásainak kiértékelése mellett szükség van arra, hogy ezeket az egységeket üzleti tranzakciós átviteli keresztültel minősítse, amelyet az SAP az "SAP" egységben fejez ki. Az SAP-méretezés az SAPS-számításoktól függ. A meglévő SAP-rendszerek kiértékelése, általában az infrastruktúra-szolgáltató segítségével kiszámíthatja az egységek SAPS-ét. A DBMS-réteghez és az alkalmazásréteghez egybeesve. Más esetekben, ahol új funkciók jönnek létre, az SAP-val végzett méretezési gyakorlat felfedheti az alkalmazásréteg és a DBMS-réteg szükséges SAPS-számait. Infrastruktúra-szolgáltatóként a Microsoft köteles biztosítani a NetWeaver és/vagy HANA tanúsítvánnyal rendelkező különböző egységek SAP átviteli jellemzőjét.

Az Azure virtuális gépek esetében ezek az SAPS-átviteli műveletek számai az [SAP támogatási megjegyzése #1928533](https://launchpad.support.sap.com/#/notes/1928533)dokumentálva vannak. Az Azure HANA nagy példányegységek esetében az SAPS átviteli műveletek számai dokumentálva vannak az [SAP támogatási megjegyzésében #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Az [SAP támogatási megjegyzésének #1928533](https://launchpad.support.sap.com/#/notes/1928533)a következő megjegyzések érvényesek:

- **M-sorozatú Azure virtuális gépek és Mv2 sorozatú Azure-virtuális gépek esetén különböző minimális operációsrendszer-kiadások érvényesek, mint más Azure virtuálisgép-típusok esetében.** Az újabb operációsrendszer-kiadások követelménye az operációs rendszer különböző szállítóiáltal az operációs rendszer kiadásaiban megadandó módosításokon alapul, hogy vagy engedélyezzék az operációs rendszerüket az adott Azure Virtuálisgép-típusokon, vagy optimalizálják az SAP-munkaterhelés teljesítményét és átviteli teljesítményét ezeken a virtuálisgép-típusokon
- Két tábla, amely meghatározza a különböző virtuálisgép-típusok. A második tábla saps átviteli kapacitást határoz meg az Azure virtuálisgép-típusokhoz, amelyek csak az Azure standard storage-ot támogatják. A DBMS telepítése a feljegyzés második táblázatában megadott egységeken nem támogatott


## <a name="other-sap-products-supported-on-azure"></a>Az Azure-ban támogatott egyéb SAP-termékek
Általában az a feltételezés, hogy az azure-hoz hasonló nagy kapacitású felhők állapotával az SAP-szoftverek nagy részének funkcionális problémák nélkül kell futnia az Azure-ban. Mindazonáltal, és a privát felhőmegjelenítés, az SAP továbbra is támogatja a különböző SAP-termékek kifejezetten a különböző hyerpscale felhőszolgáltatók. Ennek eredményeképpen különböző SAP-támogatási megjegyzések jelzik az Azure támogatását a különböző SAP-termékekhez. 

Business Objects BI platform esetén [az SAP támogatási megjegyzése #2145537](https://launchpad.support.sap.com/#/notes/2145537) az Azure-ban támogatott SAP Business Objects-termékek listáját adja meg. Ha olyan kérdések merülnek fel a szoftverkiadások és operációsrendszer-kiadások összetevőivel vagy kombinációival kapcsolatban, amelyek látszólag nem szerepelnek a listán vagy nem támogatottak, és amelyek újabbak, mint a felsorolt minimális kiadások, meg kell nyitnia egy SAP támogatási kérelmet az összetevővel szemben, amelyhez támogatást kér.

Az Üzleti objektumok adatszolgáltatások esetén [az SAP támogatási megjegyzése #22288344](https://launchpad.support.sap.com/#/notes/2288344) ismerteti az Azure-on futó SAP Data Services minimális támogatását. 

> [!NOTE]
> Ahogy az SAP támogatási megjegyzés, be kell jelentkeznie az SAP PAM azonosítani a megfelelő támogatási csomag szintjét támogatni kell az Azure-ban

Az SAP Datahub/Vora támogatása az Azure Kubernetes-szolgáltatásokban (AKS) az [SAP támogatási megjegyzésében](https://launchpad.support.sap.com/#/notes/2464722) #2464722

Az SAP BPC 10.1 SP08 támogatása az [SAP támogatási #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Az SAP Hybris Commerce Platform azure-beli támogatását a [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud)részletezi. Mivel a támogatott DBMS az SAP Hybris Commerce Platform, felsorolja, mint:

- SQL Server és oracle a Windows operációs rendszer platformján. Ugyanazok a minimális kiadások érvényesek, mint az SAP NetWeaver esetében. A részleteket az [SAP támogatási #1928533](https://launchpad.support.sap.com/#/notes/1928533) tartalmazza.
- SAP HANA a Red Hat és a SUSE Linux. Sap HANA tanúsítvánnyal rendelkező virtuálisgép-típusok szükségesek a [dokumentum](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support)korábbi dokumentumainak szerint. Az SAP (Hybris) Kereskedelmi Platform OLTP-munkaterhelésnek minősül




## <a name="next-steps"></a>Következő lépések
Olvassa el a következő lépéseket az [Azure virtuális gépek tervezésében és megvalósításában az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

