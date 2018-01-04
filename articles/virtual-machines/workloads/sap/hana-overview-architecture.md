---
title: "Áttekintés és SAP HANA Azure (nagy példányok) architektúrájának |} Microsoft Docs"
description: "Architektúra áttekintése, amelyekkel telepítheti az SAP HANA Azure (nagy példány)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09198355ecd862c73b728d8119bbf9d56e3b9f69
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA (nagy példányok) – áttekintés és az Azure-architektúra

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az Azure (nagy példányok) SAP HANA?

SAP HANA Azure (nagy példány) egy olyan egyedi megoldás az Azure-bA. Azure virtuális gépeken történő központi telepítéséhez és futtatásához az SAP HANA biztosít, Azure lehetőséget biztosít, futtatása és az SAP HANA ügyfélként Önnek dedikált operációs rendszer nélküli a kiszolgálókra telepített lehetőségét. Az SAP HANA Azure (nagy példányok) megoldás, amely hozzá van rendelve, mint az ügyfél nem megosztott gazdakiszolgáló/operációs rendszer nélküli hardver építkezik. A Kiszolgálóhardver nagyobb számítási és a kiszolgáló, a hálózat és tároló-infrastruktúra tartalmazó bélyegzők van beágyazva. Amellyel kombinációja lesz hitelesített HANA TDI. A szolgáltatás ajánlat az SAP HANA Azure (nagy példányok) kínál különböző különböző kiszolgálótermékek vagy kezdve, amely 72 processzorok és 768 GB memória, amelyek 960 processzorok egységek és 20 TB memóriával rendelkeznek egység méretét.

Az ügyfél elkülönítési az infrastruktúra stamp belül történik bérlők, amely részletesen a következőhöz hasonló:

- Hálózatkezelés: Elkülönítési ügyfelek belül infrastruktúra verem hozzárendelt ügyfél bérlőnként virtuális hálózatokon keresztül. A bérlő egyetlen ügyfél hozzá van rendelve. Egy ügyfél rendelkezhet több bérlő. A hálózati elkülönítés a bérlő nem engedélyezi a hálózati kommunikáció a bérlők infrastruktúra stamp szint között. Akkor is, ha a bérlők számára az azonos ügyfél tartozik.
- Tárolási összetevőinek: tároló virtuális gépek, amelyek rendelkeznek a hozzárendelt tárolókötetek elkülönítését. Tárolási köteteket rendelhet egy tárolási virtuális gépet. Egy tároló virtuális géphez van hozzárendelve, kizárólag a SAP HANA TDI hitelesített infrastruktúra verem egy egyetlen bérlő számára. Ennek eredményeképpen a tároló virtuális géphez rendelt tárolókötetek érhetők el egy adott és kapcsolódó bérlői csak. És nem látható a különböző telepített bérlők között.
- Kiszolgáló vagy a gazdagép: A kiszolgáló vagy a gazdagép egység nem megosztott ügyfelei vagy a bérlők között. A kiszolgáló vagy a gazdagép egy ügyfél telepítve egy atomi operációs rendszer nélküli számítási egység, amely egy egybérlős hozzá van rendelve. **Nem** hardver particionálási vagy soft-particionálást használnak, amelyek eredményezheti, hogy ügyfélként, a gazdagépen vagy egy kiszolgáló megosztása egy másik ügyféllel. Tárolás az adott bérlő tároló virtuális géphez rendelt csatlakoztatva vannak ilyen kiszolgálóra. A bérlő rendelkezhetnek kizárólag hozzárendelt különböző Termékváltozatai sok kiszolgáló egységeire.
- Egy SAP HANA (nagy példány) Azure infrastruktúra stamp, belül számos különböző bérlők telepített és egymással szembeni keresztül a bérlői fogalmak a hálózati, tárolási és számítási szint elkülönített. 


A kiszolgáló operációs rendszer nélküli egységeket SAP HANA csak futtatásához támogatottak. Az SAP alkalmazásréteg vagy munkaterhelés közel-vő réteg fut a Microsoft Azure virtuális gépeken. Az SAP HANA futó Azure (nagy példány) egységek infrastruktúra bélyegzők csatlakoznak a Azure hálózati csigolyákat, tehát, amely a kis késleltetésű kapcsolat az Azure (nagy példány) egységek SAP HANA és Azure virtuális gépek közötti valósul meg.

Ez a dokumentum több dokumentumot, amely magában foglalja az Azure (nagy példány) SAP HANA egyike. Ez a dokumentum azt keresse meg a alapvető architektúráját, feladatok, szolgáltatásait, és a magas szintű képességek a megoldás keresztül. A legtöbb területek, mint például a hálózati és a csatlakozási a más négy dokumentumok takarja el a részleteket, és elemezze időszakosan megszakadó. Az SAP HANA (nagy példány) Azure dokumentációjában nem fedi fel az SAP NetWeaver telepítési szempontok vagy SAP NetWeaver az Azure virtuális gépeken a központi telepítéséhez. SAP NetWeaver Azure ugyanabban az Azure dokumentációja a tárolóban található külön dokumentumokra vonatkozik. 


A különböző dokumentumok HANA nagy példány útmutatás terjed ki a következő területeken:

