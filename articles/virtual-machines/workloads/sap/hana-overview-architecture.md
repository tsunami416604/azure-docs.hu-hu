---
title: Az SAP HANA az Azure-ban (nagyméretű példányok) áttekintése |} A Microsoft Docs
description: Központi telepítése a SAP HANA az Azure-ban (nagyméretű példányok) áttekintése.
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
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44026921"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az SAP HANA az Azure-ban (nagyméretű példányok)?

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

**Következő lépések**
- Tekintse meg [ismeri a feltételeket](hana-know-terms.md)