---
title: Áttekintés és SAP HANA Azure (nagy példányok) architektúrájának |} Microsoft Docs
description: Architektúra áttekintése, amelyekkel telepítheti az SAP HANA Azure (nagy példány).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ceeec6991aaac64211301313c1bb8dc5f5faa1c0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA (nagy példányok) – áttekintés és az Azure-architektúra

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az Azure (nagy példányok) SAP HANA?

SAP HANA Azure (nagy példányok) egy olyan egyedi megoldás az Azure-bA. Biztosít a virtuális gépek üzembe helyezéséhez és futtatásához az SAP HANA, Azure lehetőséget biztosít, futtatása és az SAP HANA Önnek dedikált operációs rendszer nélküli a kiszolgálókra telepített lehetőségét. Az SAP HANA Azure (nagy példányok) megoldás nem megosztott gazdakiszolgáló/operációs rendszer nélküli hardver Önhöz rendelt építkezik. A Kiszolgálóhardver nagyobb számítási és a kiszolgáló, a hálózat és tároló-infrastruktúra tartalmazó bélyegzők van beágyazva. Kombinációját, mint a hitelesített igazított HANA data center integrációs (TDI). Az Azure (nagy példányok) SAP HANA kínál különböző kiszolgálótermékek vagy mérete. Egységek 72 processzorok és 768 GB memóriával rendelkeznek, és lépjen 960 rendelkező egységek processzorok és 20 TB memóriával.

Az ügyfél elkülönítési az infrastruktúra stamp belül történik bérlők, amelyek a következőhöz hasonló:

- **Hálózati**: elkülönítési ügyfelek belül infrastruktúra verem hozzárendelt ügyfél bérlőnként virtuális hálózatokon keresztül. A bérlő egyetlen ügyfél hozzá van rendelve. Egy ügyfél rendelkezhet több bérlő. A hálózati elkülönítés a bérlő hálózati kommunikáció a infrastruktúra stamp szinten bérlők között nem engedélyezi, akkor is, ha a bérlők számára az azonos ügyfél tartozik.
- **Tárolási összetevőinek**: tároló virtuális gépek, amelyeknek a hozzájuk rendelt tárolási kötetein elkülönítését. Tárolási köteteket rendelhet egy tárolási virtuális gépet. Egy tároló virtuális géphez van hozzárendelve, kizárólag a SAP HANA TDI hitelesített infrastruktúra verem egy egyetlen bérlő számára. Ennek eredményeképpen a tároló virtuális géphez rendelt tárolókötetek érhetők el egy adott és kapcsolódó bérlői csak. Nem látható a különböző telepített bérlők között.
- **Kiszolgáló vagy a gazdagép**: A kiszolgáló vagy a gazdagép egység nem osztott ügyfelei vagy a bérlők között. A kiszolgáló vagy a gazdagép egy ügyfél telepítve egy atomi operációs rendszer nélküli számítási egység, amely egy egybérlős hozzá van rendelve. *Nem* hardver particionálás vagy enyhe particionálást használnak, amelyek eredményezhet a gazdagépen vagy egy kiszolgáló egy másik ügyféllel oszt meg. Tárolás az adott bérlő tároló virtuális géphez rendelt csatlakoztatva vannak ilyen kiszolgálóra. A bérlő rendelkezhetnek kizárólag hozzárendelt különböző Termékváltozatai sok kiszolgáló egységeire.
- Egy SAP HANA (nagy példányok) Azure infrastruktúra stamp, belül számos különböző bérlők telepített és egymással szembeni keresztül a bérlői fogalmak a hálózati, tárolási és számítási szint elkülönített. 


A kiszolgáló operációs rendszer nélküli egységeket SAP HANA csak futtatásához támogatottak. Az SAP alkalmazásréteg vagy a munkaterhelés közel-vő réteg fut a virtuális gépeken. Az SAP HANA futó Azure (nagy példányok) egységek infrastruktúra bélyegzők a Azure hálózati szolgáltatások csigolyákat csatlakoznak. Ezzel a módszerrel kis késleltetésű kapcsolat SAP HANA Azure (nagy példányok) egységekről és a virtuális gépek közötti valósul meg.

Ez a dokumentum több dokumentumok, a SAP HANA Azure (nagy példányok) egyike. Ez a dokumentum bemutatja a alapvető architektúráját, feladatok és a megoldás által nyújtott szolgáltatások. A megoldás magas szintű képességeket is ismerteti. A legtöbb más területek, például a hálózati és a csatlakozási négy más dokumentumok közé tartozik a részletek és információkat. Az SAP HANA (nagy példányok) Azure dokumentációjában nem fedi le, a SAP NetWeaver telepítési szempontok vagy SAP NetWeaver a virtuális gépek központi telepítését. SAP NetWeaver Azure ugyanabban az Azure dokumentációja a tárolóban található külön dokumentumokra vonatkozik. 


A különböző dokumentumok HANA nagy példány útmutatás terjed ki a következő területeken:

