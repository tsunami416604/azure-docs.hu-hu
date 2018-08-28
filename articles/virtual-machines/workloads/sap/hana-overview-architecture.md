---
title: Áttekintés és az SAP HANA az Azure-ban (nagyméretű példányok) architektúrája |} A Microsoft Docs
description: Az architektúra áttekintése, amelyekkel telepítheti az SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d28b6ea5612a3db539c51d2603c3f12282ca519
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090416"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az SAP HANA az Azure-ban (nagyméretű példányok)?

SAP HANA az Azure-ban (nagyméretű példányok) egy olyan egyedi megoldás az Azure-bA. Virtuális gépek üzembe helyezéséhez és futtatásához az SAP HANA amellett Azure kínál futtatásához és az SAP HANA telepítése operációs rendszer nélküli kiszolgálókon, amelyek dedikált, lehetőségét. Az SAP HANA az Azure-ban (nagyméretű példányok) megoldás, amely hozzá van rendelve, nem megosztott gazdakiszolgáló/operációs rendszer nélküli hardver épül. A kiszolgáló hardveres be van ágyazva, amelyek tartalmazzák a számítási és a kiszolgáló, hálózati és tárolási infrastruktúra nagyobb stampek. Együttes, mint certified szabott HANA data center integrálása (TDI). SAP HANA az Azure-ban (nagyméretű példányok) a másik kiszolgáló SKU-k vagy méretű kínál. Egységek rendelkezhet 36 Intel processzormag és 768 GB memória és a go egységig, amely legfeljebb 480 Intel processzormag szükséges és legfeljebb 24 TB memóriával.

Az ügyfelek elkülönítésével belül az infrastruktúra-blokk bérlők, történik, amely a következőhöz hasonló:

- **Hálózatkezelés**: ügyfeleket infrastruktúra stack keresztül hozzárendelt ügyfél bérlői virtuális hálózatok elkülönítését. A bérlőhöz hozzá van rendelve, egyetlen ügyfél számára. Egy ügyfél több bérlő is rendelkezhet. Akkor is, ha az adott ügyfél tartozik a bérlők bérlők hálózati elkülönítése meggátolja, hogy az infrastruktúra-blokk szintű bérlők közötti hálózati kommunikáció.
- **Tároló-összetevők**: storage virtuális gépek, amelyek rendelkeznek a hozzájuk rendelt tárolóköteteket elkülönítését. Tároló kötetek csak egy virtuális géphez rendelhető. A storage virtuális gépek kizárólag a az SAP HANA TDI minősített infrastruktúra veremben egy egybérlős jogosultságot. Ennek eredményeképpen egy meghatározott és kapcsolódó bérlő csak tárolási virtuális géphez hozzárendelt, annak tárolóköteteket elérhető. Nem látható a különböző telepített bérlők között.
- **Kiszolgáló vagy a gazdagép**: egy kiszolgáló vagy a gazdagép egység nem megosztott, ügyfelei vagy a bérlők között. A kiszolgáló vagy a gazdagép egy ügyfél telepítve egy atomi operációs rendszer nélküli számítási egység, amely egy egybérlős van rendelve. *Nem* hardver particionálás, vagy helyreállítható particionálást használnak, amely azt eredményezheti, hogy a gazdagép vagy a kiszolgáló oszt meg egy másik ügyféllel. Az adott bérlő, a tárolás virtuális géphez rendelt tárolóköteteket ilyen kiszolgálóhoz csatlakoztatva vannak. Egy bérlő rendelkezhet több kiszolgáló egységek kizárólag hozzárendelt különböző SKU-k számát.
- Számos különböző bérlőn belül egy SAP HANA az Azure-ban (nagyméretű példányok) infrastruktúra blokk, üzembe és egymással szembeni keresztül a bérlő hálózati, tárolási és számítási szintjének fogalmak elkülönített. 


Ezek az operációs rendszer nélküli kiszolgáló egységek csak a SAP HANA futtatásához támogatottak. Az SAP alkalmazásréteg vagy a számítási feladatok szoftverek középső réteg fut a virtuális gépeken. A szolgáltatások az Azure gerinchálózatra kapcsolódnak, hogy az SAP HANA futtatása az Azure-ban (nagyméretű példányok) egység infrastruktúra stampek. Ily módon az SAP HANA az Azure-ban (nagyméretű példányok) egységek és a virtuális gépek közötti kis késleltetésű kapcsolatot biztosítja.

Ez a dokumentum több dokumentumot, amelyek bemutatják az SAP HANA az Azure-ban (nagyméretű példányok) egyike. Ez a dokumentum bemutatja az alapszintű architektúra, feladatok és a megoldás által nyújtott szolgáltatások. A megoldás magas szintű képességeit is ismertetik. A legtöbb más területeken, például a hálózat és a kapcsolatot négy egyéb dokumentumokat részleteit és a részletes adatokat terjed ki. SAP Hana az Azure-ban (nagyméretű példányok) a dokumentáció nem fedi le, az SAP NetWeaver-telepítés szempontok vagy virtuális gépeken futó SAP NetWeaver központi telepítései. SAP NetWeaver az Azure-ban ugyanabban az Azure dokumentációja a tárolóban található külön dokumentumokra vonatkozik. 


A különböző dokumentumokon nagyméretű HANA-példány útmutató terjed ki a következő területeken:

