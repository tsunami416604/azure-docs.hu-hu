---
title: Az SAP HANA áttekintése az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA azure-beli (nagy példányok) üzembe helyezésének áttekintése.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39fcf5d0fe2273c4debd3ae5ebe5fd1190ddc959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616953"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az SAP HANA az Azure-ban (nagy méretű példányok)?

AZ SAP HANA az Azure-ban (nagy példányok) egy egyedülálló megoldás az Azure-ban. Az SAP HANA üzembe helyezéséhez és futtatásához szükséges virtuális gépek biztosítása mellett az Azure lehetőséget kínál az SAP HANA futtatására és üzembe helyezésére az Ön számára dedikált, operációs rendszer nélküli kiszolgálókon. Az SAP HANA az Azure-ban (nagy példányok) megoldás nem megosztott gazda-/kiszolgáló i metlen-fém hardver, amely önhöz van rendelve. A kiszolgáló hardvere nagyobb bélyegzőkbe van ágyazva, amelyek számítási/kiszolgálói, hálózati és tárolási infrastruktúrát tartalmaznak. Kombinációként hana szabott adatközpont-integráció (TDI) tanúsítvánnyal rendelkezik. SAP HANA az Azure-ban (nagy példányok) különböző kiszolgálói sk-k vagy méretek kínál. Az egységek 36 Intel CPU maggal és 768 GB memóriával rendelkezhetnek, és akár 480 Intel CPU maggal és akár 24 TB memóriával is rendelkezhetnek.

Az infrastruktúra bélyegzőn belüli ügyfélelkülönítés a bérlőkben történik, amely így néz ki:

- **Hálózatkezelés**: Az ügyfelek elkülönítése az infrastruktúra-veremen belül a virtuális hálózatokon keresztül ügyféláltal hozzárendelt bérlőnként. A bérlő egyetlen ügyfélhez van hozzárendelve. Egy ügyfél több bérlővel is rendelkezhet. A bérlők hálózati elkülönítése megtiltja a bérlők közötti hálózati kommunikációt az infrastruktúra bélyegzőszintjén, még akkor is, ha a bérlők ugyanahhoz az ügyfélhez tartoznak.
- **Tárolási összetevők:** Elkülönítés tárolóvirtuális gépeken keresztül, amelyekhez tárolókötetek vannak rendelve. A tárolókötetek csak egy tárolóvirtuális géphez rendelhetők hozzá. A tároló virtuális gép van rendelve kizárólag egyetlen bérlő az SAP HANA TDI tanúsítvánnyal rendelkező infrastruktúra verem. Ennek eredményeképpen a tároló virtuális géphez rendelt tárolókötetek csak egy adott és kapcsolódó bérlőben érhetők el. Nem láthatók a különböző üzembe helyezett bérlők között.
- **Kiszolgáló vagy állomás**: A kiszolgáló vagy a gazdaegység nincs megosztva az ügyfelek vagy a bérlők között. Az ügyfél üzembe helyezett kiszolgáló vagy gazdagép egy atomi, csupasz fémből álló számítási egység, amely egyetlen bérlőhöz van rendelve. *Nincs* olyan hardveres particionálás vagy lágy particionálás, amely azt eredményezheti, hogy egy állomást vagy egy kiszolgálót megoszt egy másik ügyféllel. Az adott bérlő tárolóvirtuális gépéhez rendelt tárolókötetek ilyen kiszolgálóhoz vannak csatlakoztatva. A bérlő rendelkezhet egy vagy több kiszolgálóegységek különböző sk-ek kizárólag hozzárendelt.
- Az Azure (Large Instances) infrastruktúra-bélyegzőn belül számos különböző bérlő van telepítve és elkülönítve egymás ellen a hálózati, tárolási és számítási szintű bérlői fogalmakon keresztül. 


Ezek a csupasz-fém kiszolgálóegységek csak AZ SAP HANA futtatásához támogatottak. Az SAP alkalmazásréteg vagy a számítási feladatok középső szoftverrétege virtuális gépeken fut. Az Azure-beli (nagy példányok) egységeken az SAP HANA-t futtató infrastruktúra-bélyegzők az Azure hálózati szolgáltatások gerincéhez kapcsolódnak. Ily módon alacsony késésű kapcsolat az SAP HANA az Azure -ban (nagy példányok) egységek és a virtuális gépek biztosított.

2019 júliusától különbséget teszünk a HANA nagypéldány-bélyegzők két különböző módosítása és a központi telepítés helye között:

- "3. verzió" (Rev 3): Azok a bélyegzők, amelyeket az ügyfél számára 2019 júliusa előtt rendelkezésre bocsátottak
- "4. verzió" (Rev 4): Új bélyegzőkialakítás, amely az Azure virtuális gép gazdagépeinek közvetlen közelében van telepítve, és amely eddig az Azure-régiókban jelent meg:
    -  USA 2. nyugati régiója 
    -  USA keleti régiója 
    -  Nyugat-Európa
    -  Észak-Európa


Ez a dokumentum egyike azoknak a dokumentumoknak, amelyek az SAP HANA az Azure-ban (nagy példányok). Ez a dokumentum bemutatja a megoldás által nyújtott alapvető architektúrát, felelősségi köröket és szolgáltatásokat. A megoldás magas szintű képességeit is tárgyaljuk. A legtöbb más területen, például a hálózatkezelés és a kapcsolat, négy másik dokumentum ismerteti a részleteket és a részletezési információkat. Az SAP HANA dokumentációja az Azure-ban (nagy példányok) nem terjed ki az SAP NetWeaver telepítésének vagy üzembe helyezésének szempontjaira a virtuális gépeken. Az Azure-beli SAP NetWeaver az ugyanabban az Azure-dokumentációs tárolóban található külön dokumentumokban található. 


A HANA nagypéldányok útmutatójának különböző dokumentumai a következő területekre terjednek ki:

- [SAP HANA (nagy példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (nagy példányok) infrastruktúrája és kapcsolata az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Sap HANA (nagy példányok) telepítése és konfigurálása az Azure-ban](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Nagy példányok) hibaelhárításés és figyelés az Azure-ban](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Magas rendelkezésre állás a SUSE-ban a STONITH használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [A 3-as verzió bélyegzőinek II típusú suk-jainak operációs rendszerének biztonsági mentése és visszaállítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**További lépések**
- Olvassa [el ismerve a feltételeket](hana-know-terms.md)