- [SAP HANA (nagy példányok) – áttekintés és az Azure-architektúra](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Telepítse és konfigurálja az SAP HANA (nagy példány) az Azure-on](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási az Azure-on](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) hibaelhárítási és Azure figyelése](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Állítsa be a SUSE a STONITH használatával magas rendelkezésre állás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Az operációs rendszer biztonsági mentéséhez és visszaállításához a típus II termékváltozatok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Meghatározások

Több, közös definíciók széles körben használt a architektúra és a műszaki üzembe helyezési útmutatójában. Vegye figyelembe a következő feltételek és azok jelentését:

- **Infrastruktúra-szolgáltatási**: szolgáltatott infrastruktúra.
- **A PaaS**: Platformszolgáltatást.
- **SaaS**: szolgáltatott szoftver.
- **SAP összetevő**: az egyes SAP-alkalmazások, például ERP központi összetevő (ECC), a Business Warehouse (BW), a megoldás Manager vagy a vállalati portálon (EP). SAP-összetevők a hagyományos ABAP vagy Java technológiák vagy egy nem NetWeaver alapú alkalmazás, például üzleti objektumok is alapulhat.
- **SAP környezet**: egy vagy több SAP összetevők logikusan egy üzleti funkció, például a fejlesztés, minőségi megbízhatósági, képzési, vész-helyreállítási vagy éles.
- **SAP fekvő**: az informatikai fekvő az egész SAP eszközök hivatkozik. Az SAP fekvő minden üzemi és nem éles környezetben tartalmaz.
- **SAP rendszer**: adatbázis-kezelő réteg és alkalmazás réteget, például egy SAP ERP fejlesztői rendszernek, az SAP BW teszt rendszer és az SAP CRM éles rendszerek kombinációja. Azure-környezetekhez nem támogatják a két réteg a helyszíni és az Azure közötti felosztásával. Egy SAP rendszer telepített helyszíni vagy az Azure szolgáltatásba telepített. Egy Azure vagy a helyszíni SAP fekvő a különböző rendszerek telepíthetők. Például telepítheti az SAP CRM fejlesztési és tesztrendszerek az Azure-ban, a SAP CRM éles rendszer helyszíni telepítése közben. SAP Hana (nagy példányok) Azure-on célja az SAP alkalmazásréteg SAP rendszerek a virtuális gépek és a kapcsolódó SAP HANA-példányhoz egy egység az Azure (nagy példányok) stamp az SAP HANA üzemeltetésére.
- **Nagy példány stamp**: hardver infrastruktúra verem egy SAP HANA TDI hitelesített és dedikált SAP HANA-példányok Azure-ban futtatni.
- **SAP HANA Azure (nagy példány):** , amely telepítve van a különböző Azure-régiók nagy példány bélyegzők SAP HANA TDI hitelesített hardveren példánya HANA futtassa az Azure-ajánlatot hivatalos nevét. A kapcsolódó kifejezés *HANA nagy példány* rövid a *SAP HANA Azure (nagy példányok)* és széles körben használja a műszaki telepítési útmutatóban.
- **Létesítmények közötti**: egy olyan forgatókönyvet, ahol telepített virtuális gépek Azure-előfizetéssel, amely rendelkezik pont-pont, többhelyes vagy Azure ExpressRoute-kapcsolatot a helyszíni adatközpont és az Azure közötti ismertet. Közös Azure dokumentációja, az ilyen típusú központi telepítések egyaránt létesítmények közötti forgatókönyv leírása. A kapcsolat oka, hogy a helyi tartomány, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS kiterjeszti Azure. A helyszíni fekvő az időtartam, az Azure-előfizetések az Azure eszközökre. Ez a bővítmény a virtuális gépek a helyi tartomány része lehet. 

   Tartományi felhasználók a helyi tartomány fér hozzá a kiszolgálóhoz, és szolgáltatásokat futtatnak a virtuális gépek (például adatbázis-kezelő szolgáltatás). Virtuális gépek közötti kommunikációt és a névfeloldás telepítve a helyi és virtuális gépek Azure-telepített lehet. Ebben a forgatókönyvben jellemző, amelyben a legtöbb SAP eszközök vannak telepítve módja. További információkért lásd: [megtervezése és kialakítása az Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [webhelyek kapcsolattal rendelkező virtuális hálózat létrehozása az Azure portál használatával](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Bérlői**: HANA nagy példány stamp telepített ügyfél lekérdezi elkülönített be egy *bérlő.* A bérlő elkülönül a hálózati, tárolási és számítási rétegét a többi bérlőtől. A különböző bérlők rendelt tárolási és számítási egység nem tekintse meg egymással, vagy a HANA nagy példány stamp szinten kommunikálnak egymással. Az ügyfél beállíthatja úgy a különböző bérlők történő központi telepítések. Nincs még akkor is ezt követően a HANA nagy stamp példányszintű a bérlők közötti kommunikáció.
- **Termékváltozat-kategória**: A HANA nagy példány, a következő két kategóriájához SKU érhető el:
    - **I. osztály típus**: S72, S72m, S144, S144m, S192 és S192m
    - **Írja be a II osztály**: S384, S384m, S384xm, S576, S768 és S960


A különböző további erőforrások állnak központi telepítése egy SAP munkaterhelés, a felhőben. Ha azt tervezi, egy SAP HANA központi telepítés végrehajtása az Azure-ban, kell lennie a jártasak és az Azure infrastruktúra-szolgáltatási és a központi telepítés SAP-munkaterhelések az Azure infrastruktúra-szolgáltatási tudomást. A folytatás előtt tekintse meg a [használata SAP megoldások Azure virtuális gépeken futó](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) további információt. 

## <a name="certification"></a>Tanúsítvány

A NetWeaver hitelesítő mellett SAP egy SAP Hana SAP HANA támogatásához egyes infrastrukturális, például az Azure infrastruktúra-szolgáltatási speciális hitelesítő van szükség.

A Core SAP Megjegyzés NetWeaver, valamint egy mértékben SAP HANA hitelesítő [SAP Megjegyzés #1928533 – Azure SAP-alkalmazásokból: támogatott termékek és Azure Virtuálisgép-típusokon](https://launchpad.support.sap.com/#/notes/1928533).

A [SAP Megjegyzés #2316233 - SAP HANA a Microsoft Azure (nagy példányok)](https://launchpad.support.sap.com/#/notes/2316233/E) is fontos. A megoldás a jelen útmutatóban ismertetett magában foglalja. Ezenkívül támogatottak az Azure GS5 VM típusú SAP HANA futtatásához. Ebben az esetben információt teszi közzé a [az SAP-webhely](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

Az SAP HANA SAP Megjegyzés #2316233 említett Azure (nagy példányok) megoldás teszi lehetővé Microsoft és az SAP-ügyfelek központi telepítése a nagy SAP Business Suite, SAP BW Programhoz, S/4 HANA, BW/4HANA vagy más SAP HANA-munkaterhelések az Azure-ban. A megoldás az SAP-HANA jóváhagyott dedikált hardver stamp alapul ([SAP HANA igazított center Adatintegráció – TDI](https://scn.sap.com/docs/DOC-63140)). Futtatja egy SAP HANA TDI beállított megoldás, ha a hardver infrastruktúra minden SAP HANA-alapú alkalmazások (például SAP Business Suite az SAP HANA, SAP BW SAP HANA, S4/HANA és BW4/HANA) működik.

SAP HANA-beli virtuális gépek képest, ez a megoldás előnye van. Sokkal nagyobb memória kötetek biztosít. Ahhoz, hogy ez a megoldás, kell ismertetése a következő fő szempontjait:

- Az SAP alkalmazás réteg és nem SAP alkalmazások futnak a szokásos Azure hardverkonfiguráción bélyegek tárolt virtuális gépek.
- Ügyfél a helyszíni infrastruktúrát, adatközpontok, és alkalmazások központi telepítésének a felhő platform keresztül az ExpressRoute (ajánlott) vagy virtuális magánhálózati (VPN) csatlakozik. Active Directory és a DNS is kiterjeszthetőek az Azure.
- Az SAP HANA-adatbázispéldány HANA munkaterhelés SAP HANA (nagy példányok) Azure-on futtatja. A nagy példány stamp be Azure networking csatlakozik, ezért a virtuális gépeken futó szoftver képes interakciót folytatni a HANA-példány futtatási nagy HANA-példányban.
- Hardver-vagy Azure (nagy példányok) SAP HANA egy infrastruktúra-szolgáltatási SUSE Linux Enterprise Server vagy a Red Hat Enterprise Linux előtelepített megadott dedikált hardvert. Csakúgy, mint a virtuális gépek további frissítések és az operációs rendszer karbantartási feladata a.
- HANA vagy bármely SAP HANA egységek HANA nagy példány futtatásához szükséges további összetevők telepítését a feladata. Minden megfelelő folyamatban lévő műveletek és az SAP HANA Azure felügyeleti megtalálhatók a felelős.
- Az itt leírt megoldásokkal, mellett más összetevők is telepíthető az Azure-előfizetéssel, amely kapcsolódik az Azure (nagy példányok) SAP HANA. Példák, amelyek lehetővé teszik a kommunikációt, vagy közvetlenül az SAP HANA összetevők, például a jump-kiszolgálók adatbázis-RDP kiszolgálók, SAP HANA Studio, SAP Data Services SAP BI forgatókönyvek esetén, vagy hálózati figyelési megoldások.
- Azure, mint a HANA nagy példány magas rendelkezésre állású és vész-helyreállítási funkció támogatást nyújt.

## <a name="architecture"></a>Architektúra

Magas szinten a SAP HANA Azure (nagy példányok) megoldás rendelkezik az SAP alkalmazásréteg elhelyezkedő virtuális gépeken. Az adatbázis réteg azonos Azure-régiót Azure IaaS csatlakozik egy nagy példány stamp található SAP TDI konfigurált hardveren helyezkedik el.

> [!NOTE]
> Telepítse az SAP alkalmazásréteg SAP DBMS rétegként Azure ugyanabban a régióban. Ez a szabály megfelelően legyen dokumentálva Azure SAP munkaterhelések közzétett adatait. 

SAP HANA (nagy példányok) Azure-architektúra általános egy SAP TDI hitelesített hardverkonfiguráció, amely a nem virtualizált, operációs rendszer nélküli, nagy teljesítményű kiszolgáló SAP HANA-adatbázisból. Skála erőforrásokra vonatkozó igényeinek az SAP alkalmazásréteg Azure rugalmasságát és képessége is tartalmazza.

![Az SAP HANA (nagy példányok) Azure-architektúra áttekintése](./media/hana-overview-architecture/image1-architecture.png)

Az architektúra látható három részből áll:

- **Jobb**: egy a helyszíni infrastruktúra, amely adatokat a különböző alkalmazások futnak, hogy a végfelhasználók elérhessék a LOB-alkalmazások, például SAP adatközpontok jeleníti meg. Ideális esetben ez a helyszíni infrastruktúra kapcsolódik az Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: Azure IaaS jeleníti meg, és ebben az esetben használata virtuális gépek SAP vagy más SAP HANA adatbázis-kezelő rendszert használó alkalmazások futtatásához. Virtuális gépek és a memória által a virtuális gépek kisebb HANA-példányok az alkalmazási rétegre együtt vannak telepítve. További információ a virtuális gépek: [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/).

   Az Azure hálózati szolgáltatások SAP rendszerek más alkalmazások virtuális hálózatba együtt használhatók. Ezek a virtuális hálózatok csatlakozni a helyszíni rendszerekben SAP HANA Azure (nagy példány).

   SAP NetWeaver alkalmazások és a támogatott Azure rendszerben való futtatásra adatbázisok [SAP támogatási Megjegyzés #1928533 – Azure SAP-alkalmazásokból: támogatott termékek és Azure Virtuálisgép-típusokon](https://launchpad.support.sap.com/#/notes/1928533). Az SAP-megoldások Azure telepítéséről dokumentációjáért lásd:

  -  [SAP használja a Windows virtuális gépek](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [SAP megoldások használata az Azure virtuális gépeken](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Balra**: az SAP HANA TDI hitelesített összes hardvere látható az Azure nagy példány bélyegző. A HANA nagy példány egységek ugyanazt a technológiát a helyi kapcsolat az Azure segítségével csatlakoznak az előfizetés virtuális hálózatok.

Az Azure nagy példány stamp maga egyesíti a következő összetevőket:

- **A számítástechnikai**: Intel Xeon E7-8890v3 vagy Intel Xeon E7-8890v4 processzorok szükséges számítási képességet biztosít, amelyek hitelesített SAP HANA alapuló kiszolgálók.
- **Hálózati**: A nagy sebességű hálózati háló kapcsolja össze a számítási, tárolási és hálózati összetevők egységes.
- **Tárolási**: olyan tárolási infrastruktúra, amely egy egységes hálózati háló keresztül érhető el. A megadott tárolási kapacitás biztosított attól függ, hogy az adott SAP HANA Azure (nagy példányok) konfigurációban telepített. További tárolási kapacitás további havi költségén érhető el.

A több-bérlős infrastruktúrájában a nagy példány stamp az ügyfelek elkülönített bérlők üzemelnek. A bérlő központi telepítését, a nevezze el az Azure-előfizetés az Azure regisztrációs belül. Az Azure-előfizetés, amely a HANA nagy példányhoz lesz számlázva ellen. Ezek a bérlők rendelkezik az Azure-előfizetés 1:1 kapcsolattal. A hálózat telepítve egy bérlőt egy Azure-régiót a különböző virtuális hálózatokon, különböző Azure-előfizetések tartoznak a HANA nagy példány egység eléréséhez. Adott Azure-előfizetések az azonos Azure regisztrációs kell tartoznia. 

SAP HANA Azure (nagy példányok), mint a virtuális gépek, a több Azure-régiók kínálják. A vész helyreállítási lehetőségeket kínálnak, kiválaszthatja, elvégezni a beléptetést. Egy földrajzi politikai régión belül különböző nagy példány bélyegzők kapcsolódnak egymáshoz. Például a HANA nagy példány bélyegzők Velünk nyugati és amerikai keleti vész-helyreállítási replikációhoz a dedikált hálózati kapcsolaton keresztül csatlakoznak. 

Ugyanúgy, mint a virtuális gép különböző Azure virtuális gépek közül választhat, választhat másik termékváltozatok a HANA nagy példányt, amely különböző alkalmazások és szolgáltatások típusú SAP HANA is lefednek. SAP memória-processzor-szoftvercsatorna-arányt tesz lehetővé az Intel processzorral generációja alapján különböző munkaterhelések vonatkozik. Az alábbi táblázat a Termékváltozat-típusok érhető el.

Től július 2017 SAP HANA Azure (nagy példányok) érhető el az Azure-régiókban több konfiguráció Velünk nyugati amerikai keleti, Kelet-Ausztrália, Ausztrália délkeleti, Nyugat-Európa és Észak-Európa.

| SAP megoldás | CPU | Memory (Memória) | Tárolás | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| Az OLAP-re optimalizált: FF/4HANA, az SAP BW Programhoz<br /> vagy SAP HANA általános OLAP-munkaterhelés | Az Azure S72 SAP HANA<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> A Processzormagok 36 és 72 CPU szálak |  768 GB |  3 TB | Elérhető |
| --- | Az Azure S144 SAP HANA<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> A Processzormagok 72 és 144 CPU szálak |  1,5 TB |  6 TB | Nem érhető el többé |
| --- | Az Azure S192 SAP HANA<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 96 és 192 CPU szálak |  2.0 TB |  8 TB | Elérhető |
| --- | Az Azure S384 SAP HANA<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 192 és 384 CPU szálak |  4.0 TB |  16 TB | Elérhető |
| Az OLTP optimalizált: SAP Business csomag<br /> SAP HANA vagy az S/4HANA (OLTP)<br /> általános OLTP | Az Azure S72m SAP HANA<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> A Processzormagok 36 és 72 CPU szálak |  1,5 TB |  6 TB | Elérhető |
|---| Az Azure S144m SAP HANA<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> A Processzormagok 72 és 144 CPU szálak |  3.0 TB |  12 TB | Nem érhető el többé |
|---| Az Azure S192m SAP HANA<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 96 és 192 CPU szálak  |  4.0 TB |  16 TB | Elérhető |
|---| Az Azure S384m SAP HANA<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 192 és 384 CPU szálak |  6.0 TB |  18 TB | Elérhető |
|---| Az Azure S384xm SAP HANA<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 192 és 384 CPU szálak |  8.0 TB |  22 TB |  Elérhető |
|---| Az Azure S576 SAP HANA<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 288 és 576 CPU szálak |  12.0 TB |  28 TB | Elérhető |
|---| Az Azure S768 SAP HANA<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> A Processzormagok 384 és 768 CPU szálak |  16.0 TB |  36 TB | Elérhető |
|---| Az Azure S960 SAP HANA<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 Processzormagok és 960 CPU szálak |  20.0 TB |  46 TB | Elérhető |

- A Processzormagok = Processzormagok nem-többszálú a processzorok, a kiszolgáló egység összegére összege.
- CPU szálak CPU-magokat többszálú a processzorok, a kiszolgáló egység összegére által biztosított számítási szálak számának összege, azaz =. Minden egység alapértelmezés szerint a Hyper-Threading technológia használatára vannak konfigurálva.


A kiválasztott konfigurációkkal munkaterhelés, a Processzor-erőforrások és a kívánt memória függenek. A használatára a termékváltozatok optimalizált az OLAP-munkaterhelés OLTP-munkaterhelés lehetőség. 

A rendszer a az ajánlatok alapszintű hardver SAP HANA TDI hitelesített. Hardver két különböző osztályú osztani az SKU:

- S72, S72m, S144, S144m, S192 és S192m, amely a "Type i. osztály" nevezik SKU.
- S384, S384m, S384xm, S576, S768 és S960, amely nevezzük az "Típus II osztály" SKU.

Egy teljes HANA nagy példány stamp kizárólag az egyetlen ügyfél nem lefoglalt&#39;s használja. Ezt a tényt a számítási és tárolási erőforrásokat egy Azure szolgáltatásba telepített, valamint hálózati háló összekapcsolt rackszekrények vonatkozik. HANA nagy példány infrastruktúra, például Azure, telepíti a különböző ügyfél &quot;bérlők&quot; , amelyek elkülönülnek egymástól a következő három szintje:

- **Hálózati**: a HANA nagy példány stamp virtuális hálózatok elkülönítését.
- **Tárolási**: tároló virtuális gépek, amelyek rendelt tárolási köteteket és elkülöníteni a bérlők közötti tárolókötetek elkülönítését.
- **Számítási**: hozzárendelés server egységek dedikált egyetlen bérlő számára. Nem merevlemez vagy világos particionálás kiszolgáló egységek. Egyetlen kiszolgáló vagy a gazdagép egység bérlők között nincs megosztás. 

A központi telepítések HANA nagy példány egységek különböző bérlők között nem láthatók, egymással. A különböző bérlők rendszerbe HANA nagy példány egység nem közvetlenül egymással kommunikálnak a HANA nagy példány stamp szinten. Egy bérlőt csak HANA nagy példány egységében HANA nagy példány stamp szint is kommunikálhatnak egymással.

A nagy példány stamp egy telepített tenant számlázási okokból van rendelve egy Azure-előfizetéssel. A hálózat akkor érhetők el más Azure-előfizetések belül az azonos Azure regisztrációs virtuális hálózatok. Ha telepít egy másik Azure-előfizetés azonos Azure-régióban, is választhat egy elkülönített HANA nagy példány bérlő kérni.

SAP HANA HANA nagy példányon és az Azure szolgáltatásba telepített virtuális gépeken futó SAP HANA futó között jelentős különbség van:

- Nincs nincs virtualizálási réteg SAP Hana Azure (nagy példány). Az alapul szolgáló operációs rendszer nélküli hardver teljesítményét kap.
- Azure-ban eltérően az SAP HANA Azure (nagy példányok) kiszolgálón adott ügyfélhez van kijelölve. Nincs, hogy a kiszolgáló egység vagy a gazdagép merevlemez vagy világos particionálva van lehetőség. Ennek eredményeképpen a HANA nagy példány egység használatos egész a bérlők és az, hogy az Ön számára kiosztani. Újraindítás vagy a kiszolgáló leállása nem okozhat, automatikusan az operációs rendszer és az SAP HANA üzembe helyezéséhez egy másik kiszolgálón. (Típus i. osztály SKU, az egyetlen kivétel, ha egy kiszolgáló észlel problémák és újratelepítés hajtható végre egy másik kiszolgálón.)
- Azure, ahol processzor állomástípusok választja ki a legjobb price/teljesítményesemények aránya, ellentétben a processzor típusok az SAP HANA Azure (nagy példányok) választott a következők az Intel E7v3 és E7v4 processzor sor legmagasabb hajt végre.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Több SAP HANA-példány futtatnak egy HANA nagy példány egység
Akkor lehet HANA nagy példány egységek több aktív SAP HANA példány futtatására. A storage-pillanatfelvételekkel és vész-helyreállítási képességek biztosítása érdekében az ilyen konfiguráció egy példányonként beállítása kötettel kell rendelkeznie. Jelenleg HANA nagy példány egység az alábbiak szerint lehet osztani:

- **S72, S72m, S144, S192**: lépésekben 256 GB, a legkisebb kezdési egység 256 GB. Különböző nagyobb, mint 256 GB és 512 GB az egység a memória maximális egyesíthetők.
- **S144m és S192m**: 256 GB legkisebb 512 GB-os lépésekben. Különböző nagyobb, mint 512 és 768 GB-os az egység a memória maximális egyesíthetők.
- **Írja be a II osztály**: 512 GB-ot, a legkisebb kezdési egységgel 2 TB-os lépésekben. Az egység a memória maximális össze lehet kombinálni különböző nagyobb, mint 512 GB, 1 TB-os és 1,5 TB.

Néhány példa a több SAP HANA-példányt futtató nézhet ki például a következő.

| SKU | Memória mérete | Tároló mérete | Több adatbázisból méretek |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA példány<br /> vagy példányt. 1 x 512 GB + 1 x 256 GB példány<br /> vagy 3 x 256 GB példányok | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA példányok<br />vagy példányt. 1 x 512 GB + 1 darab 1 TB-példány<br />vagy 6 x 256 GB példányok<br />vagy 1x1.5 TB-példány | 
| S192m | 4 TB | 16 TB | 8 x 512 GB példányok<br />vagy 4 x 1 TB-példányok<br />vagy 4 x 512 GB példányok + 2 darab 1 TB-példányok<br />vagy 4 x 768 GB példányok + 2 darab 512 GB példányok<br />vagy 1 darab 4 TB-példány |
| S384xm | 8 TB | 22 TB | 4 x 2 TB-példányok<br />vagy 2 darab 4 TB-példányok<br />vagy példányok 2 x 3 TB + 1 x 2 TB-példányok<br />vagy példányok 2x2.5 TB + 1 x 3 TB-példányok<br />vagy 1 x 8 TB-példány |


Más változatok is vannak. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA és a bővítmény adatdeduplikációval csomópontok használata
SAP rétegezési adatmodell SAP BW különböző SAP NetWeaver kiadásainak és az SAP BW/4HANA támogatja. Az adatmodell rétegezési kapcsolatos további információkért lásd: a SAP dokumentum [SAP BW/4HANA és az SAP HANA-bővítmény csomópontokkal HANA SAP BW](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
HANA nagy példánnyal, 1. lehetőség konfigurációt használhatja a SAP HANA-bővítmény csomópontok a gyakori kérdések és SAP blog dokumentumok leírtak szerint. 2. lehetőség konfigurációk beállíthatja a következő nagy HANA-példány termékváltozatok rendelkező: S72m, S192, S192m, S384 és S384m. 

Ha megnézi a dokumentációt, az előnyt látható nem feltétlenül közvetlenül. De ha megnézi a SAP méretezési irányelveket, előnyös beállítás-1 és 2. lehetőség SAP HANA-bővítmény csomópontok használatával megtekintheti. Az alábbiakban példákat:

- SAP HANA méretezési irányelvek elvégzéséhez adatmennyiség összeg memória. Az SAP HANA-példányt a gyakran használt adatokkal rendelkező futtatásakor csak 50 százalékban van, vagy kevesebb, a memória megtelt adatokkal. A fennmaradó memória ideális tárolt SAP Hana végez a műveletet.
- Ez azt jelenti, 2 TB memóriával, az SAP BW-adatbázisát futtató rendelkező HANA nagy példány S192 egységben csak akkor kell 1 TB-os adatok kötetként.
- 1. lehetőség további SAP HANA kiterjesztési csomópontnak használatakor is egy S192 HANA nagy példány SKU, biztosít egy további 2-TB kapacitást adatmennyiség. A 2. lehetőség konfigurációban egy további 4 TB-os meleg adatmennyiség beolvasása. A működés közbeni csomópont képest, a teljes memória kapacitás, a "meleg" bővítmény csomópont adatok tárolására, az 1. lehetőség használható. 2. lehetőség SAP HANA bővítmény csomópont-konfiguráció adatmennyiség használható dupla a memória.
- Végül 3 TB kapacitású az adatok és az 1:1. lehetőség 2 közbeni-az-meleg arány. Lehetősége van az adatok és a 2. lehetőség bővítmény konfigurálása 1:4 aránya 5 TB.

Minél nagyobb adatmennyiség képest a memória, annál magasabb a, hogy a meleg arra utasítja a tárolja a lemezegységen található.


## <a name="operations-model-and-responsibilities"></a>Operatív modell és felelősségek

A megadott SAP HANA Azure (nagy példányok) szolgáltatás Azure IaaS szolgáltatások igazodik. Kapott egy példányának HANA nagy SAP HANA optimalizált telepített operációs rendszert egy példányát. Mint az Azure IaaS virtuális gépeket, az operációs rendszer korlátozására, további szoftverek telepítése, HANA telepítése, az operációs rendszer és a HANA operációs és frissíteni az operációs rendszer és a HANA feladatainak többségét feladata a. A Microsoft nem kényszerítheti az operációs rendszer vagy a HANA frissítéseket meg.

![SAP HANA Azure (nagy példányok) feladatai](./media/hana-overview-architecture/image2-responsibilities.png)

Ahogy az ábrán is látható, az Azure (nagy példányok) SAP HANA egy több-bérlős IaaS ajánlat. Az esetek többségében felelős az osztálynak a jelenleg az operációs rendszer – infrastruktúra-határ. Microsoft felelős a sor az operációs rendszer alatt a szolgáltatás minden elemét. Ön felelősséggel tartozik a sorban fölött a szolgáltatás minden elemét. Az operációs rendszer a felelős. Továbbra is módszerekkel legfrissebb helyszíni alkalmaz előfordulhat, hogy a megfelelőségi, biztonság, alkalmazáskezelés, alapján és az operációs rendszer felügyeleti. A rendszer jelennek meg, ha a hálózat összes tanúsítványinformációit vannak.

Ez a szolgáltatás SAP HANA van optimalizálva, így területet kell együttműködés a Microsofttal az alapul szolgáló infrastruktúra funkcióinak használatát a legjobb eredmények elérése érdekében.

Az alábbi lista részletesen ismerteti a Rétegek és az Ön feladatkörei:

**Hálózati**: a belső hálózatok az SAP HANA futtató nagy példány stampet konfiguráljon. A felhasználó felelőssége tartoznak a tárolási, a példányok (a kibővített és egyéb funkciók), a kapcsolat a fekvő és a kapcsolatot az Azure-bA az SAP alkalmazásréteg ahol tárolása közötti kapcsolatot a virtuális gépek. Tartalmazza az Azure-adatközpontokban vész-helyreállítási célokra replikációhoz WAN összekapcsolását is. Minden hálózatok a bérlő által particionáltak, és alkalmazza a szolgáltatás-minőségi.

**Tárolási**: A virtualizált particionálva az összes kötet az SAP HANA-kiszolgálók által igényelt, valamint a pillanatképek tárolási. 

**Kiszolgálók**: A dedikált fizikai kiszolgálók a bérlők rendelt SAP HANA-adatbázisok futtatásához. A kiszolgálók típusú i. osztály SKU hardver azért. Az ilyen kiszolgálók a kiszolgáló konfigurációs gyűjti és őrzi meg a profilok, amelyek egy másik fizikai hardver egy fizikai hardverek helyezheti át. Ilyen (manuális) áthelyezésre profil műveletek hasonlítható kicsit Azure szolgáltatásjavításnak. A típus II osztály SKU kiszolgálók ilyen funkció nem teszik lehetővé.

**SDDC**: szoftveralapú entitásként adatközpontok a szoftver, amely adatok kezelésére szolgál. Microsoft lehetővé teszi a készlet erőforrásokhoz, a méretezés, a rendelkezésre állás és a teljesítmény szempontjából.

**Rendszer**: (SUSE Linux vagy a Red Hat Linux) választja, az operációs rendszer futtató kiszolgálókon. Az operációs rendszer lemezképeit rel biztosított volt gyártója által biztosított az egyes Linux Microsoft SAP HANA futtatásához. A Linux gyártójával konkrét SAP HANA-re optimalizált kép előfizetéssel kell rendelkeznie. Ön felelősséggel tartozik a képek regisztrálása az operációs rendszer szállítójához. 

A Microsoft által átadási pontról való telepítésért felelős semmilyen további javítását a Linux operációs rendszer. A javítás tartalmaz, amely szükséges a sikeres SAP HANA-telepítés lehet, és nem érhetők az SAP HANA adott Linux szállító további csomagokat optimalizált operációsrendszer-lemezképek. (További információkért lásd: a SAP HANA-dokumentáció és SAP megjegyzések.) 

Ön felelőssége lesz az operációs rendszer meghibásodása vagy az operációs rendszer optimalizálása és viszonyítva az adott kiszolgálói hardver illesztőprogramja javítását. Akkor is felelősek biztonsági vagy az operációs rendszer működési javítását. 

A feladata a figyelés és a kapacitástervezés is tartalmazza:

- CPU hálózatierőforrás-fogyasztás.
- Memória-felhasználás.
- Lemezkötet szabad terület, az iops-érték és a késleltetés kapcsolódik.
- Kötet közötti hálózati forgalom nagy HANA-példány és az SAP alkalmazásréteg.

Az alkalmazás mögötti infrastruktúra HANA nagy példányának biztonsági mentése és visszaállítása a rendszerkötet a funkciókat biztosít. Ez a funkció használata is a felelős.

**Köztes**: az SAP HANA-példány, elsősorban. Felügyeleti műveletek és figyelési az Ön felelőssége. A megadott funkció segítségével storage-pillanatfelvételekkel biztonsági mentési és visszaállítási és vész helyreállítási célokra használja. Ezek a képességek a infrastruktúra által biztosított. Az Ön feladatkörei is magas rendelkezésre állású és vész-helyreállítási ezekkel a lehetőségekkel hasznosítja ki őket, és figyelési annak meghatározásához, hogy a storage-pillanatfelvételekkel sikeresen végrehajtva tervezése.

**Adatok**: az SAP HANA által kezelt adatok, és más adatok, például biztonsági másolatok köteteken található fájlok vagy a fájl megosztja. Szabad lemezterület megfigyelését és felügyeletét a tartalmat azokon a köteteken közé tartoznak az Ön feladatkörei Akkor is sikeres kötetek biztonsági mentése lemezre és storage-pillanatfelvételekkel felügyeletéért felelős. Microsoft feladata a vész-helyreállítási helyeken irányuló adatreplikációt sikeres végrehajtását.

**Alkalmazások:** az SAP alkalmazáspéldányok, illetve nem SAP-alkalmazásokból, ezeknek az alkalmazásoknak az alkalmazási rétegre. Központi telepítés, a felügyeletet, a műveletek és a ezeknek az alkalmazásoknak megfigyelést közé tartoznak az Ön feladatkörei Ön felelősséggel tartozik a kapacitástervezés CPU erőforrás-felhasználás, memória-felhasználás, Azure-tároló fogyasztása és hálózati sávszélességet a virtuális hálózatokon belül. Akkor is felelősek kapacitásának tervezése az erőforrás-felhasználás a virtuális hálózatok az SAP HANA Azure (nagy példány).

**WAN**: A kapcsolatok létrehozása a helyszíni munkaterhelések Azure-környezetekhez. Összes ügyfél számára, ha nagy példánya HANA Azure ExpressRoute kapcsolatot használjon. Ez a kapcsolat nem Azure (nagy példányok) megoldás a SAP HANA része. Ön felelőssége lesz a kapcsolat beállítása.

**Archív**: Előfordulhat, hogy inkább archivált adatok másolatát a saját metódusokkal storage-fiókok. Archiválás szükséges felügyeleti, a megfelelőségi, a költségek és a műveletek. Ön felelősséggel tartozik előállítása archív példányszám és a biztonsági mentéseket a Azure és a megfelelő módon tárolja őket.

Tekintse meg a [SAP Hana (nagy példányok) Azure SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Méretezése

Méretezési HANA nagy példány eltér nem Hana általában méretezése. Meglévő és áthelyezése más RDBMS HANA, SAP biztosít számos olyan jelentést, a meglévő SAP rendszereken futó kívánt rendszerek telepíthetők. Ha az adatbázis HANA helyezik át, ezek a jelentések ellenőrizze az adatokat, és számítja ki a memória a HANA-példány. Ezek a jelentések futtatásához, és beszerezni a legújabb javítások vagy verzió kapcsolatos további információkért tekintse meg a következő SAP megjegyzéseket:

- [SAP Megjegyzés #1793345 - HANA az SAP Suite méretezése](https://launchpad.support.sap.com/#/notes/1793345)
- [Megjegyzés: #1872170 - csomag HANA és S/4 HANA méretezési jelentésre SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Megjegyzés #2121330 - gyakran ismételt kérdések: A jelentés méretezése HANA SAP BW Programhoz](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP HANA méretezési BW jelentés #1736976 - Megjegyzés](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP #2296290 - új méretezése a jelentés a HANA BW Programhoz tartozó megjegyzés](https://launchpad.support.sap.com/#/notes/2296290)

Zöld mező megvalósítások esetében gyors Szimbólumméretező SAP HANA fölött SAP szoftver végrehajtásának memóriaigényének kiszámításához rendelkezésére áll.

Memóriára vonatkozó követelményeknek Hana növelje az adatmennyiség növekedésével. Vegye figyelembe a jelenlegi memóriahasználatának kívánja tudni, hogy mi ez lesz a jövőben. Memória követelmények alapján, majd leképezheti a igény szerint a HANA nagy példány termékváltozatok valamelyikére.

## <a name="requirements"></a>Követelmények

Ez a lista állítja össze a SAP HANA futtatásához Azure (nagyobb példány).

**Microsoft Azure**

- Azure-előfizetéssel, amely az Azure (nagy példányok) SAP HANA lehet társítani.
- Microsoft Premier szintű támogatási szerződése. Azure-beli SAP kapcsolatos információkért lásd: [SAP támogatási Megjegyzés #2015553 – a Microsoft Azure SAP: Előfeltételek támogatja](https://launchpad.support.sap.com/#/notes/2015553). Ha HANA nagy példány egységek 384 és több processzort használ, is szeretné bővíteni a Premier szintű támogatási szerződése Azure gyors válasz tartalmazza.
- A HANA nagy példány SKU egy méretezési a gyakorlatban az SAP végrehajtása után szüksége ezekre a műveletekre.

**Hálózati kapcsolat**

- A helyszíni Azure között ExpressRoute: a helyszíni adatközpont csatlakozni az Azure-ba, ügyeljen arra, hogy az internetszolgáltató által biztosított legalább 1-GB/s kapcsolat sorrendben. 

**Operációs rendszer**

- Licencek az SUSE Linux Enterprise Server 12 SAP-alkalmazásokból.

   > [!NOTE] 
   > Az operációs rendszer, amelyeket a Microsoft SUSE nincs regisztrálva. A előfizetés kezelőeszköz példánya nem kapcsolódik.

- SUSE Linux előfizetés felügyeleti eszköz a virtuális gép Azure szolgáltatásba telepített. Ez az eszköz az SAP Hana Azure regisztrálni és SUSE rendre frissítette (nagy példányok) lehetővé teszi. (Nincs nincs internet-hozzáférés a HANA nagy példány adatközpont belül.) 
- Red Hat Enterprise Linux 6.7 vagy 7.2 SAP Hana licencet.

   > [!NOTE]
   > Az operációs rendszer, amelyeket a Microsoft Red Hat nincs regisztrálva. A Red Hat előfizetés Manager példánya nem kapcsolódik.

- Red Hat előfizetés Manager a virtuális gép Azure szolgáltatásba telepített. A Red Hat előfizetés Manager SAP Hana Azure regisztrálni és rendre frissítette a Red Hat (nagy példányok) lehetővé teszi. (Nincs nincs közvetlen internet-hozzáférést a belül a bérlő az Azure nagy példány stamp telepítve.)
- SAP szükséges hozzá egy támogatási szerződés, valamint a Linux-szolgáltatónál. Ez a követelmény HANA nagy példány vagy a Linux futtatja az Azure-ban tény-megoldás által nem eltávolítva. Ellentétben egyes Linux Azure gyűjtemény képek, a szolgáltatás díj van *nem* HANA nagy példány megoldás ajánlatban foglalt. A feladata a követelményeit az SAP támogatási szerződések a Linux terjesztő teljesítésére. 
   - SUSE Linux támogatása a szerződések követelményeinek kereshető [SAP Megjegyzés #1984787 - SUSE Linux Enterprise Server 12: telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787) és [SAP Megjegyzés #1056161 - SUSE prioritás támogatása SAP-alkalmazásokból](https://launchpad.support.sap.com/#/notes/1056161).
   - A Red Hat Linux szüksége van a megfelelő előfizetés, amely támogatja, és frissítheti az operációs rendszerek HANA nagy példány a szolgáltatást. Red Hat [SAP megoldások] azt javasolja a Red Hat Enterprise Linux (https://access.redhat.com/solutions/3082481 előfizetés. 

A támogatási mátrix a különböző Linux verzióival SAP HANA verzióiról, lásd: [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Adatbázis**

- Licencek és a szoftver telepítési összetevők SAP Hana (platform vagy enterprise edition).

**Alkalmazások**

- Licencek és a szoftver telepítési összetevők SAP HANA és a kapcsolódó SAP-hez SAP alkalmazások támogatja a szerződéseket.
- Licencek és a szoftver telepítési összetevők nem SAP alkalmazások Azure (nagy példányok) környezetben található SAP HANA képest használt, és a kapcsolódó támogatási szerződés.

**Képességek**

- És tapasztalattal az Azure infrastruktúra-szolgáltatási és összetevői.
- A felhasználói élmény és központi telepítése az Azure-ban egy SAP munkaterhelés ismerete.
- SAP HANA-telepítés hitelesített tanácsadási csoporthoz.
- SAP felelős mérnök ismereteikkel körül SAP HANA magas rendelkezésre állási és vészhelyreállítási helyreállítási terv.

**SAP**

- Általános gyakorlat, hogy egy SAP-ügyfél van, és rendelkezik a támogatási szolgálathoz, SAP szerződést.
- Kifejezetten a HANA nagy példány SKU típusú osztály megvalósításokhoz vegye fel a kapcsolatot SAP SAP HANA és a végleges konfigurációit, nagy méretű méretezési hardverének verzióiban.


## <a name="storage"></a>Tárolás

A tárolási elrendezés SAP Hana Azure (nagy példányok) SAP HANA konfigurálható a klasszikus üzembe helyezési modellel irányelvek ajánlott SAP keresztül. Az irányelvek ismertetett a [SAP HANA tárhellyel kapcsolatos követelmények](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) találhatók meg.

A HANA nagy példány i. osztály típusú tárolási kötetként négyszer a memória kötet tartalmaz. A nagy példány HANA egységek típusú osztályához a tároló négy alkalommal több nem található. Az egységek rendelkeznek egy kötetet, amelyet HANA tranzakciós napló biztonsági mentések tárolására. További információkért lásd: [telepítése és konfigurálása a SAP HANA (nagy példány) az Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Lásd az alábbi táblázatban a Foglalás tekintetében. A táblázat a különböző kötetek, a másik HANA nagy példány egységek megadott nyers kapacitását.

| HANA nagy példány Termékváltozat | Hana/adatok | Hana/napló | Hana/megosztott | Hana/naplómásolat |
| --- | --- | --- | --- | --- |
| S72 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |MAXIMUM 1280 GB | 768 GB |
| S192 | 4,608 GB | 1,024 GB | 1 536 GB | 1,024 GB |
| S192m | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384 | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S576 | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768 | 28 000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S960 | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


Tényleges telepített kötetek telepítése és az eszköz, amellyel jelenjen meg a kötet mérete alapján függően változhat.

Tovább HANA nagy példány SKU, ha lehetséges osztás darabok néhány példa látható:

| Memória partíció GB-ban | Hana/adatok | Hana/napló | Hana/megosztott | Hana/naplómásolat |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | MAXIMUM 1280 GB | 768 GB |


Ezen értékek nyers mennyiségi számát, amelyek alapján némileg üzembe helyezési és nézze meg a kötetek használt eszközök eltérőek lehetnek. Nincsenek is a többi partíció méretét, például 2,5 TB. Ezen tárolási méretek egy hasonló a korábbi partíciók képlettel kiszámítása. A kifejezés "partíció" nem jelenti azt, hogy az operációs rendszer, a memória vagy a Processzor-erőforrások bármely olyan módon particionáltak. Azt jelzi, hogy a különböző HANA-példányok üzembe a HANA nagy példány egységben érdemes adattárolási partíciókat. 

Szükség lehet további tárhelyet. 1 TB méretű egységekbe további tárterület megvásárlásával tárolási is hozzáadhat. A további tárhely további kötetként lehet hozzáadni. Azt is használható egy vagy több kötetnek a meglévő kiterjeszteni. Nem lehet a kötetek méretének csökkentése eredetileg telepített, és többnyire részletes ismertetését lásd az előző táblázatokban által. Még nem lehet módosítható a köteteket a neve, vagy csatlakoztassa a neveket. A korábban ismertetett tárolási köteteket a HANA nagy példány mértékegységet NFS4 kötetek vannak csatolva.

Biztonsági mentés és visszaállítás és vész helyreállítási célokra használható storage-pillanatfelvételekkel. További információkért lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
A HANA nagy példány használt tároló lehetővé teszi, hogy az adatok átlátható titkosítási a lemezen tárolt. Amikor telepít egy HANA nagy példány egység, engedélyezheti a titkosítás eredő. Is titkosított kötetek után módosíthatja a központi telepítés akkor történik meg. Áthelyezést nem titkosított titkosított kötetek átlátható, és nincs szükség az állásidő. 

I. osztály azzal a típussal, SKU, a logikai egység tárolt rendszerindító kötet titkosítva van. Az SKU a HANA nagy példány típusú osztály az operációs rendszer módszerek a rendszerindítás LUN titkosítania kell. További információért forduljon a Microsoft szolgáltatás-felügyeleti csoport.


## <a name="networking"></a>Hálózat

Az Azure hálózati szolgáltatások architektúrája a sikeres alkalmazások központi telepítését figyelemmel SAP HANA-nagy példányon nyilvános kulcsokra épülő. SAP HANA Azure (nagy példányok) üzemelő példányok esetében általában egy nagyobb SAP, számos különböző SAP-megoldások különböző méretű adatbázisok, a Processzor-erőforrás-felhasználás és a memóriahasználat a fekvő tájolás kell. Valószínű, hogy nem minden említett SAP rendszereken SAP HANA alapulnak. Az SAP fekvő valószínűleg használó hibrid:

- SAP rendszerek helyszíni üzembe. A mérete miatt ezek a rendszerek jelenleg nem lehet üzemeltetni az Azure-ban. Példa: egy üzemi (és az adatbázis) SQL-kiszolgálón fut, és további CPU és memória erőforrást, mint a virtuális gépeket biztosíthatnak SAP ERP rendszer.
- SAP SAP HANA-alapú rendszerek helyszíni üzembe.
- SAP rendszerek központilag telepített virtuális gépeken. Ezek a rendszerek fejlesztési, tesztelési, védőfal, lehet, vagy az SAP NetWeaver-alapú alkalmazások, amelyek az Azure-ban (a virtuális gépeken), erőforrás használat és a memória igény szerint sikeres telepítése éles példányának. Ezek a rendszerek is alapulhat például az SQL Server adatbázisok. További információkért lásd: [SAP támogatási Megjegyzés #1928533 – Azure SAP-alkalmazásokból: támogatott termékek és Azure Virtuálisgép-típusokon](https://launchpad.support.sap.com/#/notes/1928533/E). És ezek a rendszerek adatbázisok például SAP HANA-alapú lehet. További információkért lásd: [SAP HANA hitelesített IaaS platformok](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Telepített SAP alkalmazáskiszolgálók (a virtuális gépeken) Azure-ban Azure (nagy példányok) SAP HANA kihasználhatja az Azure nagy példány stampek.

SAP fekvő négy vagy több különböző központi telepítési forgatókönyveket, hibrid jellemző. Nincsenek is az Azure-ban futó teljes SAP-tájak eseteit sok ügyfél. Virtuális gépek egyre nagyobb teljesítményű, helyezze át az SAP-megoldások Azure-ügyfelek számát növeli.

Azure hálózatkezelés a SAP rendszerek Azure szolgáltatásba telepített környezet nem összetett. A következő alapelveket alapul:

- Egy Azure virtuális hálózatot kapcsolódnia kell az ExpressRoute-kapcsolatcsoport, amely egy helyi hálózathoz csatlakozik.
- Amely általában a helyszíni csatlakozik Azure ExpressRoute-kapcsolatcsoportot rendelkeznie kell a sávszélesség 1 GB/s vagy újabb. A minimális sávszélesség megfelelő sávszélesség a helyszíni és a virtuális gépeken futó rendszerek közötti adatátvitelt tesz lehetővé. Lehetővé teszi a kapcsolat megfelelő sávszélesség Azure rendszereken a helyszíni felhasználók.
- Az Azure-ban minden SAP rendszer kell kell beállítani a virtuális hálózatokon, egymással kommunikálni.
- Active Directory és a DNS a helyben tárolt kiterjeszthetőek az Azure ExpressRoute segítségével helyszíni.


> [!NOTE] 
> A számlázási szempontjából csak egy Azure-előfizetéssel csak egy bérlő által egy adott Azure-régió, egy nagy példány stamp lehet társítani. Ezzel ellentétben egy nagy példány stamp egybérlős csak egy Azure-előfizetés lehet társítani. Ez a követelmény az Azure-ban más számlázható objektumok összhangban.

Ha több különböző Azure-régiók SAP HANA Azure (nagy példányok) lett telepítve, egy külön bérlő a nagy példány stamp van telepítve. Mindaddig, amíg ezek a példányok az azonos SAP fekvő részét képezik az azonos Azure-előfizetés alapján is futtathatja. 

> [!IMPORTANT] 
> Csak az Azure Resource Manager deployment SAP HANA Azure (nagy példányok) használata támogatott.

 

### <a name="additional-virtual-network-information"></a>További virtuális hálózati adatok

Szeretne csatlakozni a virtuális hálózati ExpressRoute, egy Azure-átjárót kell létrehozni. További információkért lásd: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Egy Azure-átjáró ExpressRoute egy Azure-on kívüli infrastruktúra vagy egy Azure nagy példány stamp használható. Egy Azure-átjárót is segítségével csatlakozzon a virtuális hálózatok között. További információkért lásd: [hálózatok közötti kapcsolatot az erőforrás-kezelő konfigurálása a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Legfeljebb négy különböző ExpressRoute-kapcsolatot az Azure-átjáró is elérheti, mindaddig, amíg másik Microsoft vállalati peremhálózati útválasztók származhat ezeket a kapcsolatokat. További információkért lásd: [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolatokat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Egy Azure átjáró átirányítást biztosít a átviteli eltér mindkét esetben használja. További információkért lásd: [VPN-átjáró](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A maximális átviteli sebesség érhető el, ha egy virtuális hálózati átjáró 10 GB/s egy ExpressRoute-kapcsolat használatával. A virtuális hálózatban található virtuális gépek és a rendszer között a fájlok másolása a helyszíni (adatfolyamként egyetlen másolatának) nem érhetők el a teljes átviteli képessége – a különböző gateway SKU. Használja ki a virtuális hálózati átjáró teljes sávszélességét, használjon több adatfolyam. Vagy más fájlokat egyetlen fájl párhuzamos adatfolyamokat kell másolnia.


### <a name="networking-architecture-for-hana-large-instance"></a>Hálózati architektúra nagy HANA-példány
A hálózati architektúra HANA nagy példány négy részből sorolhatók:

- A helyszíni hálózat és az Azure ExpressRoute-kapcsolatot. Ez a rész a felhasználói tartomány és Azure ExpressRoute keresztül kapcsolódik. Tekintse meg az alábbi ábrán jobb alsó részen.
- Az Azure hálózati szolgáltatások, előbb tárgyalt módon, a virtuális hálózatok, átjárók, újra kell. Ez a kijelző egy olyan területre, ahol kell a megfelelő tervek található az alkalmazás követelményeinek, biztonsági és megfelelőségi követelmények. Egy másik és megfontolandó szempont szempontjából a virtuális hálózatok és az Azure gateway SKU száma a választható HANA nagy példány használatát. Az ábrán jobb felső részén talál.
- Nagy HANA-példány keresztüli kapcsolat ExpressRoute technológia az Azure. Ez a kijelző telepített, és a Microsoft kezeli. Adja meg az egyes IP-címtartományok HANA nagy példány az eszközök központi telepítése után a virtuális hálózatokhoz való kapcsolódásának az ExpressRoute-kapcsolatcsoport mindössze annyit kell tennie. További információkért lásd: [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolatokat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Hálózati HANA nagy példányban, ez utóbbi érték többnyire átlátszó.

![Az Azure (nagy példányok) és a helyszíni SAP HANA csatlakozó virtuális hálózati](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

A követelmény, hogy a helyszíni eszközök kapcsolódáshoz kell az Azure ExpressRoute nem módosítható, mivel a nagy HANA-példányt használ. Egy vagy több virtuális hálózatokhoz a virtuális gépek, amelyek az alkalmazási rétegre, amely kapcsolódik a HANA nagy példány egységekben üzemeltetett HANA példányok működteti, futtassa a követelménnyel is nem változik. 

Az Azure-ban SAP-központitelepítések különbségek a következők:

- Az ügyfél-bérlő HANA nagy példány egységei egy másik ExpressRoute-kapcsolatcsoportot keresztül kapcsolódó a virtuális hálózatba. Külön munkaterhelés esetére, a helyszíni virtuális hálózatok ExpressRoute- és virtuális hálózatok és HANA nagy példány között hivatkozásokat ne ossza meg ugyanazt az útválasztók.
- A munkaterhelés-profil az SAP alkalmazási rétegre és a HANA nagy példány között különböző jellegű, a sok kisméretű kérelem és felszakadásáig például adatok SAP HANA (eredményhalmazt) átviszi az alkalmazás rétegbe.
- Az SAP alkalmazásarchitektúra érzékenyebb a hálózati késés, mint a jellemző forgatókönyvek, ahol adatcsere a helyszíni és az Azure között.
- A virtuális hálózati átjáró legalább két ExpressRoute kapcsolat van. Mindkét kapcsolatok ossza meg a virtuális hálózati átjáró bejövő adatok maximális sávszélessége.

A hálózat késését, hibát észlelt a virtuális gépek és HANA nagy példány között egység egy tipikus VM-VM hálózati oda-vissza késés nagyobb lehet. Az Azure-régió függ, mért értékek lépheti túl a 0,7-körbejárási késése (ms) átlaga alá besorolt [SAP Megjegyzés #1100926 - gyakran ismételt kérdések: hálózati teljesítményt](https://launchpad.support.sap.com/#/notes/1100926/E). Az ügyfelek azonban SAP HANA-alapú üzemi SAP-alkalmazásokból sikeresen példányon SAP HANA nagy telepíthetnek. Az ügyfelek, akik jelentés kiváló fejlesztései telepítették az SAP-alkalmazásokból futtatásával SAP HANA HANA nagy példány egységek használatával. Győződjön meg arról, hogy alaposan tesztelni az üzleti folyamatok Azure HANA nagy példányában.
 
Arra, hogy a virtuális gépek és HANA nagy példány között determinisztikus hálózati késés, a választott a virtuális hálózati átjáró Termékváltozat nem lényeges. Ellentétben a helyszíni és a virtuális gépek közötti forgalmat a virtuális gépek és HANA nagy példány között forgalmat minta fejleszthet az átvitelre váró kérések- és adatkötetek kicsi, de nagy felszakadásáig. Ilyen felszakadásáig is kezelni, a UltraPerformance gateway SKU használata erősen ajánlott. A HANA nagy példány termékváltozatok típusú osztályához SKU UltraPerformance átjáró virtuális hálózati átjáró használata kötelező.

> [!IMPORTANT] 
> A teljes hálózati forgalom az SAP-alkalmazások és adatbázis rétegek között megadott, csak a HighPerformance vagy UltraPerformance gateway SKU-n virtuális hálózatok támogatottak SAP HANA Azure (nagy példányok) való kapcsolódáshoz. Csak a UltraPerformance gateway SKU HANA nagy példány típus II SKU, mint a virtuális hálózati átjáró támogatott.



### <a name="single-sap-system"></a>Egyetlen SAP rendszer

A korábban látható a helyszíni infrastruktúra az Azure ExpressRoute csatlakozik. Az ExpressRoute-kapcsolatcsoport csatlakozik egy vállalati peremhálózati útválasztó be. További információkért lásd: [ExpressRoute műszaki áttekintés](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az útvonal létrejötte után az Azure gerincét csatlakozik, és minden Azure-régió érhetők el.

> [!NOTE] 
> SAP tájak futtassa az Azure-ban, csatlakoznia kell a vállalati peremhálózati útválasztó az Azure-régió, a SAP tükrében megfigyelhető legközelebbi. Az Azure nagy példány bélyegzők dedikált vállalati peremhálózati útválasztó eszközök Azure infrastruktúra-szolgáltatási és nagy példány bélyegzők a virtuális gépek közötti hálózati késés csökkentése érdekében érdemes keresztül csatlakoznak.

Az SAP alkalmazás példányait futtató virtuális gépek virtuális hálózati átjáró csatlakozik-e az ExpressRoute-kapcsolatcsoportot. Az azonos virtuális hálózatban egy külön vállalati peremhálózati útválasztó dedikált nagy példány bélyegzők való csatlakozik.

A rendszer egy egyszerű példa egy SAP rendszerrel. Az SAP alkalmazásréteg az Azure-ban tárolja. Az SAP HANA-adatbázisból SAP HANA (nagy példányok) Azure-on futtatja. A feltételezi, hogy a virtuális hálózati átjáró sávszélesség 2-kapcsolóport vagy 10-GB/s átviteli nem határoz meg a szűk keresztmetszetek.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP rendszerek vagy nagy számú SAP-rendszerek

Ha több SAP rendszerek vagy nagy számú SAP-rendszerek SAP HANA Azure (nagy példányok) való kapcsolódáshoz van telepítve, az átviteli sebessége a virtuális hálózati átjáró válhatnak a szűk keresztmetszetek. Ebben az esetben az alkalmazás rétegek felosztása több virtuális hálózat. Akkor is előfordulhat, hogy speciális virtuális hálózat létrehozása, amely összeköti HANA nagy példány olyan esetekben, mint:

- Biztonsági mentések végrehajtása HANA nagy példány HANA példányokból egy Azure-ban, amelyen az NFS-megosztásokat.
- Másolás nagy biztonsági másolatait és egyéb fájlok HANA nagy példány egységekből az Azure-ban kezelt lemezterület.

A gazdagépen virtuális gépeket, hogy a tárterület kezelése külön virtuális hálózatot használ. Ezzel az elrendezéssel elkerülhetők a nagy méretű fájlok vagy az adatátvitel HANA nagy példányból az Azure-bA az SAP alkalmazásréteg futtató virtuális gépeken látja, hogy a virtuális hálózati átjárón hatásait. 

Több méretezhető hálózati architektúra:

- Használja ki az egyetlen, nagyobb SAP alkalmazásréteg több virtuális hálózat.
- Központi telepítése egy különálló virtuális hálózat minden egyes SAP rendszert telepíteni, ezeket külön alhálózatokon ugyanazt a virtuális hálózatot az SAP rendszerek kombinálásával képest.

 A több méretezhető hálózati architektúra SAP Hana Azure (nagy példány):

![SAP alkalmazásréteg telepítése több virtuális hálózaton keresztül](./media/hana-overview-architecture/image4-networking-architecture.png)

Az ábrán az látható, a SAP alkalmazásréteg vagy összetevők telepített több virtuális hálózaton keresztül. Ez a konfiguráció bevezetett elkerülhetetlen késés terhelést, hogy a virtuális hálózatok üzemeltetett alkalmazások közötti kommunikáció során történt. Alapértelmezés szerint ebben a konfigurációban a vállalati peremhálózati útválasztók útvonal különböző virtuális hálózatokon lévő virtuális gépek közötti hálózati forgalmat. 2016 szeptemberétől, mert ez az útválasztási is lehet optimalizálni. 

A optimalizálása és a két virtuális hálózatok közötti kommunikációs késés kevesebb módja a társviszony-létesítési virtuális hálózatok a régión belül. Ez a módszer használható akkor is, ha virtuális hálózatok különböző előfizetésekhez. A virtuális hálózati társviszony-létesítést, két különböző virtuális hálózatokon lévő virtuális gépek közötti kommunikáció használhatja az Azure-hálózat gerincét közvetlenül kommunikálhatnak egymással. Késés jeleníti meg, ha a virtuális gépek ugyanazt a virtuális hálózatot. Az Azure virtuális hálózati átjárón keresztül csatlakozó IP-címtartományok orvosló továbbítódik a virtuális hálózat az egyes virtuális hálózati átjárón keresztül. 

További információ a virtuális hálózati társviszony-létesítés: [virtuális hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Az Azure-Útválasztás

Három, hálózati útválasztási szempontok fontosak SAP HANA Azure (nagy példány):

* SAP HANA Azure (nagy példányok) csak a virtuális gépek és a dedikált ExpressRoute-kapcsolatot, nem közvetlenül a helyszíni keresztül elérhető. Közvetlen hozzáférést a helyszíni HANA nagy példány egység, Microsoft által kiadott nem lehetséges közvetlenül. A tranzitív útválasztási korlátozások vonatkoznak az SAP HANA nagy példány használt aktuális Azure hálózati architektúrája miatt. Egyes felügyeleti ügyfeleket és kell közvetlen hozzáférés, például SAP megoldás Manager fut a helyszíni alkalmazások nem tud kapcsolódni az SAP HANA-adatbázisból.

* Ha két különböző régiókban Azure vész-helyreállítási telepített HANA nagy példány egység, az azonos átmeneti útválasztási korlátozások érvényesek. Más szóval egy régióban (például US nyugati) HANA nagy példány egység IP-címek nincs átirányítva telepített egy másik régióban (például amerikai keleti) HANA nagy példány egység. Ez a korlátozás nem függ a társviszony-létesítés régiók közötti vagy a virtuális hálózatokhoz való kapcsolódásának HANA nagy példány egységek ExpressRoute-Kapcsolatcsoportok cross-csatlakozás Azure-beli hálózat használatát. Egy grafikus megjelenítését lásd: az ábra a következő szakaszban: "Használata HANA nagy példány egységek több régióba." Ez a korlátozás, amely a telepített architektúrával, nem engedélyezi a vész-helyreállítási funkcióinak HANA replikációs azonnali használatát.

* SAP HANA Azure (nagy példányok) egységeken rendelkezik a kiszolgáló IP-készlet címtartománya beküldött hozzárendelt IP-címeit. További információkért lásd: [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolatokat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az IP-cím az Azure-előfizetések és a virtuális hálózatok csatlakozó HANA (nagy példányok) Azure ExpressRoute keresztül érhető el. Az IP-cím hozzárendelése ki, hogy a kiszolgáló IP-készlet címtartománya közvetlenül hozzárendelve a hardver egységhez. Rendelkezik *nem* hozzárendelt hálózati többé, mint az első telepítések esetén az ilyen típusú megoldásra. 

> [!NOTE] 
> A korlátozásnak átmeneti útválasztási megoldásához, ahogy az első két listaelemek, használja a további összetevők irányításához. A szoftverkorlátozó megoldásához használható összetevők lehetnek:

> * A fordított proxy adatok továbbításához a. Ha például F5 BIG-IP, NGINX a Traffic Managerrel virtuális tűzfal/forgalom útválasztási megoldásként Azure szolgáltatásba telepített.
> * Használatával [IPTables szabályok](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) egy Linux virtuális gépre, a helyszíni helyek és HANA nagy példány egységek, vagy különböző régiókban HANA nagy példány egységek között Útválasztás engedélyezése.

> Vegye figyelembe, hogy végrehajtása, és támogatja az egyéni megoldások használata esetén a külső hálózati készülékek, vagy IPTables a Microsoft nem biztosítja. Támogatási összetevőt használja a szállítójával, vagy a integráló kell biztosítania. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Internetkapcsolat nagy HANA-példány
HANA nagy példány does *nem* rendelkezik közvetlen internetkapcsolattal. Tegyük fel ez a korlátozás korlátozhatnak operációsrendszer-lemezképet közvetlenül az operációs rendszer szállító regisztrálni a képességére. Szükség lehet a helyi SUSE Linux Enterprise Server előfizetés kezelőeszköz kiszolgálóhoz vagy a Red Hat Enterprise Linux előfizetés Manager működéséhez.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Virtuális gépek és HANA nagy példány között adattitkosítás
Nagy HANA-példány és a virtuális gépek között továbbított adatok nincsenek titkosítva. Azonban csak az exchange között a HANA adatbázis-kezelő és az alkalmazások JDBC/ODBC-alapú, engedélyezheti forgalom titkosítása. További információkért lásd: [ebben a dokumentációban SAP által](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Több régióba HANA nagy példány egységek használata

Lehetséges, hogy központi telepítése az Azure (nagy példányok) a több Azure-régiók feltételtag vész-helyreállítási SAP HANA okok miatt. Lehet, hogy az elérni kívánt HANA nagy példány az egyes régiókban különböző virtuális hálózatokon a telepített virtuális gépek. A különböző HANA nagy példány egységek hozzárendelt IP-címeket a virtuális hálózatok a példányok az átjárón keresztül közvetlenül csatlakozó túl nem kerülnek. Ennek eredményeképpen a enyhe módosítása megjelent a virtuális hálózati terv. A virtuális hálózati átjáró kezelni tud a négy különböző ExpressRoute-Kapcsolatcsoportok kívül más vállalati peremhálózati útválasztók. Minden egyes virtuális hálózat, amely egy nagy példány stampek kapcsolódik egy másik Azure-régió, a nagy példány stamp lehet csatlakoztatni.

![Virtuális hálózat csatlakozik a különböző Azure-régiók nagy példány Azure bélyegzők](./media/hana-overview-architecture/image8-multiple-regions.png)

Az ábrán látható, hogyan mindkét régiókban a különböző virtuális hálózatokhoz csatlakoznak két különböző ExpressRoute-Kapcsolatcsoportok SAP HANA Azure (nagy példányok) való kapcsolódáshoz használt mindkét Azure régióban. Az újonnan bevezetett kapcsolatok a téglalap alakú piros sorokat. Ezeket a kapcsolatokat, a virtuális hálózatokon kívül a virtuális hálózatok egyikére futó virtuális gépeket érhetik el a különböző HANA nagy példány egységek, a két régió telepített minden egyes. Az ábrán az látható, mivel feltételezzük, hogy rendelkezik-e a helyszíni az két ExpressRoute-kapcsolat a két Azure-régiók. Ezzel az elrendezéssel vész-helyreállítási okokból ajánlott.

> [!IMPORTANT] 
> Ha több ExpressRoute-Kapcsolatcsoportok, AS elérési fertőző és a helyi beállításokat szabályozó BGP-beállítások használatával győződjön meg arról, a forgalom megfelelő útválasztását.