- [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Telepítse és konfigurálja az SAP HANA (nagyméretű példányok) az Azure-ban](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagyméretű példányok) hibaelhárítási és figyelési az Azure-ban](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Magas rendelkezésre állás beállítása a STONITH használatával a SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Az operációs rendszer biztonsági mentési és visszaállítási II. típusú termékváltozatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Meghatározások

Több közös definíciók széles körben használják az architektúra és üzembe helyezési útmutatót. Vegye figyelembe a következő kifejezéseket és azok jelentését ismerteti:

- **IaaS**: infrastruktúra-szolgáltatás.
- **PaaS**: szolgáltatásként nyújtott platformon.
- **SaaS**: szolgáltatott szoftver.
- **Az SAP-összetevő**: egyedi SAP alkalmazások, például az ERP központi összetevő (ECC), a Business Warehouse (BW), a megoldás Manager vagy a vállalati portál (EP). Az SAP-összetevők hagyományos ABAP és Java-technológiák vagy egy nem NetWeaver-alapú alkalmazás, például az üzleti objektumok alapulhat.
- **Az SAP-környezet**: egy vagy több SAP összetevő logikusan való alkalmasság vállalati, például fejlesztési, minőségbiztosítási, képzés, vész-helyreállítási vagy éles környezetben.
- **SAP-rendszeren**: hivatkozik a teljes SAP-eszközök az informatikai környezet változásaihoz. Az SAP-rendszeren, tartalmazza az összes éles környezetben, és nem éles környezetekben.
- **SAP-rendszerhez**: DBMS-réteg és a, például az SAP ERP fejlesztési rendszer, az SAP BW-teszt rendszer és az SAP CRM éles rendszer alkalmazásréteg kombinációja. Azure-környezetek nem támogatják a értékkel való osztásának e két réteget a helyszíni és az Azure között. Egy SAP-rendszerrel, üzembe helyezett helyszíni vagy annak az Azure-ban üzembe helyezett. A különböző rendszerek, az SAP-rendszeren az Azure-ban vagy a helyszíni telepítheti. Például telepítheti az SAP CRM fejlesztési és rendszerek tesztelése az Azure-ban, miközben az SAP CRM rendszert a helyi környezetben telepíti. Az SAP Hana az Azure-ban (nagyméretű példányok) célja, hogy az SAP alkalmazásréteg-alapú virtuális gépeken futó SAP-rendszereit és a egy egységet az SAP HANA az Azure-ban (nagyméretű példányok) stamp a kapcsolódó SAP HANA-példány üzemeltetése.
- **Nagyméretű szolgáltatáspéldányban**: egy, az SAP HANA TDI-tanúsítvánnyal és futtatásához az Azure-beli SAP HANA-példányok dedikált hardver infrastruktúra stack.
- **SAP HANA az Azure-ban (nagyméretű példányok):** az SAP HANA TDI-tanúsítvánnyal rendelkező hardverek nagyméretű példány stampek különböző Azure-régióban üzembe helyezett példánya hivatalos név az ajánlat az Azure-beli HANA futtatásához. A kapcsolódó kifejezés *nagyméretű HANA-példány* rövid a *SAP HANA az Azure-ban (nagyméretű példányok)* és széles körben használatos a műszaki telepítési útmutatóban.
- **Létesítmények közötti**: egy olyan forgatókönyvet, ahol a virtuális gépek Azure-előfizetéssel, amely rendelkezik a site-to-site, többhelyes vagy Azure ExpressRoute-kapcsolat a helyszíni adatközpontok és az Azure közötti üzembe ismertet. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések is rendelkezésre állnak, létesítmények közötti forgatókönyvek leírtak szerint. A kapcsolat az az oka, hogy kiterjesztése a helyszíni tartományokra, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS az Azure-bA. Az Azure-előfizetések az Azure-objektumok kiterjed a helyszíni környezet változásaihoz. Ez a bővítmény a virtuális gépeket a helyszíni tartomány része lehet. 

   Tartományi felhasználókat a helyszíni tartomány elérheti a kiszolgálók és szolgáltatások futtatása a virtuális gépeken (például adatbázis-kezelő szolgáltatások). Virtuális gépek közötti kommunikációt és a névfeloldás üzembe helyezte a helyszínen és az Azure által telepített virtuális gépek lehetséges. Ebben a forgatókönyvben jellemző, amelyben a legtöbb SAP-eszközök vannak üzembe helyezve módja. További információkért lásd: [megtervezése és kialakítása az Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [virtuális hálózat létrehozása helyek közötti kapcsolattal az Azure portal használatával](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Bérlő**: HANA nagyméretű szolgáltatáspéldányban telepített ügyfél lekérdezi izolált be egy *bérlő.* Egy bérlő van különítve a hálózati, tárolási és számítási rétegben, a többi bérlőtől. A különböző bérlők rendelt tárolási és számítási egységek nem tekintse meg egymással, és nem kommunikálnak egymással, nagyméretű HANA-példány stamp szint. Egy ügyfél lehet váltani, más bérlők üzemelő rendelkezik. Ezután még nem folyik kommunikáció bérlők HANA nagyméretű példányok stamp szint között.
- **Termékváltozat kategória**: A nagyméretű HANA-példányt, a következő két kategória SKU-k érhetők el:
    - **I. osztály típus**: S72, S72m, S144, S144m, S192, S192m és S192xm
    - **Írja be a II osztály**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm és S960m


Számos további erőforrás az SAP számítási feladatok felhőbeli üzembe helyezése a érhetők el. Ha azt tervezi, hajtsa végre az SAP HANA üzembe helyezése az Azure-ban, szüksége lehet hasznosítani és az Azure IaaS és a központi telepítés az SAP számítási feladatok az Azure IaaS tisztában. A folytatás előtt tekintse meg a [használata SAP-megoldások az Azure-beli virtuális gépek](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) további információt. 

## <a name="certification"></a>Tanúsítvány

Mellett a NetWeaver hitelesítő SAP használatához az SAP Hana, SAP HANA támogatásához egyes infrastruktúrákat, például az Azure IaaS speciális minősítés.

Alapvető fontosságú SAP-Jegyzetnek NetWeaver, valamint a párhuzamossági SAP HANA-minősítési program [SAP Megjegyzés #1928533 – SAP alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533).

A minősítési rekordokat az SAP Hana az Azure-ban (nagyméretű példányok) egység megtalálható a [SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) hely. 

Az SAP HANA az Azure-ban (nagyméretű példányok) típusú hitelesített IaaS-platformon hely, az SAP HANA említett biztosítja a Microsoft és ügyfeleink helyezheti üzembe az alkalmazásokat nagy SAP Business Suite, az SAP BW, S/4 HANA, BW/4HANA vagy egyéb SAP HANA számítási feladatok Azure-beli SAP. A megoldás az SAP-HANA jóváhagyott dedikált hardver stamp alapul ([SAP HANA szabott data center integráció – TDI](https://scn.sap.com/docs/DOC-63140)). Ha futtatja az SAP HANA TDI-konfigurált megoldást, a hardveres infrastruktúrát használható, az összes, az SAP HANA-alapú alkalmazások (például SAP Business Suite az SAP HANA, SAP BW on SAP HANA, az S4/HANA és BW4/HANA).

Virtuális gépeken futó SAP HANA képest, ez a megoldás előnye van. Sokkal nagyobb memória kötetek biztosítja. Ez a megoldás engedélyezéséhez kell tennie a következő főbb szempontok megismerése:

- A SAP alkalmazás réteget és az SAP alkalmazások a szokásos Azure-beli hardveres stampek tárolt virtuális gépek futnak.
- Ügyfél a helyszíni infrastruktúra, adatközpontok, és alkalmazás-KözpontiTelepítés csatlakozik a cloud platform (ajánlott) expressroute-on keresztül, vagy virtuális magánhálózati (VPN). Az Active Directory és DNS is kibővíti az Azure-bA.
- Az SAP HANA-adatbázispéldányt HANA számítási futtatja az SAP HANA az Azure-ban (nagyméretű példányok). A nagyméretű szolgáltatáspéldányban be az Azure networking, csatlakoztatva van, így a HANA-példány a nagyméretű HANA-példányt futtató virtuális gépeken futó szoftver használhatják.
- Hardver-vagy SAP HANA az Azure-ban (nagyméretű példányok) dedikált hardverre, a SUSE Linux Enterprise Server vagy a Red Hat Enterprise Linux előtelepített IaaS megadott. Virtuális gépek, a további frissítések és az operációs rendszer karbantartási feladata a.
- HANA vagy SAP HANA nagyméretű HANA-példány egységeinek futtatásához szükséges minden olyan további összetevők telepítését a feladata. Minden folyamatban lévő műveletek megfelelő és az SAP HANA az Azure felügyeleti is rendelkezésre állnak a felhasználó felelőssége.
- Mellett a megoldások az itt leírtak szerint az Azure-előfizetéshez az Azure-ban (nagyméretű példányok) SAP Hana-hoz csatlakozó más összetevők is telepíthető. Példák olyan összetevők, amelyek lehetővé teszik a kommunikációt a, vagy közvetlenül az SAP HANA, helyettesítő kiszolgálókkal, például adatbázis-RDP kiszolgálók, az SAP HANA Studio, a SAP Data Services, az SAP BI forgatókönyvek esetén, vagy hálózati figyelési megoldások.
- Azure-ban, mint a nagyméretű HANA-példány biztosít magas rendelkezésre állású és vész-helyreállítási funkciók támogatása.

## <a name="architecture"></a>Architektúra

Magas szintű az SAP HANA az Azure-ban (nagyméretű példányok) megoldás rendelkezik az SAP alkalmazásrétegre szereplő virtuális gépeket. Az adatbázis réteg található egy nagyméretű szolgáltatáspéldányban ugyanazon Azure-régióban, amely csatlakozik az Azure IaaS SAP TDI-konfigurált hardveren található.

> [!NOTE]
> Helyezze üzembe az SAP alkalmazásrétegre ugyanabban a régióban az Azure az SAP DBMS-összetevővel. Ez a szabály megfelelően legyen dokumentálva az SAP számítási feladatok Azure-on közzétett információt. 

SAP Hana az Azure-ban (nagyméretű példányok) az általános architektúrát biztosít egy SAP TDI-tanúsítvánnyal rendelkező hardverkonfiguráció, amely egy nem virtualizált, operációs rendszer nélküli számítógépen, a nagy teljesítményű kiszolgáló, az SAP HANA-adatbázis. A lehetőség és az erőforrások igény szerint, a SAP alkalmazás réteg skálázása az Azure rugalmasságot is biztosít.

![SAP Hana az Azure-ban (nagyméretű példányok) az architektúra áttekintése](./media/hana-overview-architecture/image1-architecture.png)

Az architektúra látható három részből áll:

- **Jobb**: egy a helyszíni infrastruktúra, amely futtatja a különböző alkalmazások az adatokat, hogy a végfelhasználók számára elérhető ÜZLETÁGI alkalmazások, például SAP központok jeleníti meg. Ideális esetben ez a helyszíni infrastruktúra majd csatlakozik az Azure-bA [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: az Azure IaaS jeleníti meg, és ebben az esetben használata virtuális gépek üzemeltetéséhez a SAP vagy egyéb alkalmazásokhoz, amelyek az SAP HANA használata egy adatbázis-kezelő rendszer. Virtuális gépek kisebb HANA-példányok függvény, amely a virtuális gépek adja meg a memória és az alkalmazási rétegre együtt helyezik üzembe. További információ a virtuális gépek: [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/).

   Az Azure hálózati szolgáltatások használhatók együtt más alkalmazások virtuális hálózatokra SAP-rendszereinket. Ezek a virtuális hálózatok csatlakozhat a helyszíni rendszerek, valamint hogy az SAP HANA az Azure-ban (nagyméretű példányok).

   SAP NetWeaver-alkalmazásaihoz és az Azure-ban támogatott adatbázisok: [SAP támogatási Megjegyzés #1928533 – SAP alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533). Dokumentáció az Azure-beli SAP-megoldások üzembe helyezése lásd:

  -  [SAP használata Windows virtuális gépeken](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Azure virtuális gépeken futó SAP-megoldások használata](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Balra**: az SAP HANA TDI-tanúsítvánnyal rendelkező hardverek bemutatja az Azure-beli nagyméretű példány blokkba. A nagyméretű HANA-példány egységek csatlakozik a virtuális hálózatok, az előfizetés ugyanazt a technológiát használják, mint a kapcsolat a helyszínről az Azure-bA.

Az Azure-beli nagyméretű példány stamp magát a következő összetevőket kombinálja:

- **Számítástechnika**: alapuló Intel Xeon E7-8890v3 vagy Intel Xeon E7-8890v4 processzorok, amely a szükséges számítási képességeket biztosítanak, és az SAP HANA-minősítéssel rendelkező kiszolgálókat.
- **Hálózati**: egy egyesített nagy sebességű hálózati háló kapcsolja össze a számítási, tárolási és hálózati Ébresztés összetevő.
- **Tárolási**: A tárolási infrastruktúra, amely egy egységes hálózati háló keresztül érhető el. Az adott tárolási kapacitás biztosított attól függ, hogy az adott SAP HANA, az üzembe helyezett Azure-ban (nagyméretű példányok) konfigurációja. További tárolási kapacitás további havi költségek érhető el.

A nagyméretű szolgáltatáspéldányban több-bérlős infrastruktúrájában ügyfelek elkülönített bérlők üzemelnek. : Üzembe helyezés a bérlő nevezze el az Azure-előfizetés belül az Azure-regisztrációjában. Az Azure-előfizetés akkor azt, amelyik a nagyméretű HANA-példányt kell kiegyenlíteni. Ezek a bérlők számára az Azure-előfizetéshez 1:1 kapcsolat áll fenn. A hálózat egy nagyméretű HANA-példány egység több bérlőhöz tartozó Azure-előfizetések különböző virtuális hálózatokból egy Azure-régióban üzembe helyezett eléréséhez. Ezeket az Azure-előfizetések az azonos Azure-regisztrációjához kell tartoznia. 

Virtuális gépek, az SAP HANA az Azure-ban (nagyméretű példányok) több Azure-régióban érhető el. A vész helyreállítási funkciókat kínálnak, kiválaszthatja, elvégezni a beléptetést. Egy földrajzi politikai régión belül különböző nagyméretű példány blokkokban kapcsolódnak egymáshoz. Például a HANA nagyméretű példány Bélyegekre az USA nyugati régiója és USA keleti régiójában vész-helyreállítási replikáció dedikált hálózati kapcsolaton keresztül csatlakoznak. 

Ugyanúgy, mint a virtuális gépek különböző típusairól az Azure Virtual Machines közül választhat, választhat a különböző SKU-k a nagyméretű HANA-példányt, amely a különböző számítási feladatok esetében az SAP Hana vannak igazítva. Az SAP az Intel processzorral generációk alapján különböző számítási feladatokhoz a memória, processzor szoftvercsatorna arányok vonatkozik. Az alábbi táblázat a Termékváltozat típusa érhető el.

SAP HANA az Azure-ban (nagyméretű példányok) szolgáltatás több konfiguráció az Azure-régióban az USA nyugati régiója és USA keleti régiója, Kelet-Ausztrália, Délkelet-Ausztrália, Nyugat-Európa, Észak-Európa, kelet-japán és Nyugat-Japánban érhető el.

[Az SAP HANA-tanúsítvánnyal rendelkező SKU-k, HANA nagyméretű példányok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) például a listából:

| SAP-megoldás | CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| Az OLAP-optimalizálva: SAP BW, BW/4hana-t<br /> vagy az SAP HANA általános OLAP számítási feladatokhoz | SAP HANA az Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 processzormagot és 72 CPU szálak |  768 GB |  3 TB | Elérhető |
| --- | SAP HANA az Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 processzormagot és 144 CPU szálak |  1,5 TB |  6 TB | Többé nem érhető el |
| --- | SAP HANA az Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 processzormagot és 192 CPU szálak |  2.0-S TB |  8 TB | Elérhető |
| --- | SAP HANA az Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  4.0 TB |  16 TB | Elérhető |
| OLTP-hez optimalizálva: SAP Business Suite<br /> az SAP HANA vagy az S/4hana-t (OLTP),<br /> általános OLTP | SAP HANA az Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 processzormagot és 72 CPU szálak |  1,5 TB |  6 TB | Elérhető |
|---| SAP HANA az Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 processzormagot és 144 CPU szálak |  3.0 TB |  12 TB | Többé nem érhető el |
|---| SAP HANA az Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 processzormagot és 192 CPU szálak  |  4.0 TB |  16 TB | Elérhető |
|---| SAP HANA az Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  6.0 TB |  18 TB | Elérhető |
|---| SAP HANA az Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  8.0 TB |  22 TB |  Elérhető |
|---| SAP HANA az Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 processzormagot és 576 CPU szálak |  12.0-S TB |  28 TB | Elérhető |
|---| SAP HANA az Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 processzormagot és 768 CPU szálak |  16,0 TB |  36 TB | Elérhető |
|---| SAP HANA az Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 processzormagot és 960 CPU szálak |  20.0 TB |  46 TB | Elérhető |


A SAP HANA TDIv5 SAP specifikus méretezése és ügyfél-specifikus projektek, amely kiszolgálókonfigurációk, amelyek nem szerepelnek az igazolt vezethet teszi lehetővé:

- [Az SAP HANA Certified készülékek](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Az SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Sok esetben ügyfél-specifikus kiszolgáló konfigurációkról biztosítunk több memóriát az SAP-minősítéssel rendelkező kiszolgáló egységeket. SAP dolgozik, az ügyfelek rendelkeznek SAP-támogatás és a saját specifikus méretű kiszolgálókonfigurációk tanúsítása lehetőségét. Az Azure-ban a következő HANA nagyméretű példányok standard termékváltozatok érhetők el, és a Microsoft ár a ilyen TDIv5 specifikus méretezési projektek listája.


| Eredeti Termékváltozat, amely lehet <br /> a kiterjesztett memória | CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| S192m kiterjeszthető | SAP HANA az Azure S192xm<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 processzormagot és 192 CPU szálak |  6.0 TB |  16 TB | Elérhető |
| S384xm kiterjeszthető | SAP HANA az Azure S384xxm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  12.0-S TB |  28 TB | Elérhető |
| S576m kiterjeszthető | SAP HANA az Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 processzormagot és 576 CPU szálak |  18.0 TB |  41 TB | Elérhető |
| S768m kiterjeszthető | SAP HANA az Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 processzormagot és 768 CPU szálak |  24,0 TB |  56 TB | Elérhető |

- CPU-magok = Processzormagok nem – a hyper-threaded vonatkozóan a processzorok, a kiszolgáló egység összege.
- Szálak CPU = Processzormagok többszálú vonatkozóan a kiszolgáló egység a processzor által biztosított számítási szálak összege. A legtöbb egységek használata a Hyper-Threading technológiát alapértelmezés szerint vannak konfigurálva.
- S768m szállítói javaslatai szerinti lépéseket, S768xm és S960m nem konfigurált Hyper-Threading használata az SAP HANA futtatásához.


A kiválasztott konfigurációkkal a számítási feladatok, a Processzor-erőforrások és a kívánt memória függenek. Az OLTP számítási feladatokhoz való felhasználáshoz optimalizált SKU-ja az OLAP-munkaterhelések esetében használjon lehetőség. 

A hardver, az ajánlatok, kivéve a projektek, a specifikus méretezési egység alap olyan SAP HANA TDI-tanúsítvánnyal. Hardver két különböző osztályú oszthatja be a termékváltozatok:

- S72, S72m, S144, S144m, S192, S192m és S192xm, amely a "Type I osztály" nevezik, SKU-k.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm és S960m nevezzük, amely a "Type II osztály" termékváltozatok.

Egy teljes HANA nagyméretű szolgáltatáspéldányban kizárólag egyetlen ügyfél számára nem lefoglalt&#39;s használja. Emiatt a számítási és tárolási erőforrások, valamint az Azure-ban telepített hálózati háló keresztül csatlakozik a rackszekrények vonatkozik. Nagyméretű HANA-példány infrastruktúrát, mint például az Azure, helyez üzembe különböző ügyfél &quot;bérlők&quot; , amelyek elkülönülnek egymástól a következő három szinten:

- **Hálózati**: a HANA nagyméretű szolgáltatáspéldányban belüli virtuális hálózatok elkülönítését.
- **Tárolási**: storage virtuális gépek, amelyek hozzárendelt tárolóköteteket és bérlők között tárolóköteteket elkülönítése elkülönítését.
- **COMPUTE**: egyetlen új bérlő dedikált kiszolgáló egységek hozzárendelését. Nem nehéz vagy helyreállítható particionálás server egységek. Megosztás nélkül egyetlen kiszolgáló vagy a gazdagép egység bérlők között. 

A központi telepítéseket, egységek nagyméretű HANA-példány a különböző bérlők között nem láthatók-e egymáshoz. Üzembe helyezett különböző bérlők HANA nagyméretű példányok egység nem tud közvetlenül kommunikálni egymással, nagyméretű HANA-példány stamp szintjén. Csak nagyméretű HANA-példány egység egy bérlőn belül a nagyméretű HANA-példány stamp szintjén is kommunikálhatnak egymással.

A nagyméretű szolgáltatáspéldányban egy üzembe helyezett tenant számlázási célból van rendelve egy Azure-előfizetéssel. Egy hálózatot akkor érhető el a virtuális hálózatok más Azure-előfizetések belül az azonos Azure-regisztrációjához. Ha telepít egy másik Azure-előfizetésében ugyanazon Azure-régióban, is lehet váltani, kérje meg egy elkülönített nagyméretű HANA-példány bérlő számára.

Futó SAP HANA nagyméretű HANA-példány és az Azure-ban üzembe helyezett virtuális gépeken futó SAP HANA között jelentős különbség van:

- Nincs nem virtualizálási réteg az SAP Hana az Azure-ban (nagyméretű példányok). Az alapul szolgáló operációs rendszer nélküli hardver teljesítményét kap.
- Egy adott ügyfél eltérően az Azure-ban, az SAP HANA az Azure-ban (nagyméretű példányok) kiszolgáló van kijelölve. Nincs, hogy egy kiszolgáló-egység vagy a gazdagép keményen vagy helyreállítható particionálva van lehetőség. Ennek eredményeképpen egy nagyméretű HANA-példány egység használatos teljes bérlőre, és az adott Önhöz rendelve. Egy újraindítás vagy leállítás, a kiszolgáló nem vezethet automatikusan az operációs rendszer és az SAP HANA üzembe helyezéséhez egy másik kiszolgálón. (Írja be a I osztályból SKU-k, az egyetlen kivétel, ha egy kiszolgáló problémákat észlel, és újbóli üzembe helyezés kell hajtható végre egy másik kiszolgálón.)
- Azure-ban, ahol processzor állomástípusok ki van jelölve a legjobb ár-teljesítmény arányt, ellentétben a processzor választott SAP Hana az Azure-ban (nagyméretű példányok) a következők az Intel E7v3 és E7v4 processzor sor legmagasabb végrehajtása.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Több SAP HANA-példányok futtatunk egy nagyméretű HANA-példány egység
Egynél több aktív SAP HANA-példány a nagyméretű HANA-példány egységek üzemeltetésére lehetőség. Ahhoz, hogy a pillanatképek tárolási és a vész-helyreállítási képességeit, az ilyen konfiguráció szükséges példányonként kötet. Jelenleg nagyméretű HANA-példány egységek részekre módon:

- **S72, S72m, S144, S192**:, 256 GB, a legkisebb kezdési egység 256 GB-os léptékben. Az egység a memória maximális kombinálhatók a különböző nagyobb, mint 256 GB és 512 GB.
- **S144m és S192m**:, 256 GB, a legkisebb egység 512 GB-os léptékben. Az egység a memória maximális kombinálhatók a különböző, például az 512 GB és 768 GB-os lépésekben.
- **Írja be a II osztály**: 2 TB-os a legkisebb kiindulási egységgel 512 GB-os lépésekben. Az egység a memória maximális kombinálhatók a különböző, például az 512 GB, 1 TB-os és 1,5 TB-os lépésekben.

Néhány példa a több SAP HANA-példány fut a következőhöz hasonlóan nézhet ki.

| SKU | Memória mérete | Tároló mérete | Több adatbázis mérete |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB-os HANA-példány<br /> vagy a példány 1 x 512 GB + 1 x 256 GB-példány<br /> vagy 3 x 256 GB-példányok | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-példányok<br />vagy a példány 1 x 512 GB + 1 x 1 TB-példány<br />vagy 6 x 256 GB-példányok<br />vagy TB-os 1x1.5 példány | 
| S192m | 4 TB | 16 TB | 8 x 512 GB példányok<br />vagy 4 x 1 TB-os<br />vagy a példányok 4 x 512 GB + 2 darab 1 TB-os<br />vagy a példányok 4 x 768 GB + 2 x 512 GB példányok<br />vagy 1 darab 4 TB-példány |
| S384xm | 8 TB | 22 TB | 4 x 2 TB-os<br />2 darab 4 TB-os vagy<br />vagy 2 x 3 TB-os + 1 x 2 TB-os<br />vagy a példányok 2x2.5 TB + 1 x 3 TB-os<br />vagy 1 x 8 TB példány |


Más változatok is vannak. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA rétegezést és bővítmény csomópontok használata
SAP az SAP BW a különböző SAP NetWeaver-kiadások és az SAP BW/4HANA rétegezési adatmodellt támogatja. Az adatmodell rétegezési kapcsolatos további információkért lásd: az SAP-dokumentum [SAP BW/4hana-t és az SAP BW on HANA, SAP HANA-bővítmény csomóponttal rendelkező](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Nagyméretű HANA-példány, az SAP HANA-bővítmény csomópontok konfigurációja – 1. lehetőség – gyakori kérdések és az SAP blog dokumentumok leírtak is használhatja. 2. lehetőség konfigurációk beállíthatja az a következő HANA nagyméretű példány termékváltozatok: S72m, S192, S192m, S384 és S384m. 

Ha a dokumentáció, azt az előnyt esetleg nem láthatók azonnal. De ha az SAP méretezési irányelveket tekinti meg, megjelenik egy nagy előnnyel jár beállítás-1. és 2. lehetőség SAP HANA-bővítmény csomópontok használatával. Az alábbiakban példákat:

- SAP HANA méretezési irányelvek összeg adatmennyiség általában szükséges memória. A gyakran használt adatokkal futtatja az SAP HANA-példány, ha csak 50 %-os rendelkezik, vagy kevesebb, a memória adatokkal feltöltött. A fennmaradó memória ideális tárolt az SAP Hana végez a műveletet.
- A 2 TB memóriával, az SAP BW-adatbázist futtató HANA nagyméretű példány S192 egységben jelenti csak rendelkezik 1 TB-os adatok kötetként.
- Egy másik SAP HANA bővítmény csomópontjára: 1. lehetőség használatakor is egy S192 HANA nagyméretű példányok Termékváltozatban, biztosít egy további 2 TB kapacitást adatmennyiség. A 2. lehetőség a konfigurációban egy további 4 TB-os meleg adatmennyiség kap. A gyakori elérésű csomópont képest, a teljes memória-kapacitás, a "meleg" kiterjesztése csomópont adatok tárolására, az 1. lehetőség használható. A 2. lehetőség az SAP HANA bővítmény csomópont-konfiguráció adatmennyiség kétszeres memória is használható.
- Végül 3 TB kapacitású az adataihoz, és ritkáról gyakori elérésű-az-meleg 1:1. lehetőség 2-es. 5 TB-nyi adat és a 2. lehetőség bővítmény csomópont-konfiguráció az 1:4 aránya rendelkezik.

Minél nagyobb adatmennyiség képest a memória, annál nagyobb az esélye, hogy a meleg adatokat kér, a lemezes tárolás tárolja.


## <a name="operations-model-and-responsibilities"></a>Működési modellt és felelősségek

A szolgáltatás az SAP HANA az Azure-ban (nagyméretű példányok) a megadott Azure IaaS-szolgáltatások igazodik. Kap egy nagyméretű HANA-példány, az SAP HANA-hoz optimalizált telepített operációs rendszer egy példánya. Mint az Azure IaaS virtuális gépeket, műveletek, az operációs rendszer korlátozására, valamilyen más szoftvert telepítenie, HANA telepítése, az operációs rendszer és a HANA operációs és frissítése az operációs rendszer és a HANA feladata a. A Microsoft nem érvényben lévő, az operációs rendszer és a HANA-frissítéseket.

![Az SAP HANA az Azure-ban (nagyméretű példányok) feladatai](./media/hana-overview-architecture/image2-responsibilities.png)

Ahogy az ábrán látható, az SAP HANA az Azure-ban (nagyméretű példányok) egy több-bérlős IaaS ajánlat. Többségében az osztálynak a feladata az operációsrendszer-infrastruktúra határ van. A Microsoft felelős a sor az operációs rendszer alatt a szolgáltatás minden aspektusát. Ön felelős a sor fölé a szolgáltatás minden aspektusát. Az operációs rendszer az Ön felelőssége. Továbbra is lehet alkalmazni a legújabb helyszíni módszerek megfelelőségi, biztonsági, felügyelet, alapja és az operációs rendszer felügyeleti használandó. A rendszerek jelennek meg, mintha azok a hálózaton lévő összes Üdvözlettel.

Ez a szolgáltatás így területek, ahol kell dolgozni a mögöttes infrastruktúra-funkciók használata a legjobb eredmények elérése érdekében a Microsoft az SAP Hana-hoz optimalizáltuk.

Az alábbi lista részletesen az egyes réteget és az Ön feladatkörei biztosítja:

**Hálózatkezelés**: a belső hálózatok számára a futó SAP HANA nagyméretű szolgáltatáspéldányban. A felhasználó felelőssége hozzáférést biztosít a tárolás, a példányhoz (például a horizontális felskálázást és egyéb funkciók), a kapcsolat a fekvő és a kapcsolatokat az Azure-bA az SAP alkalmazásrétegre üzemeltető közötti kapcsolatot a virtuális gépek. Is tartalmaz a WAN-kapcsolatra vész helyreállítási célokra replikáció az Azure adatközpontok között. Minden hálózatból elérhető a bérlői szerint vannak particionálva, és rendelkezik a alkalmazni szolgáltatás-minőségi.

**Tárolási**: A virtualizált particionálva az SAP HANA-kiszolgáló számára szükséges összes kötetet, valamint a pillanatképek tárolási. 

**Kiszolgálók**: A dedikált fizikai kiszolgálók futtatásához az SAP HANA-adatbázisok hozzárendelt a bérlők számára. A kiszolgálók típusú I osztályhoz termékváltozatok a hardver emeli ki. Az ilyen kiszolgálók a kiszolgáló konfigurációs van gyűjtött és karbantartása a profilok, amelyek egy másik fizikai hardveren egyetlen fizikai hardverről helyezheti át. Az ilyen (manuális) áttért a profil által végrehajtott műveletek az Azure-szolgáltatás javítása egy kicsit hasonlítható. A kiszolgálók, II. típusú osztály SKU-ja nem érhető el az ilyen képességet.

**SDDC**: A felügyeleti szoftver, amellyel kezelheti a data centers szoftveralapú entitásokként. A Microsoft lehetővé teszi az adatbáziskészlet erőforrásainak méretezés, a rendelkezésre állás és a teljesítmény javítása érdekében.

**Operációs**: (SUSE Linux vagy Red Hat Linux) válassza ki az operációs rendszer futtató kiszolgálókon. A rendszer a megadott operációsrendszer-lemezképek megadva az egyes Linux-gyártó által a Microsoftnak az SAP HANA futtatásához. A Linux-gyártó az SAP HANA-ra optimalizált adott rendszerkép rendelkező előfizetés kell rendelkeznie. Ön felelős a képek rögzítése az operációs rendszer szállítójához. 

Készenléti feladat átadása a Microsoft által a pontról Ön felelős minden olyan további javítása, a Linux operációs rendszert. További csomagok, amelyek egy SAP HANA sikeres telepítéséhez szükséges lehet, és, amely nem tartalmazza az SAP HANA-ban az adott Linux-gyártó által a javításokat tartalmaz operációsrendszer-lemezképek optimalizált. (További információkért lásd az SAP HANA-telepítési dokumentációt és SAP-megjegyzések) 

Ön felelős operációs rendszer javításait hibásan működik, vagy az operációs rendszer optimalizálás és az illesztőprogramok az adott kiszolgálói hardver viszonyítva. Is Ön felelős biztonsági és funkcionális javítás az operációs rendszer. 

Ön felelőssége a figyelés és a kapacitástervezés is tartalmazza:

- Erőforrás-használat Processzor.
- Memóriahasználatát.
- Kötetek szabad terület, az IOPS és a késleltetés kapcsolódik.
- A hálózati forgalomnak nagyméretű HANA-példány és az SAP alkalmazásrétegre között.

Az alapul szolgáló infrastruktúrát, a nagyméretű HANA-példányt a biztonsági mentés és az operációsrendszer-kötet visszaállítása funkciót biztosít. Ez a funkció használata is a felhasználó felelőssége.

**Közbenső**: az SAP HANA-példányt, elsősorban. Felügyeleti, műveleti és figyelési az Ön felelőssége. A megadott funkció segítségével tárolási pillanatfelvételeket kíván használni a biztonsági mentési és visszaállítási és vész-helyreállítási céllal. Ezek a képességek az infrastruktúra által biztosított. Az Ön feladatkörei is magas rendelkezésre állású és vész-helyreállítási ezekkel a lehetőségekkel kihasználva őket, és a figyelés meghatározni, hogy storage pillanatképek sikeresen végrehajtva tervezése.

**Adatok**: az SAP HANA által kezelt adatok és az egyéb adatok, például a biztonsági mentések köteteken található fájlt vagy fájlt oszt meg. Az Ön feladatkörei közé tartozik a szabad lemezterület figyelése és kezelése a tartalom a köteteken. Is Ön felelős lemezkötetek és a pillanatképek tárolási, biztonsági mentések sikeres végrehajtásának figyelése. A Microsoft feladata a vész-helyreállítási helyek az adatok replikálása sikeres végrehajtását.

**Alkalmazások:** az SAP alkalmazás példányai vagy az SAP alkalmazások, ezeknek az alkalmazásoknak az alkalmazásrétegre. Az Ön feladatkörei közé tartozik a központi telepítési, felügyeleti, műveleti és ezen alkalmazások figyelését. Ön felelős hasznosan Processzor-erőforrás-használat, memóriahasználat, az Azure-tárhelyhasználat és hálózati sávszélességet a virtuális hálózatokon belül. Is Ön felelős kapacitás megtervezése a SAP Hana-hoz a virtuális hálózatok erőforrás-használat az Azure-ban (nagyméretű példányok).

**WAN-OK**: kapcsolatot létesíteni a helyszíni számítási feladatok Azure-környezetek. Minden, a nagyméretű HANA-példány ügyfél számára a csatlakozást az Azure ExpressRoute használata. Ez a kapcsolat nem az SAP HANA az Azure-ban (nagyméretű példányok) megoldás része. Ön felelős a beállítás a kapcsolat.

**Archív**: Előfordulhat, hogy inkább az archív adatok másolatát a saját módszerrel a storage-fiókok. Archiválás szükséges felügyeleti, megfelelőségi, költségeket és műveleteket. Ön felelős létrehozni archívum másolatot és biztonsági másolatokat az Azure és a egy megfelelő módon tárolja őket.

Tekintse meg a [SLA-t, az SAP Hana az Azure-ban (nagyméretű példányok)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Méretezés

Nagyméretű HANA-példányt a méretezési semmiben nem különbözik Hana általános méretezése. A meglévő és üzembe helyezte a rendszereket, amelyeket meg szeretne áthelyezése más relációsadatbázis-kezelő rendszer HANA, SAP Itt számos olyan jelentést, amelyek a meglévő SAP-rendszerek futnak. Ha az adatbázis Hana-hoz, ezek a jelentések ellenőrizze az adatokat, és a HANA-példány memóriakövetelményei kiszámítása. Ezek a jelentések futtatása, és szerezze be a legújabb javítások vagy verziók további információkért olvassa el az alábbi SAP-megjegyzések:

- [SAP-Jegyzetnek #1793345 – SAP Suite on HANA méretezése](https://launchpad.support.sap.com/#/notes/1793345)
- [Megjegyzés: #1872170 - Suite on HANA, és S/4 HANA méretezési jelentés SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-Jegyzetnek #2121330 – gyakori kérdések: SAP BW on HANA jelentés méretezése](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP BW on HANA méretezési jelentés #1736976 - Megjegyzés](https://launchpad.support.sap.com/#/notes/1736976)
- [Megjegyzés: #2296290 – új méretezési a jelentés az SAP BW on HANA SAP](https://launchpad.support.sap.com/#/notes/2296290)

Zöldmezős megvalósításokhoz, a gyors Szimbólumméretező SAP HANA épülő SAP-szoftverek végrehajtásának memóriaigényének kiszámításához érhető el.

HANA memóriakövetelményei növelje az adatmennyiség növekedésével. Vegye figyelembe az aktuális memóriafogyasztás segít előre jelezni, mi legyen a jövőben. A memória-követelmények alapján, majd leképezheti az igény szerinti a HANA nagyméretű példány termékváltozatok egyikébe.

## <a name="requirements"></a>Követelmények

Ebben a listában több, a SAP HANA futtatásához az Azure-ban (nagyobb példányok).

**Microsoft Azure**

- Azure-előfizetést is társítható, az Azure-ban (nagyméretű példányok) SAP Hana-hoz.
- A Microsoft Premier szintű támogatási szerződés. Azure-ban futó SAP kapcsolatos információkért lásd: [SAP támogatási Megjegyzés #2015553 – Microsoft Azure-on SAP: támogatás előfeltételei](https://launchpad.support.sap.com/#/notes/2015553). Ha nagyméretű HANA-példány egységek 384-et és a további processzorokat használja, szükség is kiterjesztheti a Premier szintű támogatási szerződés Azure Rapid Response tartalmazza.
- A HANA nagyméretű példány SKU-k az SAP egy méretezési gyakorlat végrehajtása után kell hívja.

**Hálózati kapcsolat**

- Az Azure-bA helyszíni között ExpressRoute: a helyi adatközpontban az Azure-hoz csatlakozhat, ügyeljen arra, hogy az internetszolgáltató által biztosított legalább 1-GB/s kapcsolat order. 

**Operációs rendszer**

- SUSE Linux Enterprise Server 12 SAP-alkalmazások a licencet.

   > [!NOTE] 
   > Az operációs rendszer, amelyeket a Microsoft SUSE nincs regisztrálva. Egy előfizetés felügyeleti eszköz példány nincs csatlakoztatva.

- SUSE Linux előfizetés felügyeleti eszköz üzembe helyezett Azure-beli virtuális gépen. Ez az eszköz lehetővé teszi a SAP Hana az Azure-ban regisztrált és megfelelő frissítette a SUSE (nagyméretű példányok). (Nincs nincs internet-hozzáférés a nagyméretű HANA-példány adatközponton belül nem.) 
- Red Hat Enterprise Linux 6.7 vagy az SAP Hana 7.x licencet.

   > [!NOTE]
   > Az operációs rendszer, amelyeket a Microsoft a Red Hattel nincs regisztrálva. A Red Hat előfizetés-kezelő példány nincs csatlakoztatva.

- Red Hat előfizetés-kezelő üzembe helyezett Azure-beli virtuális gépen. A Red Hat előfizetés-kezelő lehetővé teszi a SAP Hana az Azure-ban regisztrált és megfelelő frissítette a Red Hat (nagyméretű példányok). (Nincs nincs közvetlen internet-hozzáférést a telepített Azure nagyméretű szolgáltatáspéldányban a bérlőn belül nem.)
- SAP használatához szükséges támogatási szerződés, valamint a Linux-szolgáltatónál. Ez a követelmény a nagyméretű HANA-példányt, vagy az a tény, hogy a Linux az Azure-ban fut, a megoldás által nem törlődnek. Ellentétben az egyes Linux rendszerű Azure-katalógus képek a szolgáltatási díj nem *nem* nagyméretű HANA-példányt, a megoldás ajánlatban foglalt. A feladata követelményeit SAP támogatási szerződéssel kapcsolatos a Linux terjesztő teljesítéséhez. 
   - SUSE Linux esetén keresse meg a támogatási szerződés előírásainak [SAP Megjegyzés #1984787 – SUSE Linux Enterprise Server 12: telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787) és [SAP Megjegyzés #1056161 - SUSE elsőbbségi támogatást az SAP-alkalmazások](https://launchpad.support.sap.com/#/notes/1056161).
   - Red Hat Linux rendszeren kell a megfelelő előfizetés szintű támogatást tartalmaznak, és operációs rendszerét, nagyméretű HANA-példányt, a szolgáltatást. Red Hat [SAP-megoldások] ajánlja a Red Hat Enterprise Linux (https://access.redhat.com/solutions/3082481 előfizetés. 

A különböző Linux-verziók a különböző SAP HANA-verziók támogatási mátrixa, lásd: [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Tekintse meg a kompatibilitási mátrix az operációs rendszer és a belső vezérlőprogram és illesztőprogram-verziókat HLI [HLI az operációs rendszer frissítése](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> II. típusú egységek csak a SLES 12 SP2 operációsrendszer-verzió támogatott ezen a ponton. 


**Adatbázis**

- Licencek és az SAP Hana (platform vagy enterprise edition) telepítési szoftverösszetevőket.

**Alkalmazások**

- Licencek és olyan SAP-alkalmazások, amelyhez csatlakozni az SAP HANA és a kapcsolódó SAP telepítési szoftverösszetevőket támogatja a szerződéseket.
- A licencek és az SAP alkalmazások telepítése szoftverösszetevőket használja az SAP HANA (nagyméretű példányok) az Azure-környezetekben, és támogatási szerződéssel kapcsolatos.

**Képességek**

- A tapasztalatokat és ismereteket Azure IaaS és összetevői.
- A tapasztalatokat és ismereteket, amelyekkel telepítheti az SAP számítási feladatok Azure-ban.
- SAP HANA telepítése megkapta a csoporthoz.
- SAP mérnök képességek körül SAP HANA magas rendelkezésre állás és vészhelyreállítás helyreállítási tervezéséhez.

**SAP**

- Az elvárás, hogy már az SAP-ügyfelek és a támogató SAP-szerződés.
- A HANA nagyméretű példány termékváltozatok II. típusú osztályát megvalósítását, különösen a tekintse meg az SAP az SAP HANA és a végleges konfigurációk nagy méretű vertikális felskálázás hardveren verzióit.


## <a name="storage"></a>Storage

A tárolási elrendezés az SAP Hana az Azure-ban (nagyméretű példányok) a klasszikus üzemi modell keresztül irányelvek ajánlott SAP az SAP Hana van konfigurálva. Az irányelvek vannak dokumentálva az [SAP HANA tárolási követelményei](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) tanulmányt.

A nagyméretű HANA-példány I osztály típusú storage-kötetként négyszer a memória kötet tartalmaz. A nagyméretű HANA-példány egységek II. típusú az osztályhoz a tároló nem négyszer további. Az egységek kapható olyan kötetre, amely a HANA tranzakciós napló biztonsági mentések tárolására szolgál. További információkért lásd: [telepítse és konfigurálja az SAP HANA (nagyméretű példányok) az Azure-ban](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tekintse meg az alábbi táblázat a Foglalás tekintetében. A táblázat a különböző kötetek, a másik egységgel nagyméretű HANA-példány a megadott nyers kapacitása.

| Nagyméretű HANA-példány Termékváltozat | Hana/adatok | Hana/log | Hana/shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |MAXIMUM 1280 GB | 768 GB |
| S192 | 4,608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S192xm |  11,520 GB |  1 536 GB |  1,792 GB |  1 536 GB |
| S384 | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xxm |  20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576m | 20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576xm | 31,744 GB | 4096 GB | 2048 GB | 4096 GB |
| S768m | 28 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4096 GB | 6 144 GB |
| S960m | 36,000 GB | 4,100 GB | 2050 GB | 4,100 GB |


Tényleges üzembe helyezett kötetek üzembe helyezés és az eszköz, amellyel megjelenítése a kötet mérete alapján változhat.

Ha Ön feloszthatja a HANA nagyméretű példányok Termékváltozatban, néhány példa a lehetséges osztás darab következőhöz hasonló lehet:

| Memória partíció GB-ban | Hana/adatok | Hana/log | Hana/shared | Hana/log/biztonsági mentés |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | MAXIMUM 1280 GB | 768 GB |


Ezeket a méreteket elsősorban a nyers mennyiségi számát, amelyek a központi telepítés és a kötetek megjelenítéséhez használt eszközök függően némileg eltérőek lehetnek. Még nincsenek más partícióméretei, például 2,5 TB. Ezek a storage-méretek kiszámítása egy képlettel hasonló az előző partíciók. A "partíció" kifejezés nem jelenti azt, hogy az operációs rendszer, a memória vagy a Processzor-erőforrásokat bármilyen módon particionáltak. Azt jelzi, hogy a különböző HANA-példányokhoz, érdemes egy egyetlen nagyméretű HANA-példány egységen üzembe helyezése adattárolási partíciókat. 

Szükség lehet további tárhelyet. Vásároljon további tárterületet 1 TB-os egységekben storage is hozzáadhat. A további tárhely is hozzáadhatók további kötetként. Azt is segítségével kiterjesztheti a meglévő kötetek közül legalább egyet. Nem lehet a kötetek méretének csökkentése eredetileg üzembe helyezve, és többnyire felterjesztése révén az előző táblázatokban. Emellett nem lehet módosítani a kötetek nevei, vagy csatlakoztassa a neveket. A korábban leírt köteteinek a nagyméretű HANA-példány egységek NFS4 kötetek vannak csatolva.

Használhatja a pillanatképek tárolási, biztonsági mentési és visszaállítási és vész-helyreállítási céllal. További információkért lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) a forgatókönyvnek a tárolási elrendezés részletekért.

### <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
Nagyméretű HANA-példány felhasznált tárterület lehetővé teszi, hogy egy adatok átlátható titkosítását, a lemezeken tárolja. Egy nagyméretű HANA-példány egység telepítésekor a engedélyezheti a titkosítást az ilyen típusú. Emellett titkosított kötetek után módosíthatja a központi telepítés történik. Az áthelyezés nem titkosított a titkosított kötetekre transzparens módon történik, és nem igényli az állásidő. 

Típusú I osztályhoz termékváltozatok, a logikai egység tárolt rendszerindító kötet titkosítva van. Az SKU-k a nagyméretű HANA-példányt, II. típusú osztályhoz a rendszerindító LUN-t az operációs rendszer módszerekkel titkosítania kell. További információért forduljon a Microsoft-kezelési csapatunk.


## <a name="networking"></a>Hálózat

Az architektúra az Azure hálózati szolgáltatások egyik legfontosabb összetevőjeként a sikeres üzembe helyezéséhez SAP-alkalmazások nagyméretű HANA-példány. Általában az SAP HANA Azure-ban (nagyméretű példányok) üzemelő példányok esetében egy nagyobb SAP-rendszeren, a számos különböző SAP-megoldások a különböző méretű készletben az adatbázisok, a Processzor-erőforrás-használat és a memóriahasználat rendelkezik. Valószínű, hogy nem minden ezeket SAP-rendszereinket SAP HANA alapulnak. Az SAP-rendszeren, akkor valószínűleg használó hibrid:

- Üzembe helyezett SAP rendszerek helyszíni. A méretek miatt ezek a rendszerek jelenleg nem futhat az Azure-ban. Ilyen például, egy éles SAP ERP-rendszer, amely (mint az adatbázis) SQL-kiszolgálón fut, és több CPU és memória erőforrást, mint a virtuális gépeket biztosíthatnak igényel.
- Az SAP az SAP HANA-alapú rendszerek a helyszíni rendszerbe.
- Üzembe helyezett SAP-rendszerek virtuális gépeket. Ezek a rendszerek fejlesztés, tesztelés, védőfal, vagy bármely, az SAP NetWeaver-alapú alkalmazások sikeres központi telepítése az Azure-ban (a VM-EK), az erőforrás-használat és a memória igény szerinti példányok éles környezetben. Ezek a rendszerek is alapulhat SQL Server-adatbázisok. További információkért lásd: [SAP támogatási Megjegyzés #1928533 – SAP alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533/E). És az SAP HANA-adatbázisok alapjául ezekben a rendszerekben is használható. További információkért lásd: [SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Üzembe helyezett SAP-alkalmazáskiszolgálókhoz az Azure-ban (a VM-EK), az SAP HANA az Azure-ban (nagyméretű példányok) az Azure-beli nagyméretű példány stampek.

Hibrid SAP-megoldás a négy vagy több különböző központi telepítési forgatókönyv jellemző. Nincsenek is befejeződött az Azure-ban futó SAP-környezetünk számos ügyfél eseteit. Virtuális gépek egyre nagyobb teljesítményű, helyezze át saját SAP-megoldások Azure-beli ügyfelek száma növekszik.

Azure-hálózat az Azure-ban üzembe helyezett SAP-rendszereinket kontextusában nem bonyolult. A következő alapelveket alapul:

- Azure virtuális hálózatok kapcsolódnia kell az ExpressRoute-kapcsolatcsoport, amely egy helyszíni hálózathoz csatlakozik.
- ExpressRoute-kapcsolatcsoport, amely kapcsolódik a helyszíni Azure általában 1 GB/s vagy nagyobb sávszélességet kell rendelkeznie. A minimális sávszélesség elegendő sávszélesség a helyszíni és a virtuális gépeken futó rendszerek közötti adatátvitelt tesz lehetővé. Lehetővé teszi elegendő sávszélesség-kapcsolat Azure rendszerek, a helyszíni felhasználók.
- Minden SAP-rendszereit az Azure-ban kell állítani a virtuális hálózatok egymással kommunikálni.
- Az Active Directory és DNS-a helyileg üzemeltetett helyszíni kiterjeszthetők az Azure expressroute-on keresztül.


> [!NOTE] 
> Elszámolási szempontból csak egy Azure-előfizetéssel is össze a egy nagyméretű szolgáltatáspéldányban egy adott Azure-régióban kizárólag egy bérlővel. Ezzel szemben egyetlen nagyméretű példány stamp bérlő kapcsolhatók csak egy Azure-előfizetéshez. Ez a követelmény nem konzisztensek legyenek más számlázható objektumok az Azure-ban.

SAP HANA az Azure-ban (nagyméretű példányok) több különböző Azure-régióban van üzembe helyezve, ha külön bérlőt a nagyméretű szolgáltatáspéldányban üzemel. Mindaddig, amíg ezek a példányok ugyanazt az SAP-rendszeren részét képező alatt az Azure-előfizetéshez is futtathatja. 

> [!IMPORTANT] 
> Csak az Azure Resource Manager üzembe helyezése az SAP HANA az Azure-ban (nagyméretű példányok) is támogatja.

 

### <a name="additional-virtual-network-information"></a>További virtuális hálózati adatai

Virtuális hálózat az expressroute-hoz csatlakozhat, létre kell hozni egy Azure-átjáró. További információkért lásd: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Az infrastruktúra Azure-on kívül, vagy egy Azure nagyméretű szolgáltatáspéldányban egy Azure-átjáró használható az expressroute-tal. Emellett az Azure-átjáró használható virtuális hálózatok közötti kapcsolat. További információkért lásd: [Resource Manager-hálózatok közötti kapcsolat konfigurálása PowerShell-lel](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az Azure-átjáró négy különböző az ExpressRoute-kapcsolatok maximális csatlakoztathatja mindaddig, amíg a különböző Microsoft vállalati peremhálózati útválasztói biztosítja ezeket a kapcsolatokat. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Az átviteli sebességet biztosít az Azure-átjáró eltér mindkét használati eseteket. További információkért lásd: [információk a VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A maximális átviteli sebességet érhet el a virtuális hálózati átjáró egy ExpressRoute-kapcsolat használatával 10 GB/s. A virtuális hálózatban található virtuális gép és a egy rendszer közötti fájlok másolása a helyszíni (adatfolyamként egyetlen példányt) nem érhet el a teljes átviteli képessége a különböző átjáró SKU-k. Kihasználhatja a virtuális hálózati átjáró a teljes sávszélesség, több Stream használja. Vagy különböző fájlokat egyetlen fájl párhuzamos Streamek kell másolnia.


### <a name="networking-architecture-for-hana-large-instance"></a>Nagyméretű HANA-példány a hálózati architektúra
A hálózati architektúra a nagyméretű HANA-példány négy részből tartalmaznak:

- Helyszíni hálózat és az Azure ExpressRoute-kapcsolat. Ez a rész az ügyfél a tartományhoz, és az Azure expressroute-on keresztül csatlakozik. Tekintse meg az alábbi ábrán a bal alsó részen.
- Az Azure hálózati szolgáltatások, előbb tárgyalt módon, újra kell az átjáró virtuális hálózatok használatával. Ez a rész egy adott területre, ahol meg kell keresnie a megfelelő tervek esetében az alkalmazás követelményei, biztonsági és megfelelőségi követelményeknek. Nagyméretű HANA-példány használatát egy másik pontot kell figyelembe venni a virtuális hálózatok és az Azure-átjáró SKU-k számát tekintetében közül választhat. Tekintse meg a jobb felső sarokban az ábrán látható.
- A nagyméretű HANA-példány keresztüli kapcsolat ExpressRoute technológia az Azure-bA. Ez a kijelző telepítve és a Microsoft által kezelt. Teendők csak adja meg az egyes IP-címtartományok nagyméretű HANA-példányt az eszközök telepítése után az ExpressRoute-kapcsolatcsoport csatlakozhat a virtuális hálózatok. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Hálózatkezelés a nagyméretű HANA-példányt, amely nagyrészt átlátható az Ön számára.

![Az Azure-ban (nagyméretű példányok) és a helyszíni SAP Hana-hoz csatlakoztatott virtuális hálózat](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

A követelmény, hogy a helyszíni eszközök csatlakoznia kell az Azure expressroute-on keresztül nem módosítható, mert a nagyméretű HANA-példányt használ. Egy vagy több virtuális hálózatot, amely a virtuális gépek, amelyek az alkalmazásrétegre, amely kapcsolódik a HANA-példányok nagyméretű HANA-példány egységekben futtatott, hogy még nem változik. 

Az Azure-beli SAP-környezetekhez különbségek a következők:

- A nagyméretű HANA-példány egységeket az ügyfél-bérlő másik ExpressRoute-kapcsolatcsoporton keresztül csatlakoznak a virtuális hálózatokra. Betöltés feltételek külön, a helyszíni virtuális hálózatokat az ExpressRoute-kapcsolatok és virtuális hálózatok és a nagyméretű HANA-példány közötti kapcsolatot nem adjuk ki ugyanazt az útválasztók.
- A számítási feladat profilja az SAP alkalmazásrétegre és nagyméretű HANA-példány között különböző jellegű, és sok kisebb kérelem, és a tevékenységcsúcsok hasonlóan az alkalmazásrétegre, az adatforgalom az SAP HANA-ból (eredményhalmazt).
- A SAP alkalmazás architektúra olyan bizalmas adatokat kezelő a hálózati késés, mint a jellemző forgatókönyvek, ahol adatcsere a helyszíni és az Azure között.
- A virtuális hálózati átjárót már legalább két ExpressRoute-kapcsolatok. Mindkét kapcsolat osztozik a virtuális hálózati átjáró a bejövő adatok maximális sávszélessége.

A hálózati késések tapasztalhatók virtuális gépek és a nagyméretű HANA-példány között egységre is lehet magasabb, mint a szokásos virtuális gép virtuális gép hálózati körbejárási késés. Az Azure-régió függ mért értékek lépheti túl a besorolását, az átlagosnál rosszabb a 0,7-ms körbejárási késés [SAP Megjegyzés #1100926 – gyakori kérdések: hálózati teljesítményt](https://launchpad.support.sap.com/#/notes/1100926/E). Mindazonáltal az ügyfelek üzembe sikeresen SAP HANA nagyméretű példányok az SAP HANA-alapú üzemi SAP-alkalmazások. Az ügyfelek, akik jelentések nagyszerű fejlesztései telepítették az SAP-alkalmazások futtatásával SAP HANA nagyméretű HANA-példány egységek használatával. Ellenőrizze, hogy alaposan tesztelni az üzleti folyamatok az Azure nagyméretű HANA-példányt.
 
Ahhoz, hogy a virtuális gépek és a nagyméretű HANA-példány a determinisztikus hálózati késés, a virtuális hálózati átjáró Termékváltozata, amely alapvető fontosságú. Ellentétben a helyszíni és a virtuális gépek közötti forgalmat a virtuális gépek és a nagyméretű HANA-példány közötti forgalom minta kis, de nagy adatlöketekkel továbbítani a kéréseket és az adatokat kötetek is fejleszthet. Az ilyen adatlöketekkel jól kezelése érdekében az UltraPerformance átjáró-Termékváltozatot használata erősen ajánlott. A HANA nagyméretű példány termékváltozatok II. típusú osztályát az UltraPerformance átjáró-Termékváltozat használatát a virtuális hálózati átjáró megadása kötelező.

> [!IMPORTANT] 
> Adja meg a teljes hálózati forgalom az SAP-alkalmazások és adatbázis rétegek között, csak a nagy teljesítményű vagy a virtuális hálózatok az UltraPerformance átjáró-termékváltozatok támogatottak az Azure-ban (nagyméretű példányok) SAP Hana-val. Csak az UltraPerformance átjáró-Termékváltozatot HANA nagyméretű példány II. típusú termékváltozatokhoz, mint a virtuális hálózati átjáró támogatott.



### <a name="single-sap-system"></a>Egyetlen SAP-rendszerhez

A korábban látható a helyszíni infrastruktúra expressroute-on keresztül csatlakozik az Azure-bA. Az ExpressRoute-kapcsolatcsoporthoz az olyan vállalati peremhálózati útválasztó kapcsolódik. További információkért lásd: [ExpressRoute technikai áttekintése](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az útvonal létrejötte után, az Azure gerinchálózatán keresztül kapcsolódik, és az összes Azure-régiókban érhetők el.

> [!NOTE] 
> Futtassa SAP-környezetünk az Azure-ban, csatlakozzon az Azure-régióba az SAP-rendszeren, a legközelebbi vállalati peremhálózati útválasztó. Az Azure nagy példány stampek dedikált vállalati peremhálózati útválasztó eszközök az Azure IaaS és a nagyméretű példány stampek virtuális gépek közötti hálózati késés minimalizálása érdekében keresztül csatlakoznak.

Az SAP alkalmazáspéldányok üzemeltető virtuális gépek a virtuális hálózati átjárót az ExpressRoute-kapcsolatcsoporthoz csatlakozik. Ugyanahhoz a virtuális hálózathoz csatlakoztatva van egy külön vállalati peremhálózati útválasztó dedikált nagyméretű példány stampek való kapcsolódás.

A rendszer egyetlen SAP-rendszer egyszerű példát. Az SAP alkalmazásrétegre az Azure-ban üzemel. Az SAP HANA-adatbázis futtatja az SAP HANA az Azure-ban (nagyméretű példányok). Feltételezzük, hogy a virtuális hálózati átjáró sávszélesség 2-GB/s és 10-GB/s átviteli nem jelentenek szűk keresztmetszetté.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP-rendszereit, vagy a nagyméretű SAP-rendszerek

Ha több SAP-rendszereit, vagy a nagyméretű SAP-rendszereit az Azure-ban (nagyméretű példányok) SAP Hana-hoz kapcsolódáshoz van telepítve, az átviteli sebességet a virtuális hálózati átjáró szűk keresztmetszetté válhat. Ebben az esetben az alkalmazás rétegek felosztása több virtuális hálózat. Akkor is előfordulhat, hogy speciális virtuális hálózat létrehozása, amely csatlakozik a nagyméretű HANA-példány azokra az esetekre, mint például:

- Közvetlenül a HANA-példányok a nagyméretű HANA-példányt a biztonsági mentések végrehajtásához az NFS-megosztásokat üzemeltető Azure-beli virtuális géphez.
- Bemásolja a nagyméretű biztonsági másolatok és egyéb fájlok nagyméretű HANA-példány egység az Azure-ban felügyelt hely a lemezen.

Használjon egy külön virtuális hálózatot a gazdagép, amely kezeli a tárhelyet a virtuális gépek. Ezzel az elrendezéssel fokozott elkerülhetők a nagy méretű fájlok vagy az adatforgalmi HANA nagyméretű példányok az Azure-bA a virtuális hálózati átjárót, az SAP alkalmazásrétegre futtató virtuális gépek ellátó hatásait. 

Jobban skálázható hálózati architektúra:

- Használja ki a több virtuális hálózat egyetlen, nagyobb SAP alkalmazás réteg.
- Üzembe helyezése egy külön virtuális hálózatot az egyes üzembe helyezett, SAP-rendszerhez, ezeket külön alhálózatra alatt az azonos virtuális hálózatban SAP-rendszereinket kombinálásával képest.

 Egy jobban skálázható hálózati architektúra az SAP Hana az Azure-ban (nagyméretű példányok):

![SAP alkalmazásréteg üzembe több virtuális hálózaton keresztül](./media/hana-overview-architecture/image4-networking-architecture.png)

Az ábrán látható, az SAP alkalmazásréteg vagy -összetevőket, több virtuális hálózatokon üzembe helyezett. Ez a konfiguráció elkerülhetetlen késéses többletterhelést okoz, a virtuális hálózatok üzemeltetett alkalmazás közötti kommunikáció során fellépő vezetett be. Alapértelmezés szerint a különböző virtuális hálózatokban útvonalon keresztül a vállalati peremhálózati útválasztói ebben a konfigurációban található virtuális gépek közötti hálózati forgalom. A úgy optimalizálása és lefelé a késés két virtuális hálózat közötti kommunikációt a Kivágás, hogy társviszony-létesítés virtuális hálózatok ugyanazon a régión belül. Ez a módszer használható akkor is, ha a virtuális hálózatok különböző előfizetésekben találhatóak. A virtuális hálózatok közötti társviszony, két különböző virtuális hálózatokban lévő virtuális gépek közötti kommunikáció segítségével az Azure gerinchálózatába közvetlenül kommunikálnak egymással. Késés látható, ha a virtuális gépek ugyanazon a virtuális hálózaton. Az egyes virtuális hálózati átjáró a virtuális hálózat, amely az Azure virtuális hálózati átjárón keresztül csatlakozó IP-címtartományok forgalmat továbbít. 

Virtuális hálózatok közötti társviszony létesítésével kapcsolatos további információkért lásd: [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Útválasztás az Azure-ban

Három hálózati útválasztási szempontok fontosak az SAP Hana az Azure-ban (nagyméretű példányok):

* SAP HANA az Azure-ban (nagyméretű példányok) csak a virtuális gépek és a dedikált ExpressRoute-kapcsolat nem közvetlenül a helyszíni keresztül érhetők el. A nagyméretű HANA-példány egységek közvetlen hozzáférést a helyszíni, Microsoft által kiadott nem lehetséges közvetlenül. A tranzitív útválasztási korlátozások vonatkoznak az aktuális Azure-beli hálózati architektúra használt SAP HANA nagyméretű példányok miatt. Néhány felügyeleti ügyfelek és olyan alkalmazásokat, amelyek kell közvetlen hozzáférés, például SAP megoldás Manager a helyszínen futó SAP HANA-adatbázis nem lehet csatlakozni.

* Ha nagyméretű HANA-példány egységek vész-helyreállítási két különböző Azure régióban üzembe helyezve, az azonos átmeneti útválasztási érvényesek. Más szóval egy nagyméretű HANA-példány egység egy adott régióban (például USA nyugati régiója) IP-címek nem irányíthatók át egy nagyméretű HANA-példány egységet (például USA keleti régiója) egy másik régióban üzembe helyezve. Ez a korlátozás nem függ a régiók közötti társviszony-létesítés, vagy a nagyméretű HANA-példány egységek virtuális hálózatokhoz csatlakozó ExpressRoute-Kapcsolatcsoportok átívelő kapcsolódás Azure-hálózat használatát. Egy grafikus megjelenítését lásd: az ábra a szakaszban a "Használata nagyméretű HANA-példány egységek több régióban is." Ez a korlátozás, amelyben a telepített architektúrával, úgy, hogy a HANA-Rendszerreplikálást azonnali használata az vészhelyreállítási funkciók.

* SAP HANA az Azure-ban (nagyméretű példányok) egység van egy hozzárendelt IP-címet a kiszolgáló IP-készlet címtartomány elküldött. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az IP-címet az Azure-előfizetések és a Hana-hoz az Azure-ban (nagyméretű példányok) virtuális hálózatokhoz csatlakozó ExpressRoute keresztül érhető el. Az IP-cím kiosztva közül, hogy kiszolgáló IP-készlet címtartománya közvetlenül hozzá rendelt hardver egységhez. Rendelkezik *nem* keresztül kiosztott NAT válik, mivel ez a megoldás első központi levő volt. 

> [!NOTE] 
> Átmeneti útválasztás a korlátozás áthidalható, az első két listaelemek leírtak szerint, a további összetevők használata útválasztást. A korlátozás áthidalható felhasználható összetevők lehetnek:

> * Egy proxykiszolgálói adatok, irányíthatja a. Például F5 BIG-IP-ot és NGINXET a Traffic Managerrel üzembe helyezett Azure-ra, egy virtuális tűzfal/forgalom-útválasztási megoldás.
> * Használatával [IPTables szabályait saját](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) a Linux rendszerű virtuális gép az Útválasztás a helyszínek és nagyméretű HANA-példány egységek vagy különböző régiókban lévő nagyméretű HANA-példány egységek között engedélyezése.

> Vegye figyelembe, hogy a megvalósítás és a támogatás az egyéni megoldások használata esetén a külső hálózati berendezések, vagy engedélyezze az IPTables a nem Microsoft által biztosított. Támogatási biztosítania kell a használt összetevő a szállító vagy az adatintegrálónak. 

### <a name="internet-connectivity-of-hana-large-instance"></a>A nagyméretű HANA-példány internetkapcsolat
Nagyméretű HANA-példány does *nem* rendelkezik közvetlen internetkapcsolattal. Tegyük fel ez a korlátozás előfordulhat, hogy korlátozni a regisztrálni közvetlenül az operációsrendszer-lemezkép az operációs rendszer szállítójához. Szüksége lehet a SUSE Linux Enterprise Server előfizetés felügyeleti eszköz helyi kiszolgáló vagy a Red Hat Enterprise Linux előfizetés-kezelő használata.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Virtuális gépek és a nagyméretű HANA-példány között adattitkosítás
Nagyméretű HANA-példány és a virtuális gépek között továbbított adatok nincsenek titkosítva. Azonban csak az exchange között a HANA DBMS-oldalon és a JDBC/ODBC-alapú alkalmazások, az szintén titkosítást alkalmazhat a forgalom. További információkért lásd: [ebben a dokumentációban, az SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Nagyméretű HANA-példány egységek használata több régióban

Előfordulhat, hogy üzembe helyezéséhez SAP HANA az Azure-ban (nagyméretű példányok) több Azure-régióban nem vész-helyreállítási okok miatt. Érdemes lehet az egyes régiókban a különböző virtuális hálózatokon üzembe helyezett virtuális nagyméretű HANA-példány eléréséhez. A különböző nagyméretű HANA-példány egységeket rendelt IP-címek a virtuális hálózatok, a példányok az átjárón keresztül közvetlenül csatlakozó túli nem kerülnek. Ennek eredményeképpen egy kis módosítást a virtuális hálózati kialakítás jelenik meg. A virtuális hálózati átjáró négy különböző ExpressRoute-Kapcsolatcsoportok kívül más vállalati peremhálózati útválasztók kezelésére képes. Minden virtuális hálózatnak, amelyhez csatlakozik egy nagyméretű példány stampek lehet csatlakozni a nagyméretű szolgáltatáspéldányban egy másik Azure-régióban.

![Virtuális hálózat csatlakozik a különböző Azure-régiókban lévő Azure-beli nagyméretű példány bélyegzők](./media/hana-overview-architecture/image8-multiple-regions.png)

Az ábra bemutatja, hogyan a különböző virtuális hálózatok mindkét régióban kapcsolódik két másik ExpressRoute-kapcsolatcsoporttal csatlakozhat az Azure-ban (nagyméretű példányok) SAP Hana-hoz használt két Azure-régióban. Az újonnan bevezetett kapcsolatok a téglalap alakú piros vonalak. Az ezeket a kapcsolatokat a virtuális hálózatokon kívül a különböző nagyméretű HANA-példány egységek, a két régióban üzembe helyezett minden egyes virtuális hálózatok egy futó virtuális gépek lehet elérni. Ahogy az ábrán látható, feltételezzük, hogy a két Azure-régiókhoz való két ExpressRoute-kapcsolatok a helyszíni. Ezzel az elrendezéssel fokozott a vész-helyreállítási okokból ajánlott.

> [!IMPORTANT] 
> Ha több ExpressRoute-Kapcsolatcsoportok használta, a AS Path előtag-Beillesztés és a helyi beállításokat szabályozó BGP-beállítások annak érdekében, hogy a forgalom megfelelő útválasztási használandó.