- [SAP HANA (nagy példány) – áttekintés és az Azure-architektúra](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hogyan kell telepíteni, és az SAP HANA (nagy példányok) konfigurálása az Azure-on](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) magas rendelkezésre állás és vészhelyreállítás Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) hibaelhárítás és Azure figyelése](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Állítsa be a STONITH használatával SUSE a magas rendelkezésre állás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Az operációs rendszer biztonsági mentéséhez és visszaállításához a típus II termékváltozatok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Meghatározások

Több, közös definíciók széles körben használt a architektúra és a műszaki üzembe helyezési útmutatójában. Vegye figyelembe a következő feltételek és azok jelentését:

- **IaaS:** szolgáltatott infrastruktúra
- **A PaaS:** platformok
- **SaaS:** szolgáltatott szoftver
- **SAP összetevő:** az egyes SAP-alkalmazások, például az ECC, BW, megoldás kezelő vagy EP SAP-összetevők a hagyományos ABAP vagy Java technológiák vagy egy nem NetWeaver alapú alkalmazás, például üzleti objektumok is alapulhat.
- **SAP-környezetben:** egy vagy több SAP összetevők logikusan egy üzleti funkciót, például a fejlesztési, a QAS, a képzési, a vész-Helyreállítási vagy a termelési végrehajtásához.
- **SAP fekvő:** az egész SAP eszközök az informatikai fekvő hivatkozik. Az SAP fekvő minden üzemi és nem éles környezetben tartalmaz.
- **SAP-verzió:** DBMS réteg és az alkalmazási rétegre egy SAP ERP fejlesztőrendszer SAP BW gazdagépes tesztrendszer, SAP CRM éles rendszer, stb. Azure-környezetekhez nem támogatják ezeket a helyszíni és az Azure közötti két réteget elválasztó. Azt jelenti, hogy egy SAP rendszerben telepített helyi, illetve az Azure-ban telepítve van. Azonban telepítheti a másik egy SAP fekvő Azure vagy a helyszíni rendszeren. Például elvégezheti Azure, a SAP CRM fejlesztési és tesztelési rendszerek SAP CRM éles rendszer a helyi telepítése közben. SAP Hana Azure (nagy példányokat) az szolgál az SAP alkalmazásréteg SAP rendszerek az Azure virtuális gépeken és a kapcsolódó SAP HANA-példányhoz a HANA nagy példány bélyegző egység gazdagép.
- **Nagy példány stamp:** hardver infrastruktúra verem egy SAP HANA TDI van hitelesített, és az Azure SAP HANA-példányok futtatásához dedikált.
- **SAP HANA Azure (nagy példány):** az SAP HANA TDI hardver, amely telepítve van a különböző Azure-régiók nagy példány bélyegzők hitelesített példánya HANA futtassa az Azure-ajánlatot hivatalos nevét. A kapcsolódó kifejezés **HANA nagy példány** rövid a SAP HANA Azure (nagy példányokat), és széles körben használt műszaki telepítési útmutatóban.
- **Létesítmények közötti:** ismerteti egy olyan forgatókönyvet, ahol a virtuális gépek Azure-előfizetéshez, amely rendelkezik pont-pont, többhelyes vagy a helyszíni datacenter(s) és az Azure között ExpressRoute kapcsolat van telepítve. Közös Azure dokumentációja, az ilyen típusú központi telepítések egyaránt létesítmények közötti forgatókönyv leírása. A kapcsolat oka, hogy a helyi tartomány, a helyszíni Active Directory/OpenLDAP és a helyi DNS kiterjeszti Azure. A helyszíni fekvő az időtartam, a társított Azure-előfizetéseit Azure eszközökre. Ha ezt a bővítményt, a virtuális gépek a helyi tartomány része lehet. A helyi tartomány tartományi felhasználók férhetnek hozzá a kiszolgálókat, és szolgáltatásokat futtathatja virtuális gépek (például adatbázis-kezelő szolgáltatás). Telepített virtuális gépek a helyszíni és az Azure telepített virtuális gépek közötti kommunikációt és a névfeloldás lehetőség. Például az a jellemző forgatókönyv, mely a legtöbb SAP eszközök vannak telepítve. Tekintse meg az útmutatók a [tervezése és kialakítása VPN-átjáró](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [hozhat létre egy Vnetet az Azure portál használatával webhelyek kapcsolattal rendelkező](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részletes információkat.
- **Bérlői:** HANA nagy példányok stamp telepített ügyfél lekérdezi elkülönített be a "bérlő." A bérlő elkülönül a hálózati, tárolási és számítási rétegét a többi bérlőtől. Tehát, hogy a különböző bérlők rendelt tárolási és számítási egység nem egymással vagy látni a HANA nagy példány stamp szinten kommunikálnak egymással. Az ügyfél beállíthatja úgy a különböző bérlők történő központi telepítések. Nincs még akkor is ezt követően a HANA nagy stamp példányszintű a bérlők közötti kommunikáció.
- **Termékváltozat-kategória:** a HANA nagy példányok, a következő két kategóriájához SKU érhető el.
    - **Írja be az osztály:** S72, S72m, S144, S144m, S192 és S192m
    - **II osztálya:** S384, S384m, S384xm, S576, S768 és S960


Nincsenek további források a Microsoft Azure nyilvános felhőjében SAP alkalmazások és szolgáltatások telepítése a közzétett különböző. Erősen ajánlott, hogy bárki tervezési és egy SAP HANA központi telepítés végrehajtása az Azure-ban tapasztalt és tisztában legyen a rendszerbiztonsági tagoknak az Azure infrastruktúra-szolgáltatási és Azure infrastruktúra-szolgáltatási SAP munkaterhelését telepítését. A következő források nyújtanak további információt, és a folytatás előtt meg lehet hivatkozni:


- [SAP megoldások segítségével a Microsoft Azure virtuális gépeken](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Minősítés

A NetWeaver hitelesítő mellett SAP egy SAP Hana SAP HANA támogatásához egyes infrastrukturális, például az Azure infrastruktúra-szolgáltatási speciális hitelesítő van szükség.

A Core SAP Megjegyzés NetWeaver, valamint egy mértékben SAP HANA hitelesítő [SAP Megjegyzés #1928533 – Azure SAP-alkalmazásokból: támogatott termékek és az Azure virtuális gép típusok](https://launchpad.support.sap.com/#/notes/1928533).

Ez [SAP Megjegyzés #2316233 - SAP HANA a Microsoft Azure (nagy példányok)](https://launchpad.support.sap.com/#/notes/2316233/E) is fontos. A megoldás a jelen útmutatóban ismertetett magában foglalja. Ezenkívül támogatottak az Azure GS5 VM típusú SAP HANA futtatásához. [Ebben az esetben az adatokat a SAP webhelyen van közzétéve](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

Az SAP HANA SAP Megjegyzés #2316233 említett Azure (nagy példányok) megoldás teszi lehetővé Microsoft és az SAP-ügyfelek központi telepítése a nagy SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA vagy más SAP HANA-munkaterhelések az Azure-ban. A megoldás az SAP-HANA jóváhagyott dedikált hardver stamp alapul ([SAP HANA igazított Datacenter integrációs – TDI](https://scn.sap.com/docs/DOC-63140)). Egy SAP HANA TDI futtató beállított megoldás lehetővé teszi az vetett bizalmat tudhatja, hogy, hogy minden SAP HANA-alapú alkalmazások (beleértve az SAP Business Suite az SAP HANA, SAP Business Warehouse (BW) SAP HANA, S4/HANA és BW4/HANA) működik a hardver infrastruktúra.

SAP HANA az Azure virtuális gépek futtatását a megoldás előnyt képest – sokkal nagyobb memória kötetek biztosít. Ez a megoldás engedélyezni szeretné, ha néhány fontos szempontot megértése:

- Az SAP alkalmazás réteg és nem SAP alkalmazások az Azure virtuális gépek (VM), amelyek a szokásos Azure hardverkonfiguráción bélyegek futnak.
- Ügyfél a helyszíni infrastruktúrát, adatközpontok és alkalmazások központi telepítéseit a Microsoft Azure cloud platform keresztül Azure ExpressRoute (ajánlott) vagy virtuális magánhálózati (VPN) csatlakoznak. Active Directory (AD) és a DNS is kiterjeszthetőek az Azure.
- Az SAP HANA-adatbázispéldány HANA munkaterhelés SAP HANA (nagy példányok) Azure-on futtatja. A nagy példány stamp be Azure networking csatlakozik, ezért az Azure virtuális gépeken futó szoftver képes interakciót folytatni a HANA-példány futtatási HANA nagy példányát.
- Hardver-vagy Azure (nagy példányok) SAP HANA megadott infrastruktúra (IaaS) szolgáltatás a SUSE Linux Enterprise Server dedikált hardver- vagy a Red Hat Enterprise Linux, előre telepítve. Mint az Azure virtuális gépek további frissítések és az operációs rendszer karbantartási feladata a.
- HANA és esetleges HANA nagy példányok egységek SAP HANA futtatásához szükséges további összetevők telepítésének feladata a, az összes megfelelő folyamatban lévő műveletek és SAP HANA Azure számára.
- Az itt leírt megoldásokkal, mellett más összetevők is telepíthető az Azure-előfizetéssel, amely kapcsolódik az Azure (nagy példányok) SAP HANA.  Például az és/vagy közvetlenül az SAP HANA-adatbázishoz való kommunikáció engedélyezése összetevőket (jump-kiszolgálók, RDP-kiszolgálók, SAP HANA Studio, a SAP Data Services SAP BI forgatókönyvek esetén, vagy hálózati figyelési megoldások).
- Azure, mint HANA nagy példányok támogató magas rendelkezésre állású és vész-helyreállítási funkciókat kínál.

## <a name="architecture"></a>Architektúra

Egy magas szintű: Azure (nagy példányok) megoldás a SAP HANA rendelkezik az SAP alkalmazásréteg szereplő Azure virtuális gépek és az adatbázisrétegben levő egy nagy példány stamp Azure IaaS kapcsolódó egy Azure-régióban található SAP TDI konfigurált hardver .

> [!NOTE]
> Az SAP alkalmazásréteg ugyanabban a régióban Azure SAP DBMS rétegként telepíteni kell. Ez a szabály szerepel a dokumentált Azure SAP munkaterhelés közzétett adatait. 

SAP HANA Azure (nagy példányok) átfogó architektúrája biztosít egy SAP TDI jóváhagyott hardver konfigurálása (nem virtualizált, operációs rendszer nélküli, a SAP HANA-adatbázishoz nagy teljesítményű kiszolgáló), és skálázási erőforrásokra vonatkozó Azure rugalmasságát és képessége az SAP alkalmazásréteg az igényeinek.

![Az SAP HANA (nagy példányok) Azure-architektúra áttekintése](./media/hana-overview-architecture/image1-architecture.png)

Az architektúra látható három részből áll:

- **Jobb:** az adatközpontok különböző alkalmazások futtatása a végfelhasználók számára (például SAP) LOB-alkalmazásokhoz való hozzáférés a helyszíni infrastruktúrát. Ideális esetben ez a helyszíni infrastruktúra kapcsolódik az Azure-ral Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Központ:** Azure IaaS mutat be, és ebben az esetben használata Azure virtuális gépek SAP vagy más SAP HANA adatbázis-kezelő rendszert használó alkalmazások futtatásához. Azure virtuális gépek memória függvény biztosító kisebb HANA-példányok az alkalmazási rétegre együtt vannak telepítve az Azure virtuális gépeken. Bővebben [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/).
<br />Azure hálózatkezelés SAP rendszerek más alkalmazások az Azure virtuális hálózatokról (Vnetekről) együtt használható. A Vnetek csatlakozni a helyszíni rendszerekben SAP HANA Azure (nagy példány).
<br />SAP NetWeaver alkalmazások és a Microsoft Azure rendszerben való futtatásra támogatott adatbázisok [SAP támogatási Megjegyzés #1928533 – Azure SAP-alkalmazásokból: támogatott termékek és az Azure virtuális gép típusok](https://launchpad.support.sap.com/#/notes/1928533). Üzembe helyezni az SAP-megoldások Azure dokumentációját lásd:

  -  [A Windows virtuális gépek (VM) SAP használatával](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [SAP megoldások segítségével a Microsoft Azure virtuális gépeken](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Balra:** jeleníti meg az SAP HANA TDI hitelesített hardver az Azure nagy példány bélyegző. A nagy példány HANA egység az Azure Vnetekhez ugyanazt a technológiát használja, a helyi kapcsolat az Azure előfizetéséhez csatlakoznak.

Az Azure nagy példány stamp maga egyesíti a következő összetevőket:

- **Számítások:** Intel Xeon E7-8890v3 vagy Intel Xeon E7-8890v4 processzorok szükséges számítási képességet biztosít, amelyek hitelesített SAP HANA alapuló kiszolgálók.
- **Hálózati:** A nagy sebességű hálózati háló kapcsolja össze a számítási, tárolási és hálózati összetevők egységes.
- **Tárolás:** olyan tárolási infrastruktúra, amely egy egységes hálózati háló keresztül érhető el. Attól függően, hogy az adott SAP HANA Azure (nagy példányok) konfigurációban telepített biztosított speciális tárolási kapacitás (további tárolási kapacitás érhető el további havi költségén).

A több-bérlős infrastruktúrájában a nagy példány stamp az ügyfelek elkülönített bérlők üzemelnek. A bérlő központi telepítését akkor kell Azure-előfizetések az Azure regisztrációs belül neve. A topológiában az Azure-előfizetéssel, az nagy HANA-példányokat érintetlen alapján kell fizetni. Ezek a bérlők rendelkezik az Azure-előfizetés 1:1 kapcsolattal. Hálózati bölcs lehetséges HANA nagy példány egység telepítése egy Azure-régióban különböző Azure Vnetekhez, amelyek különböző Azure-előfizetések tartoznak a egy bérlő eléréséhez. Bár ezen Azure-előfizetések kell tartozniuk a azonos Azure regisztrációt. 

Azure virtuális gépeken, a több Azure-régiók a SAP HANA Azure (nagy példányok) kínálják. Annak érdekében, hogy vész-helyreállítási lehetőségeket, dönthet úgy elvégezni a beléptetést. Egy földrajzi politikai régión belül különböző nagy példány bélyegzők kapcsolódnak egymáshoz. Például a HANA nagy példány bélyegzők Velünk nyugati és amerikai keleti vész-Helyreállítási replikáció céljából a dedikált hálózati kapcsolaton keresztül csatlakoznak. 

Ugyanúgy, mint a virtuális gép különböző Azure virtuális gépek közül választhat, SKU, HANA nagy példány, amely különböző alkalmazások és szolgáltatások típusú SAP HANA is lefednek közül választhat. SAP memória processzor szoftvercsatorna arányt tesz lehetővé az Intel processzorral generációja alapján különböző munkaterhelések vonatkozik. Az alábbi táblázat a Termékváltozat-típusok érhető el.

Től július 2017 SAP HANA Azure (nagy példányok) érhető el Azure régiók a US nyugati és amerikai keleti, Kelet-Ausztrália, Ausztrália délkeleti, Nyugat-Európa, és Észak-Európa alkalmazásban néhány konfigurálás:

| SAP-megoldás | CPU | Memory (Memória) | Tárolás | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| Az OLAP-re optimalizált: FF/4HANA, az SAP BW Programhoz<br /> vagy SAP HANA általános OLAP-munkaterhelés | Az Azure S72 SAP HANA<br /> – 2 x Intel® Xeon® processzor E7-8890 v3<br /> A Processzormagok 36 és 72 CPU szálak |  768 GB |  3 TB | Elérhető |
| --- | Az Azure S144 SAP HANA<br /> – 4 x Intel® Xeon® processzor E7-8890 v3<br /> A Processzormagok 72 és 144 CPU szálak |  1,5 TB |  6 TB | Nem érhető el többé |
| --- | Az Azure S192 SAP HANA<br /> – 4 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 96 és 192 CPU szálak |  2.0 TB |  8 TB | Elérhető |
| --- | Az Azure S384 SAP HANA<br /> – 8 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 192 és 384 CPU szálak |  4.0 TB |  16 TB | Készen áll a sorrendje |
| Az OLTP optimalizált: SAP Business csomag<br /> SAP HANA vagy az S/4HANA (OLTP)<br /> általános OLTP | Az Azure S72m SAP HANA<br /> – 2 x Intel® Xeon® processzor E7-8890 v3<br /> A Processzormagok 36 és 72 CPU szálak |  1,5 TB |  6 TB | Elérhető |
|---| Az Azure S144m SAP HANA<br /> – 4 x Intel® Xeon® processzor E7-8890 v3<br /> A Processzormagok 72 és 144 CPU szálak |  3.0 TB |  12 TB | Nem érhető el többé |
|---| Az Azure S192m SAP HANA<br /> – 4 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 96 és 192 CPU szálak  |  4.0 TB |  16 TB | Elérhető |
|---| Az Azure S384m SAP HANA<br /> – 8 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 192 és 384 CPU szálak |  6.0 TB |  18 TB | Készen áll a sorrendje |
|---| Az Azure S384xm SAP HANA<br /> – 8 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 192 és 384 CPU szálak |  8.0 TB |  22 TB |  Készen áll a sorrendje |
|---| Az Azure S576 SAP HANA<br /> – 12 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 288 és 576 CPU szálak |  12.0 TB |  28 TB | Készen áll a sorrendje |
|---| Az Azure S768 SAP HANA<br /> – 16 x Intel® Xeon® processzor E7-8890 v4<br /> A Processzormagok 384 és 768 CPU szálak |  16,0 TB |  36 TB | Készen áll a sorrendje |
|---| Az Azure S960 SAP HANA<br /> – 20 x Intel® Xeon® processzor E7-8890 v4<br /> 480 Processzormagok és 960 CPU szálak |  20.0 TB |  46 TB | Készen áll a sorrendje |

- A Processzormagok = Processzormagok nem-többszálú a processzorok, a kiszolgáló egység összegére összege.
- CPU szálak CPU-magokat többszálú a processzorok, a kiszolgáló egység összegére által biztosított számítási szálak számának összege, azaz =. Minden egység alapértelmezés szerint a Hyper-Threading használatára vannak konfigurálva.


A különböző konfigurációkat fenti állnak rendelkezésre, vagy "SE ajánlja többé" hivatkozott [SAP támogatási Megjegyzés #2316233 – a Microsoft Azure (nagy példányok) SAP HANA](https://launchpad.support.sap.com/#/notes/2316233/E). A konfigurációk, "Készen áll a sorrend" jelű hamarosan megkeresi az SAP Megjegyzés beléptetése. Azonban ezen példány termékváltozatok által rendelhető már a hat különböző Azure-régiók a HANA nagy példány szolgáltatás nem érhető el.

A kiválasztott konfigurációkkal munkaterhelés, a Processzor-erőforrások és a kívánt memória függenek. Az OLTP-munkaterhelés OLAP munkaterhelések az SKU optimalizált használandó lehetőség. 

Az ajánlatok hardver alapja SAP HANA TDI hitelesített. Azonban különböztetünk hardver, az SKU osztó két különböző osztály között:

- S72, S72m, S144, S144m, S192 és S192m, amely a "Type i. osztály" lesz az SKU.
- S384, S384m, S384xm, S576, S768 és S960, amely a "típusú class" SKU lesz az.

Fontos megjegyezni, hogy egy teljes HANA nagy példány stamp nem kizárólag számára kíván lefoglalni az egyetlen ügyfél &#39; s használja. Ezt a tényt a számítási és tárolási erőforrásokat egy Azure szolgáltatásba telepített, valamint hálózati háló összekapcsolt rackszekrények vonatkozik. HANA nagy példányok infrastruktúra, például Azure, telepíti a különböző ügyfél &quot;bérlők&quot; , amelyek elkülönülnek egymástól a következő három szintje:

- Hálózati: Virtuális hálózatok a HANA nagy példány stamp elkülönítését.
- Tárolás: A tároló virtuális gépeken, amelyek tárolókötetek elkülönítését hozzárendelt, és tárolókötetek bérlők között elkülönítése.
- Számítási: Dedikált hozzárendelés server egységek egyetlen bérlő számára. Nem rögzített vagy soft particionálási kiszolgáló egységek. Egyetlen kiszolgáló vagy a gazdagép egység bérlők között nincs megosztás. 

A különböző bérlők között HANA nagy példányok egységek telepítéseit, egymással nem láthatók el. És HANA a különböző bérlők rendszerbe nagy példány egységek közvetlenül kommunikálhatnak egymással a HANA nagy példány stamp szintjén nem. Csak HANA nagy példány egységek belül egy bérlő HANA nagy példány stamp szint kommunikálhatnak egymással.
A nagy példány stamp egy telepített tenant hozzá van rendelve, számlázási bölcs egy Azure-előfizetéshez. Azonban a hálózati bölcs belül az azonos Azure regisztrációs más Azure-előfizetések az Azure Vnetekhez tartalomtárban. Ha telepít egy másik Azure-előfizetés azonos Azure-régióban, is választhat egy elkülönített HANA nagy példány bérlő kérni.

SAP HANA futó HANA nagy példányok és az Azure szolgáltatásba telepített Azure virtuális gépeken futó SAP HANA között jelentős különbség van:

- Nincs nincs virtualizálási réteg SAP Hana Azure (nagy példány). Az alapul szolgáló operációs rendszer nélküli hardver teljesítményét kap.
- Azure-ban eltérően az SAP HANA Azure (nagy példányok) kiszolgálón adott ügyfélhez van kijelölve. Nincs lehetőségét, hogy a kiszolgáló egység vagy a gazdagép nehéz vagy soft-particionálva van. Ennek eredményeképpen a HANA nagy példány egység használatos rendelt egész a bérlők és az, hogy az ügyfél. Újraindítás vagy a kiszolgáló leállása nem vezet automatikusan az operációs rendszer és az SAP HANA üzembe helyezéséhez egy másik kiszolgálón. (Típus i. osztály SKU, az egyetlen kivétel, ha egy kiszolgáló problémák léphetnek fel, és újbóli üzembe helyezése egy másik kiszolgálón végrehajtandó.)
- Azure, ahol processzor állomástípusok választja ki a legjobb price/teljesítményesemények aránya, ellentétben a processzor típusok az SAP HANA Azure (nagy példányok) választott a következők az Intel E7v3 és E7v4 processzor sor legmagasabb hajt végre.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Több SAP HANA-példány fut egy HANA nagy példány egység
A nagy példány HANA egységek több aktív SAP HANA példány futtatására lehetőség. Ahhoz, hogy a Storage-pillanatfelvételekkel, valamint a vész-helyreállítási lehetőségeket nyújtanak, ilyen konfiguráció példányonként kötet van szükség. Mostantól a HANA nagy példány egység az alábbiak szerint lehet osztani:

- S72, S72m, S144, S192: lépésekben 256 GB a legkisebb kezdési egység 256 GB. Az egység a memória maximális össze lehet kombinálni különböző nagyobb, mint 256 GB, 512 GB és így tovább.
- S144m és S192m: 512 GB legkisebb 256 GB-os lépésekben. Az egység a memória maximális össze lehet kombinálni különböző nagyobb, mint 512 GB, 768 GB és így tovább.
- Írja be a II osztály: 2 TB-os a legkisebb kezdési egységgel 512 GB-os lépésekben. Az egység a memória maximális össze lehet kombinálni különböző nagyobb, mint 512 GB, 1 TB-os, 1,5 TB és stb.

Néhány példa a több SAP HANA-példányt futtató nézhet:

| SKU | Memória mérete | Storage mérete | Több adatbázisból méretek |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA példány<br /> vagy példányt. 1 x 512 GB + 1 x 256 GB példány<br /> vagy 3 x 256 GB példányok | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA példányok<br />vagy példányt. 1 x 512 GB + 1 darab 1 TB-példány<br />vagy 6 x 256 GB példányok<br />vagy 1x1.5 TB-példány | 
| S192m | 4 TB | 16 TB | 8 x 512 GB példányok<br />vagy 4 x 1 TB-példányok<br />vagy 4 x 512 GB példányok + 2 darab 1 TB-példányok<br />vagy 4 x 768 GB példányok + 2 darab 512 GB példányok<br />vagy 1 darab 4 TB-példány |
| S384xm | 8 TB | 22 TB | 4 x 2 TB-példányok<br />vagy 2 darab 4 TB-példányok<br />vagy példányok 2 x 3 TB + 1 x 2 TB-példányok<br />vagy példányok 2x2.5 TB + 1 x 3 TB-példányok<br />vagy 1 x 8 TB-példány |


A képet kaphat. Biztosan más változatok vannak is. 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA-adatok rétegezéséhez és -kiterjesztést csomópontokból
SAP SAP BW különböző SAP NetWeaver kiadásainak és az SAP BW/4HANA adatok rétegezéséhez modell támogatja. Részletek a modell a dokumentum és a jelen dokumentum SAP által hivatkozott blog található adatok rétegezéséhez vonatkozó: [SAP BW/4HANA és SAP BW ON HANA az SAP HANA BŐVÍTMÉNY csomópontok](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
HANA nagy osztályt 1. lehetőség konfigurációs SAP HANA bővítmény csomópontok megadottaknak megfelelően ezt gyakran ismételt kérdések és SAP blog dokumentumok is használhatja. 2. lehetőség konfigurációk beállíthatja a következő nagy HANA-példány termékváltozatok rendelkező: S72m, S192, S192m, S384 és S384m.  
Megnézi a dokumentáció a ki nem feltétlenül látható azonnal. Csak azokat az SAP méretezési útmutatást keresése, előnyös beállítás-1 és 2. lehetőség SAP HANA-bővítmény csomópontok használatával. Itt egy példát:

- SAP HANA méretezési irányelvek elvégzéséhez adatmennyiség összeg memória. Igen SAP HANA-példány futtatja a gyakran használt adatokkal, csak akkor kell 50 % vagy kevesebb, a memória megtelt adatokkal. A fennmaradó memória ideális tárolt SAP Hana végez a műveletet.
- Ez azt jelenti, 2 TB memóriával, az SAP BW-adatbázisát futtató rendelkező HANA nagy példány S192 egységben csak akkor kell 1 TB-os adatok kötetként.
- Egy további SAP HANA kiterjesztési csomópontnak az 1. lehetőség használatakor is egy S192 HANA nagy példány SKU, az ad meg egy további 2 TB kapacitást adatmennyiség. A meleg adatmennyiség 2. lehetőség konfigurációs még akkor is, és további 4 TB. A működés közbeni csomópont képest, a teljes memória kapacitását a "meleg" kiterjesztési csomópontnak az adatok tárolását az 1. lehetőség és dupla a memóriát használhat a 2. lehetőség SAP HANA bővítmény csomópont-konfiguráció adatmennyiség használható.
- Emiatt, végül 3 TB kapacitású az adatok és az 1:1. lehetőség 2 közbeni-az-meleg arányú és 5 TB-nyi adat és a 2. lehetőség bővítmény csomópont-konfiguráció 1:4 aránya.

Azonban minél nagyobb adatmennyiség képest a memória, annál nagyobb az esélye, hogy a meleg adatokat arra utasítja a lemezes tárolás is megtalálható.


## <a name="operations-model-and-responsibilities"></a>Operatív modell és felelősségek

A megadott SAP HANA Azure (nagy példányok) szolgáltatás Azure IaaS szolgáltatások igazodik. Egy SAP HANA optimalizált telepített operációs rendszer HANA nagy példányok példány elérhetővé. Azure IaaS virtuális gépeket, az operációs rendszer korlátozására feladatainak többségét a további szoftver telepítése van szüksége, HANA telepítése, az operációs rendszer és a HANA, működő, és a feladata az operációs rendszer és a HANA frissítése. A Microsoft nem kényszeríti az operációs rendszer vagy HANA-frissítéseket, a.

![SAP HANA Azure (nagy példányok) feladatai](./media/hana-overview-architecture/image2-responsibilities.png)

Mint a fenti ábrán látható, az Azure (nagy példányok) SAP HANA egy több-bérlős infrastruktúra, mint a szolgáltatás ajánlat. És emiatt felelős az osztálynak a pontnál operációsrendszer-infrastruktúrát, részben. Microsoft felelős a sor az operációs rendszer alatt a szolgáltatás minden elemét, és az Ön felelőssége a sort, beleértve az operációs rendszer fent. Ezért legfrissebb helyszíni módszerek is alkalmazó megfelelőségi, biztonsági, alkalmazáskezelés, alapján, és az operációs rendszer felügyeleti továbbra is használható. A rendszer jelennek meg, ha a hálózat összes tanúsítványinformációit vannak.

Azonban ez a szolgáltatás megfelelően lett optimalizálva SAP Hana, ahol az Ön és a Microsoft kell együttműködése az alapul szolgáló infrastruktúra szolgáltatásokat használhatja a legjobb eredmények elérése érdekében területet.

Az alábbi lista részletesen ismerteti a Rétegek és az Ön feladatkörei:

**Hálózatkezelés:** a belső hálózatok, a SAP HANA, a tárolási, a a példányt (a kibővített és egyéb funkciók), a kapcsolat a fekvő közötti kapcsolat és a kapcsolatot az Azure-ba való hozzáférésének futtató nagy példány bélyegző ahol az SAP alkalmazásréteg tárolja az Azure virtuális gépeken. A vész-helyreállítási célokra replikáció Azure-adatközpontokban közötti WAN-kapcsolatra is tartalmaz. Az összes hálózatot a bérlő által particionáltak, és QOS alkalmazott.

**Tárolás:** a virtualizált particionálva az összes kötet az SAP HANA-kiszolgálók által igényelt, valamint a pillanatképek tárolási. 

**Kiszolgálók:** a dedikált fizikai kiszolgálók a bérlők rendelt SAP HANA-adatbázisok futtatásához. A kiszolgálók típusú i. osztály SKU hardver azért. Az ilyen kiszolgálók a kiszolgáló konfigurációs gyűjti és őrzi meg a profilok, amelyek egy másik fizikai hardver egy fizikai hardverek helyezheti át. Ilyen (manuális) áthelyezésre profil műveletek hasonlítható kicsit Azure szolgáltatásjavításnak. A kiszolgálók a típusú osztály SKU nem áll rendelkezésre egy ilyen képesség.

**SDDC:** szoftveralapú entitásként adatközpontok a szoftver, amely adatok kezelésére szolgál. Microsoft lehetővé teszi a készlet erőforrásokhoz, a méretezés, a rendelkezésre állás és a teljesítmény szempontjából.

**Rendszer:** az operációs rendszer választja (SUSE Linux vagy a Red Hat Linux) futtató kiszolgálókon. Az operációs rendszer lemezképeit rendelkezésre állnak a lemezképek az egyes Linux gyártója által biztosított Microsoft SAP HANA futtatása céljából. Meg kell a Linux gyártójával konkrét SAP HANA-re optimalizált kép előfizetéssel rendelkezik. Az Ön feladatkörei közé tartozik a képek regisztrálása az operációs rendszer szállítójához. A Microsoft által átadási pontról is való telepítésért felelős semmilyen további javítását a Linux operációs rendszer. A javítás is tartalmaz, amely egy SAP HANA sikeres telepítéséhez szükséges lehet további csomagokat (lásd a SAP HANA-dokumentáció és SAP megjegyzések) és amelyek nem szereplő az SAP HANA adott Linux szállító optimalizált az operációs rendszer képek. Az ezzel az ügyfél is, az operációs rendszer, az operációs rendszer és az adott kiszolgálói hardverekkel kapcsolatos illesztőprogramja hibásan működik/optimalizálása kapcsolódó javítását. Vagy semmilyen biztonsági vagy az operációs rendszer működési javítását. Az ügyfél felelőssége, valamint a figyelési- és a kapacitás-tervezési:

- Processzor-erőforrás-felhasználás
- Memória-felhasználás
- Lemezkötetek kapcsolódik a szabad terület, az iops-érték és a késleltetés
- Kötet forgalmat HANA nagy példány és az SAP alkalmazásréteg között

Az alkalmazás mögötti infrastruktúra HANA nagy példányok biztonsági mentéséhez és helyreállításához az operációs rendszer kötet funkciókat biztosít. Ez a funkció használata is a felelős.

**Köztes:** az SAP HANA-példány, elsősorban. Felügyeleti műveletek és figyelési az Ön felelőssége. Nincs funkció megadott, amely lehetővé teszi a tárolási pillanatképek használja biztonsági mentés/visszaállítás és vész-helyreállítási célokra. Ezek a képességek a infrastruktúra által biztosított. Azonban az Ön feladatkörei is magas rendelkezésre állás és vészhelyreállítás tervezése ezekkel a lehetőségekkel, használhatja őket és megfigyelést a storage-pillanatfelvételekkel végrehajtása sikerült.

**Adatok:** az SAP HANA által kezelt adatok, és más adatok, például biztonsági másolatok köteteken található fájlok vagy a fájl megosztja. Szabad lemezterület ellenőrzése a tartalmat azokon a köteteken, és felügyelete a kötetek biztonsági mentése lemezre és storage-pillanatfelvételekkel sikeres végrehajtását közé tartoznak az Ön feladatkörei Vész-Helyreállítási hely replikálása sikeres végrehajtását azonban Microsoft felelőssége.

**Alkalmazások:** az SAP alkalmazáspéldányok, vagy nem SAP-alkalmazásokból, ezeknek az alkalmazásoknak az alkalmazási rétegre esetén. Az Ön feladatkörei közé tartoznak a telepítési, adminisztrációs, műveletek és ezeknek az alkalmazásoknak kapacitástervezés CPU erőforrás-felhasználás, memória-felhasználás, Azure-tároló fogyasztása és hálózati sávszélesség-használat belül kapcsolatos figyelési Az Azure Vnetekhez, és az Azure Vnetekhez az SAP HANA Azure (nagy példány).

**WAN:** a kapcsolatok létrehozása a helyszíni munkaterhelések Azure-környezetekhez. Ügyfeleink HANA nagy osztályt Azure ExpressRoute kapcsolatot használjon. Ez a kapcsolat nem részét képezi az Azure (nagy példányok) megoldás, a SAP HANA, Ön felelősséggel tartozik a kapcsolat beállítása.

**Archív:** előfordulhat, hogy inkább archiválja a saját módszerekkel tárfiókokban lévő adatok másolatát. Archiválás szükséges felügyeleti, a megfelelőségi, a költségek és a műveletek. Ön felelősséggel tartozik archív másolatok és az Azure biztonsági mentések létrehozásához, és tárolja őket a megfelelő módon.

Tekintse meg a [SAP Hana (nagy példányok) Azure SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Méretezése

Nagy HANA-példányok méretezési ugyanolyan helyzetet teremt, mint általában Hana méretezése. Meglévő telepített rendszereken, és át más RDBMS HANA, SAP számos a meglévő SAP rendszereken futó jelentéseket szeretne. Ha az adatbázis HANA helyezik át, ezek a jelentések ellenőrizze az adatokat, és számítja ki a memória a HANA-példány. Az alábbi SAP szerepelnek ezek a jelentések futtatása, és hogyan kell beszerezni a legújabb javítások verzióját további információért olvassa el:

- [SAP Megjegyzés #1793345 - HANA az SAP Suite méretezése](https://launchpad.support.sap.com/#/notes/1793345)
- [Megjegyzés: #1872170 - csomag HANA és S/4 HANA méretezési jelentésre SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Megjegyzés #2121330 - gyakran ismételt kérdések: A jelentés méretezése HANA SAP BW Programhoz](https://launchpad.support.sap.com/#/notes/2121330)
- [Megjegyzés: #1736976 - HANA méretezési BW jelentés SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Megjegyzés: #2296290 - új méretezési jelentés SAP BW a HANA a](https://launchpad.support.sap.com/#/notes/2296290)

Zöld mező megvalósítások esetében gyors Szimbólumméretező SAP HANA fölött SAP szoftver végrehajtásának memóriaigényének kiszámításához rendelkezésére áll.

Memóriára vonatkozó követelményeknek Hana növekednek adatmennyiség növekedésével, a memória-felhasználás most vegye figyelembe, és képes előre jelezni, Mi ez lesz a jövőben lehet. Memória követelményeinek megfelelően, majd leképezheti a igény szerint a HANA nagy példány termékváltozatok valamelyikére.

## <a name="requirements"></a>Követelmények

Ez a lista állítja össze a SAP HANA futtatásához Azure (nagyobb példány).

**A Microsoft Azure:**

- Azure-előfizetéssel, amely az Azure (nagy példányok) SAP HANA lehet társítani.
- Microsoft Premier szintű támogatási szerződése. Lásd: [SAP támogatási Megjegyzés #2015553 – a Microsoft Azure SAP: támogatás Előfeltételek](https://launchpad.support.sap.com/#/notes/2015553) Azure-beli SAP kapcsolatos részletes információkat. HANA nagy példány egységek 384 és több processzort használ, ki is kell terjesztenie a Premier szintű támogatási szerződése Azure gyors válasz (ARR) tartalmazza.
- Ezekre a műveletekre termékváltozatok meg kell a méretezési végrehajtása után gyakorolni az SAP HANA nagy példánya.

**Hálózati kapcsolat:**

- Az Azure-bA helyszíni közötti Azure ExpressRoute: csatlakozni a helyszíni adatközpontját Azure, ügyeljen arra, hogy az internetszolgáltató által biztosított legalább 1 GB/s kapcsolat sorrendben. 

**Operációs rendszer:**

- Licencek az SUSE Linux Enterprise Server 12 SAP-alkalmazásokból.

> [!NOTE] 
> A Microsoft által kiadott operációs rendszer nincs regisztrálva a SUSE, és nem kapcsolódik SMT példánya.

- SUSE Linux előfizetés felügyeleti eszköz (SMT) üzembe helyezett Azure egy Azure virtuális gépen. Ezzel a megoldással SAP Hana Azure (nagy példányok) regisztrálni és rendre SUSE frissítette (mivel nincs internet-hozzáférés HANA nagy példányok adatközpont belül). 
- Red Hat Enterprise Linux 6.7 vagy 7.2 SAP Hana licencet.

> [!NOTE]
> Red Hat nincs regisztrálva a Microsoft által kiadott operációs rendszer, és nem csatlakozik a Red Hat előfizetés Manager példánya.

- Red Hat előfizetés kezelő üzembe helyezett Azure egy Azure virtuális gépen. A Red Hat előfizetés-kezelő lehetővé teszi a SAP Hana Azure (nagy példányok) regisztrálni és rendre Red Hat frissítette (mivel nincs közvetlen internet-hozzáférést a belül a bérlő az Azure nagy példány stamp telepítve van).
- SAP szükséges hozzá egy támogatási szerződés, valamint a Linux-szolgáltatónál. Ez a követelmény nem törlődnek a megoldás HANA nagy példányok vagy azt a tényt, amely a futtatási Linux az Azure-ban. Eltérően az egyes Linux Azure gyűjtemény képek, a szolgáltatás díj nem szerepel a megoldás ajánlat HANA nagy példányok. Van, a követelményeit az SAP támogatási szerződések a Linux terjesztő teljesítéséhez ügyfélként.   
   - SUSE Linux, keresse meg a támogatási szerződése követelményeinek [SAP Megjegyzés #1984787 - SUSE LINUX Enterprise Server 12: telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787) és [SAP Megjegyzés #1056161 - SUSE prioritás támogatása SAP-alkalmazásokból](https://launchpad.support.sap.com/#/notes/1056161).
   - Red Hat Linux szüksége van a megfelelő előfizetés támogatja, és a szolgáltatási (az operációs rendszerek HANA nagy példányok frissítése. Red Hat azt javasolja, hogy első egy "az RHEL [SAP megoldások](https://access.redhat.com/solutions/3082481)" előfizetést. 

Ellenőrizze a különböző SAP HANA-verzió-a különböző Linux-verziók támogatási mátrix, [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Adatbázis:**

- Licencek és a szoftver telepítési összetevők SAP Hana (platform vagy enterprise edition).

**Alkalmazások:**

- Licencek és a szoftver telepítési összetevők SAP HANA csatlakozik, és a kapcsolódó SAP SAP alkalmazások támogatja a szerződéseket.
- Licencek és a szoftver telepítési összetevők nem SAP alkalmazások Azure (nagy példányok) környezetben található SAP HANA képest használt, és a kapcsolódó támogatási szerződés.

**Képességek:**

- Felhasználói élmény és az Azure infrastruktúra-szolgáltatási és az összetevőinek a Tudásbázis.
- Felhasználói élmény és az Azure-ban SAP alkalmazások és szolgáltatások telepítése a Tudásbázis.
- SAP HANA-telepítés hitelesített személyes.
- SAP felelős mérnök ismereteikkel tervezési magas rendelkezésre állású és vész-helyreállítási SAP HANA körül.

**SAP:**

- Általános gyakorlat, hogy az ügyfél egy SAP, és rendelkezik a támogatási szolgálathoz, SAP szerződés
- Különösen olyan a típusa II osztály HANA nagy példány SKU-implementációk esetén ajánlott hogy SAP SAP HANA és az esetleges nagy méretű méretezett hardver-konfigurációit verzióiban.


## <a name="storage"></a>Tárolás

A tárolási elrendezés SAP Hana Azure (nagy példányok) be van állítva az Azure szolgáltatásfelügyeleti ajánlott irányelvek részletes ismertetését lásd: SAP keresztül SAP HANA a [SAP HANA tárhellyel kapcsolatos követelmények](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) találhatók meg.

Tárolási kötetként négyszer a memória kötet i. osztály típusú HANA nagy példányok rendelkeznek. A típus II osztály HANA nagy példány egységek a tárolási nem lesz négy alkalommal további. Az egységek rendelkeznek egy köteten, HANA tranzakciós napló biztonsági mentések tárolására számára készült. A további részletekért [telepítése és konfigurálása Azure SAP HANA (nagy példány)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Lásd az alábbi táblázatban a Foglalás tekintetében. A táblázat a különböző kötetek, a másik HANA nagy példány egységek biztosított kapacitás nagyjából.

| HANA nagy példány Termékváltozat | Hana/adatok | Hana/napló | Hana/megosztott | Hana/naplómásolat |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36,000 GB | 4100 GB | 2050 GB | 4100 GB |


Tényleges telepített kötetek üzembe helyezési és a kötet mérete megjelenítéséhez használt eszköz egy kicsit függően eltérhetnek.

Ha tovább HANA nagy példány SKU, néhány példa a lehetséges osztás darab alábbihoz hasonlóan fog kinézni:

| Memória partíció GB-ban | Hana/adatok | Hana/napló | Hana/megosztott | Hana/naplómásolat |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304-ES GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


Ezen értékek nyers mennyiségi számát, amelyek alapján némileg telepítése és eszközök használatával nézze meg a kötetek eltérőek lehetnek. Is vannak más partíció méretét thinkable, például a 2,5 TB. Ezen tárolási méretek volna számítható ki, egy hasonló képlettel használt a fenti partíciók számára. A partíciók kifejezés nem jelzi, hogy az operációs rendszer, a memória vagy a Processzor-erőforrások bármely módon particionálva van. Azt jelzi, érdemes egyetlen HANA nagy példány egységben a telepítendő különböző HANA-példányok adattárolási partíciókat csak. 

Az ügyfél lehet, hogy van szüksége további tárhelyet, lehetősége van 1 TB-os egységekben további tárterület vásárlása tár hozzáadása. A további tárhely további kötetként felveheti, vagy legalább egy, a meglévő kötetek kiterjeszteni használható. Nincs lehetőség a a kötetek méretének csökkentése eredetileg telepített, és főként a fenti táblák dokumentálnia. Lehetetlen is módosítható a köteteket a neve, vagy csatlakoztatási nevét. A fent ismertetett tárolási köteteket a HANA nagy példány mértékegységet NFS4 kötetek vannak csatolva.

Ügyfélként választhat storage-pillanatfelvételekkel biztonsági mentés/visszaállítás, valamint a vész helyreállítási célokra használni. További részletekért részletes leírást talál [SAP HANA (nagy példányok) magas rendelkezésre állás és vészhelyreállítás Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
A nagy HANA-példányok használt tároló lehetővé teszi, hogy az adatok átlátható titkosítási a lemezen tárolt. A központi telepítéskor HANA nagy példány egység a beállítással engedélyezhető a titkosítás eredő rendelkezik. Tudja meg titkosított kötetek már a telepítés után módosítsa. Az áthelyezés nem titkosított a titkosított kötetekre átlátható, és nem igényli a leállás. 

Azzal a típussal, i. osztály SKU, a logikai egység tárolják, a rendszerindító kötet titkosítva van. Esetén a típus II osztály példányainak termékváltozatok a HANA nagy az operációs rendszer módszerek a rendszerindítás LUN titkosítania kell. További információért forduljon a Microsoft szolgáltatás-felügyeleti csoport.


## <a name="networking"></a>Hálózat

Azure hálózati architektúra sikeres alkalmazások központi telepítését figyelemmel SAP HANA-példányokon nagy a nyilvános kulcsokra épülő. SAP HANA Azure (nagy példányok) üzemelő példányok esetében általában egy nagyobb SAP, számos különböző SAP-megoldások különböző méretű adatbázisok, a Processzor-erőforrás-felhasználás és a memóriahasználat a fekvő tájolás kell. Valószínűleg nem az összes az SAP rendszereket alapulnak SAP HANA, a SAP fekvő valószínűleg használó hibrid:

- SAP rendszerek helyszíni üzembe. A mérete miatt ezek a rendszerek nem jelenleg Azure-ban üzemeltetett; a klasszikus példa erre egy éles SAP ERP rendszer fut a Microsoft SQL Server (adatbázisként) további Processzort igényel, amely vagy a memória-erőforrások Azure virtuális gépeket biztosíthatnak.
- SAP SAP HANA-alapú rendszerek helyszíni üzembe.
- Azure virtuális gépeken telepített SAP rendszerek. Ezek a rendszerek fejlesztési, tesztelési, védőfal, vagy az SAP NetWeaver-alapú alkalmazások, amelyek az Azure-ban (a virtuális gépeken), erőforrás használat és a memória igény szerint sikeres telepítése éles példányának. Ezek a rendszerek is alapulhatnak például az SQL Server adatbázisok (lásd: [SAP támogatási Megjegyzés #1928533 – Azure SAP-alkalmazásokból: támogatott termékek és az Azure virtuális gép típusok](https://launchpad.support.sap.com/#/notes/1928533/E)) vagy SAP HANA (lásd: [SAP HANA hitelesített IaaS platformok](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)).
- Telepített SAP alkalmazáskiszolgálók (a virtuális gépeken) Azure-ban Azure (nagy példány) SAP HANA kihasználhatja az Azure nagy példány stampek.

Míg egy hibrid SAP fekvő (a négy vagy több különböző központi telepítési forgatókönyv) jellemző, nincsenek Azure-beli teljes SAP fekvő eseteit sok ügyfél. A Microsoft Azure virtuális gépek egyre nagyobb teljesítményű, helyezze át az SAP-megoldások Azure ügyfelek száma növekszik.

Hálózat az Azure szolgáltatásba telepített SAP rendszerek környezetében Azure nincs bonyolult. A következő alapelveket alapul:

- Az Azure virtuális hálózatokról (Vnetekről) kell csatlakoztatni kell a helyszíni hálózathoz csatlakozó Azure ExpressRoute-kapcsolatcsoportot.
- Általában a helyszíni csatlakozik Azure ExpressRoute-kapcsolatcsoportot rendelkeznie kell a sávszélesség 1 GB/s vagy újabb. A minimális sávszélesség lehetővé teszi a megfelelő sávszélesség az adatok átviteléhez a helyszíni és a futó Azure virtuális gépeken (valamint a helyszíni felhasználók Azure rendszerek kapcsolat) rendszerek között.
- Az Azure-ban minden SAP rendszer kell lehet állítani az Azure Vnetekhez kommunikálnak egymással.
- Active Directory és a DNS a helyben tárolt kiterjeszthetőek az Azure ExpressRoute segítségével a helyi.


> [!NOTE] 
> A számlázási szempontjából csak egy Azure-előfizetéssel csak az adott Azure-régió egy nagy példány stamp egy egybérlős lehet társítani, és ezzel szemben egy nagy példány stamp egybérlős kapcsolható csak egy Azure-előfizetéssel. Ez a tény nem eltér a más számlázható objektumok az Azure-ban

A nagy példány stamp telepítését külön bérlői eredményezi üzembe helyezése az Azure (nagy példányok) több különböző Azure-régiókban SAP HANA. Azonban futtathatja mindkét azonos Azure-előfizetést a mindaddig, amíg ezek a példányok az azonos SAP fekvő részét képezik. 

> [!IMPORTANT] 
> Csak az Azure Resource Manager deployment SAP HANA Azure (nagy példányok) használata támogatott.

 

### <a name="additional-azure-vnet-information"></a>További Azure VNet-információk

Egy Azure virtuális hálózatot ExpressRoute való csatlakozáshoz, létre kell hozni egy Azure-átjáró (lásd: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Egy Azure-átjáró vagy ExpressRoute az Azure-on kívüli infrastruktúrát (vagy egy Azure nagy példány stamp), vagy Azure Vnet közötti kapcsolat használható (lásd: [konfigurálja a VNet – VNet-kapcsolatot az erőforrás-kezelő PowerShellhasználatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Legfeljebb négy különböző ExpressRoute-kapcsolatot az Azure-átjáró is elérheti, mindaddig, amíg másik MS vállalati szélek (MSEE) útválasztók érkező ezeket a kapcsolatokat.  További információkért lásd: [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolatokat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Egy Azure átjáró átirányítást biztosít a átviteli eltér mindkét esetben használja (lásd: [VPN-átjáró](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A maximális átviteli sebesség azt érhető el a virtuális hálózat átjáróval 10 GB/s, egy ExpressRoute-kapcsolat használatával. Ne feledje, hogy egy Azure virtuális gép található egy Azure virtuális hálózatot és a rendszer között a fájlok másolása a helyszíni (adatfolyamként egyetlen másolatának), a teljes átviteli képessége – a különböző gateway SKU nem érhetik el. Ki a virtuális hálózat átjáró teljes sávszélességét, használjon több adatfolyam, vagy egyetlen fájl párhuzamos adatfolyamok különböző fájlok másolása.


### <a name="networking-architecture-for-hana-large-instances"></a>Hálózati architektúra nagy HANA-példányok
A hálózati architektúra HANA nagy példányok az alábbi módon el lehet különíteni a négy részből:

- A helyszíni hálózat és az Azure ExpressRoute-kapcsolatot. Ez a rész az ügyfelek tartománya, és kapcsolódik az Azure ExpressRoute segítségével. Ez az a része, az alábbi ábrák jobb alsó sarkában.
- Az Azure hálózatkezelés szerint röviden kapcsolatban a fentiekben ismertetett az Azure Vnetekhez, újra rendelkező átjárókat. Ez az egy olyan területre, ahol a megfelelő tervek keresése az alkalmazások követelményeinek, biztonsági és megfelelőségi követelményeknek kell. HANA nagy példányok használata egy másik virtuális hálózatokat és Azure gateway SKU választhat számát figyelembe pontját. Ez az a kijelző jobb felső sarkában a grafikus nyomtatást.
- Nagy HANA-példányok keresztüli kapcsolat ExpressRoute technológia az Azure. Ez a kijelző telepített, és a Microsoft kezeli. Összes kell tennie, mivel az ügyfél arra, hogy bizonyos IP-címtartományok és a csatlakozás az ExpressRoute HANA nagy esetekben eszközök üzembe helyezése után áramkör az Azure VNet(s) (lásd: [(nagy példányok) SAP HANA-infrastruktúra és kapcsolat az Azure-on](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
- Hálózati HANA nagy példányát, ez utóbbi érték többnyire átlátszó meg ügyfélként.

![Az Azure virtuális hálózat csatlakozik az Azure (nagy példányok) és a helyszíni SAP HANA](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Az a tény HANA nagy példányok használata nem változik a követelmény az Azure ExpressRoute keresztül csatlakoznak a helyszíni eszközök eléréséhez. Azt is nem módosítja egy vagy több Vnetek HANA nagy példány egység az Azure virtuális gépek melyik gazdagépre az alkalmazási rétegre, amely kapcsolódik a HANA példányok üzemeltetett futtató rendelkező vonatkozó követelmény. 

A különbség az SAP-központitelepítések csak Azure-ban a tények tartalmaz, amelyek:

- Az ügyfél-bérlő HANA nagy példány egységei az Azure VNet(s) be egy másik ExpressRoute-kapcsolatcsoportot keresztül kapcsolódó. Ahhoz, hogy külön munkaterhelés esetére, a helyi az Azure Vnetekhez ExpressRoute- és hivatkozásokat Azure Vnet és HANA nagy példányok között nem osztható meg a azonos útválasztók.
- A munkaterhelés profil az SAP alkalmazási rétegre és a HANA példány között a különböző jellegű a sok kisméretű kérelmek és kapacitásnövelés adatok például SAP HANA (eredményhalmazt) átviszi az alkalmazás rétegbe.
- Az SAP alkalmazásarchitektúra érzékenyebb a hálózati késés, mint a jellemző forgatókönyvek, ahol lekérdezi adatcsere a helyszíni és az Azure között.
- A virtuális hálózat átjáró rendelkezik, legalább két ExpressRoute-kapcsolatot, és mindkét kapcsolatok osztja meg a virtuális hálózat átjáró bejövő adatok maximális sávszélessége.

Lehet, hogy a tapasztalt Azure virtuális gépek és HANA nagy példány egységek közötti hálózati késés nagyobb, mint egy tipikus VM-VM hálózati oda-vissza késés. Az Azure-régió függ, mért értékek lépheti túl a 0,7 körbejárási késése (ms) átlaga alá besorolt [SAP Megjegyzés #1100926 - gyakran ismételt kérdések: hálózati teljesítményt](https://launchpad.support.sap.com/#/notes/1100926/E). Az ügyfelek azonban SAP HANA-alapú üzemi SAP alkalmazások nagyon meg SAP HANA nagy példányok telepítését. Az ügyfelek, akik telepítették az SAP-alkalmazásokból futó HANA nagy példány egységek használatával SAP HANA jelentése nagy fejlesztései. Mindazonáltal tesztelni kell az üzleti folyamatok alaposan Azure HANA nagy példányát.
 
Így biztosíthatja az Azure virtuális gépek és HANA nagy példány között determinisztikus hálózati késés, választott Azure VNet átjáró-Termékváltozat nem lényeges. Ellentétben a helyszíni és az Azure virtuális gépek közötti forgalmat Azure virtuális gépek és nagy HANA-példányok közötti forgalom minta fejleszthet az átvitelre váró kérések- és adatkötetek kicsi, de nagy felszakadásáig. Ahhoz, hogy az ilyen felszakadásáig kezelt is rendelkezik, az átjáró-Termékváltozat UltraPerformance használatát erősen ajánlott. A típus II osztály HANA nagy példány SKU a UltraPerformance gateway SKU Azure VNet átjáróként használatát megadása kötelező.  

> [!IMPORTANT] 
> A teljes hálózati forgalom az SAP-alkalmazások és adatbázis rétegek között megadott, csak a HighPerformance vagy UltraPerformance gateway SKU-n tartozó Vnetek esetében támogatott SAP HANA Azure (nagy példányok) való kapcsolódáshoz. Csak a UltraPerformance átjáró-Termékváltozat típus II termékváltozatok példány HANA nagy támogatott Azure VNet átjáróként.



### <a name="single-sap-system"></a>Egyetlen SAP rendszer

A fent látható a helyszíni infrastruktúra az Azure ExpressRoute keresztül csatlakozik, és az ExpressRoute-kapcsolatcsoport összekapcsolja azokat a Microsoft vállalati peremhálózati útválasztó (MSEE) (lásd: [ExpressRoute műszaki áttekintés](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Miután létrejött, adott útvonal csatlakozik a Microsoft Azure gerincét és, minden Azure-régió.

> [!NOTE] 
> Az Azure-beli SAP tájak, csatlakozzon az Azure-régió, a SAP tükrében megfigyelhető legközelebbi MSEE. Az Azure nagy példány bélyegzők dedikált MSEE eszközök az Azure infrastruktúra-szolgáltatási és nagy példány bélyegzők Azure virtuális gépek közötti hálózati késés csökkentése érdekében érdemes keresztül csatlakoznak.

Az Azure virtuális gépeken, SAP alkalmazáspéldányok, üzemeltető virtuális hálózat átjáróján adott ExpressRoute-kapcsolatcsoportot csatlakozik, és ugyanazt a virtuális hálózatot csatlakozik egy külön MSEE útválasztó átjárófelügyeleti feladatokat lát el nagy példány bélyegzők csatlakozik.

Ez az egyetlen SAP rendszer, ahol az SAP alkalmazásréteg van az Azure-ban és az SAP HANA Azure (nagy példányok) fut az SAP HANA-adatbázisból egy egyszerű példa. A rendszer azt feltételezi, hogy a virtuális hálózat átjáró sávszélesség 2 GB/s-vagy 10 GB/s átviteli sebesség nem felel meg a szűk keresztmetszetek.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP rendszerek vagy nagy számú SAP-rendszerek

Ha több SAP rendszerek vagy nagy számú SAP-rendszerek van telepítve, kapcsolódik az Azure (nagy példányokat), akkor &#39; SAP HANA s, nagy valószínűséggel tegyük fel, az átviteli sebessége a VNet átjáró válhat a szűk keresztmetszetek. Ebben az esetben az alkalmazás rétegek felosztása több Azure Vnetekhez kell. Azt is lehet létrehozni olyan esetekben, például a nagy példányok HANA csatlakozó különleges Vnetek recommendable:

- Biztonsági másolatok HANA nagy példányok HANA példányokból egy virtuális géphez, amelyen az NFS-megosztásokat az Azure-ban
- Másolás nagy biztonsági másolatait és egyéb fájlok HANA nagy példány egységekből az Azure-ban kezelt lemezterület.

Használja a külön Vnetek, hogy a gazdagépen virtuális gépeket, hogy a tárterület kezelése elkerülhetők a nagy méretű fájlok hatás vagy adatátvitel HANA nagy példányokból az Azure-bA a VNet-átjárón, amely a virtuális gépeket futtató az SAP alkalmazásréteg szolgál. 

Több méretezhető hálózati architektúra:

- Használja ki az egyetlen, nagyobb SAP alkalmazásréteg több Azure Vnet.
- Központi telepítése egy külön Azure virtuális hálózatot az egyes SAP rendszert telepíteni, ezeket külön alhálózatokon ugyanazt a virtuális hálózatot az SAP rendszerek kombinálásával képest.

 A több méretezhető hálózati architektúra SAP Hana Azure (nagy példány):

![SAP alkalmazásréteg telepítését a több Azure Vnetekhez keresztül](./media/hana-overview-architecture/image4-networking-architecture.png)

Az SAP alkalmazásréteg vagy összetevők telepítését a fent látható több Azure Vnetekhez keresztül bevezetett elkerülhetetlen késés terhelést, hogy az adott Azure Vnetekhez üzemeltetett alkalmazások közötti kommunikáció során történt. Alapértelmezés szerint a más Vnetekről útvonal a MSEE útválasztók ebben a konfigurációban található, Azure virtuális gépek közötti hálózati forgalmat. Azonban óta 2016 szeptemberétől a útválasztási is lehet optimalizálni. Optimalizálás, és kevesebb két Vnetek közötti kommunikáció késését a úgy, hogy társviszony-létesítési Azure Vnetekhez belül ugyanabban a régióban. Akkor is, ha ezek Vnetekhez különböző előfizetésekhez. A társviszony-létesítés Azure VNet, két különböző Azure Vnetekhez a virtuális gépek közötti kommunikáció az Azure-hálózat gerincét történő használatával közvetlenül kommunikálhatnak egymással. Hasonló késéssel ezáltal jelenít meg, mintha a virtuális gépek ugyanazon virtuális lenne. Mivel, IP-címtartományokat, amelyek az Azure virtuális hálózat átjárón keresztül csatlakoznak címzéshez továbbítódik a vnet az egyedi hálózatok átjárón keresztül. Kaphat Azure virtuális hálózat adatait a cikkben társviszony [Vnetben társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Az Azure-Útválasztás

Három dolgot fontos hálózati útválasztási SAP Hana Azure (nagy példány):

1. Az Azure (nagy példányok) SAP HANA csak elérhető Azure virtuális gépeken, valamint dedikált ExpressRoute-kapcsolat; nem közvetlenül a helyszíni. Közvetlen hozzáférés a helyszíni HANA nagy példány egységnél, Microsoft által kiadott nincs lehetőség közvetlenül a jelenlegi Azure hálózati architektúra SAP HANA-nagy példányok használt átmeneti útválasztási korlátozások miatt. Egyes felügyeleti ügyfeleket és közvetlen hozzáférést, például SAP megoldás Manager fut a helyszíni igénylő alkalmazások nem tud kapcsolódni az SAP HANA-adatbázisból.

2. Ha nagy példány HANA egységek két különböző Azure-régiók katasztrófa utáni helyreállítás céljából telepítve van, az azonos átmeneti útválasztási korlátozások érvényesek. Vagy más szóval egy régióban (pl. Velünk nyugati) HANA nagy példány egység IP-címek nem továbbítja a telepített meg egy másik régióban (pl. amerikai keleti) HANA nagy példány egység. Ez az az Azure-hálózat társviszony-létesítés régiók között, vagy az ExpressRoute-Kapcsolatcsoportok HANA nagy példány egységek Azure Vnet-hez csatlakozó közötti használatát független. Ahogy azt egy kicsit tovább le ezt a dokumentációt. Ezt a korlátozást, előre telepített architektúrával, vész-helyreállítási funkcióinak az azonnali használatát, HANA replikációs összetevő letiltása.

3. SAP HANA Azure (nagy példányok) egységeken rendelkezik hozzárendelt IP-címnek a kiszolgáló IP-készlet címről benyújtott ügyfélként között meg (lásd: [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolatokat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részletekért).  Az IP-cím az Azure-előfizetések és az Azure Vnetekhez csatlakozó HANA (nagy példányok) Azure ExpressRoute keresztül érhető el. Az IP-cím kívül a kiszolgáló IP-készlet címtartománya közvetlenül a hardver egységhez van rendelve, és már nem NAT'ed, ez az eset az első telepítések esetén az ilyen típusú megoldásra. 

> [!NOTE] 
> A korlátozásnak átmeneti útválasztási kapcsolatos, az első két listaelemek fenti leírtak szerint van szüksége, ha meg szeretné használni, további összetevők irányításához. A szoftverkorlátozó megoldásához használható összetevők lehetnek: útvonal adatai, és A fordított-proxyként. Ha például F5 BIG-IP, NGINX a Traffic Managerrel virtuális tűzfal/forgalom útválasztási megoldásként Azure szolgáltatásba telepített.
> Használatával [IPTables szabályok](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) egy Linux virtuális gépre, és különböző régiókban HANA nagy példány egységek között a helyi raktár és HANA nagy példány egység Útválasztás engedélyezése.
> Vegye figyelembe, hogy végrehajtása és a harmadik féltől származó hálózati berendezések vagy IPTables egyéni megoldások támogatása nem a Microsoft biztosítja. Támogatási kell a használt komponens gyártójához vagy integráló meg kell adni. 

### <a name="internet-connectivity-of-hana-large-instances"></a>Internetkapcsolat nagy HANA-példányok
Nagy HANA-példányok nem rendelkezik közvetlen internetkapcsolattal. Ez korlátozza a képességek rögzítéséhez, például az operációsrendszer-lemezképek közvetlenül az operációs rendszer szállítójához. Ezért előfordulhat, hogy szüksége SLES SMT helyi kiszolgáló vagy az RHEL előfizetés Manager

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Azure virtuális gépek és nagy HANA-példányok közötti adattitkosítás
Nagy HANA-példányok és az Azure virtuális gépek között továbbított adatok nincsenek titkosítva. Azonban csak az exchange között a HANA adatbázis-kezelő és az JDBC-/ ODBC-alapú alkalmazások engedélyezheti forgalom titkosítása. Hivatkozás [ebben a dokumentációban SAP által](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Több régióba HANA nagy példány egységek használatával

Lehetséges, hogy okokat, amelyek központi telepítése az Azure (nagy példányok) SAP HANA a több Azure-régiók vész-helyreállítási mellett. Lehet, hogy az elérni kívánt HANA nagy példányok az egyes régiókban különböző Vnetek a telepített virtuális gépek. A különböző HANA nagy példányok egységek hozzárendelt IP-címeket a rendszer nem propagálja meghaladja az Azure Vnetekhez (csatlakozó közvetlenül a példányok az átjárón keresztül), mert a virtuális hálózat terv fent bevezetett enyhe változás áll: egy Azure virtuális hálózatot átjáró kezelni tud a négy különböző ExpressRoute-Kapcsolatcsoportok különböző MSEEs kívül, és minden virtuális hálózat, amely egy nagy példány stampek kapcsolódik egy másik Azure-régió, a nagy példány stamp lehet csatlakoztatni.

![Az Azure Vnetekhez csatlakozik a különböző Azure-régiók nagy példány Azure bélyegzők](./media/hana-overview-architecture/image8-multiple-regions.png)

A fenti ábrán bemutatja, hogyan csatlakozik a különböző Azure Vnetekhez mindkét régiókban két különböző ExpressRoute-Kapcsolatcsoportok SAP HANA Azure (nagy példányok) való kapcsolódáshoz használt mindkét Azure régióban. Az újonnan bevezetett kapcsolatok a téglalap alakú piros sorokat. Ezeket a kapcsolatokat, az Azure Vnetekhez kívül egy adott Vnetek futó virtuális gépeket érhetik el a különböző HANA nagy példányok egységek, a két régió telepített minden egyes. Ahogyan a képen látható, a fenti grafikus, feltételezzük, hogy rendelkezik-e a helyszíni az két ExpressRoute-kapcsolat a két Azure-régiók; Vész-helyreállítási okokból ajánlott.

> [!IMPORTANT] 
> Több ExpressRoute-Kapcsolatcsoportok használata esetén AS elérési fertőző és a helyi beállításokat szabályozó BGP-beállítások használatával győződjön meg arról, a forgalom megfelelő útválasztását.


