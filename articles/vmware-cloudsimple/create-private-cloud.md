---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple private cloud létrehozása
description: Bemutatja, hogyan hozhat létre egy CloudSimple private cloudot a VMware-számítási feladatok felhőre való kiterjesztéséhez működési rugalmassággal és folyamatossággal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024789"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple privát felhő létrehozása

A privát felhő egy elkülönített VMware verem, amely támogatja az ESXi gazdagépek, vCenter, vSAN és NSX. A privát felhők kezelése a CloudSimple portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományban. A verem dedikált csomópontokon és elkülönített, csupasz fém hardvercsomópontokon fut.

A magánfelhő létrehozásával számos közös hálózati igény nek megfelelően kell elférnie:

* **Növekedés**. Ha elérte a meglévő infrastruktúra hardverfrissítési pontját, a magánfelhő lehetővé teszi a bővítést új hardverbefektetés nélkül.

* **Gyors bővülés**. Ha bármilyen ideiglenes vagy nem tervezett kapacitásra van szükség, a private cloud lehetővé teszi, hogy a további kapacitást késedelem nélkül hozza létre.

* **Fokozott védelem**. Három vagy több csomópontból álló privát felhővel automatikus redundanciát és magas rendelkezésre állásvédelmet kaphat.

* **Hosszú távú infrastrukturális igények**. Ha az adatközpontok kapacitása, vagy szeretné átstrukturálni a költségek csökkentése érdekében, a magánfelhő lehetővé teszi, hogy kivonja adatközpontok és áttérni egy felhőalapú megoldás, miközben továbbra is kompatibilis a vállalati műveletek.

Amikor létrehoz egy privát felhőt, egyetlen vSphere-fürtöt és az adott fürtben létrehozott összes felügyeleti virtuális gépet kap.

## <a name="before-you-begin"></a>Előkészületek

A privát felhő létrehozása előtt a csomópontokat ki kell építeni. A csomópontok kiépítéséről további információt az [Azure VMware-megoldás felhőalapú csomópontjainak kiépítése című témakörben talál.](create-nodes.md)

Cidr-tartomány lefoglalása vSphere/vSAN alhálózatokhoz a magánfelhőhöz. A privát felhő egy vCenter-kiszolgáló által felügyelt elkülönített VMware veremkörnyezetként jön létre (ESXi-gazdagépekkel, vCenter-kiszolgálóval, vSAN-nal és NSX-szel). A felügyeleti összetevők a CIDR vSphere/vSAN alhálózatokhoz kiválasztott hálózatban vannak telepítve. A hálózati CIDR-tartomány a központi telepítés során különböző alhálózatokra van felosztva. A vSphere/vSAN alhálózati címterületnek egyedinek kell lennie. Nem fedheti át a CloudSimple környezettel kommunikáló hálózatot. A CloudSimple-lel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure virtuális hálózatok. A vSphere/vSAN alhálózatokról további információt a VLAN-ok és alhálózatok áttekintése című témakörben talál.

* Minimális vSphere/vSAN alhálózatok CIDR tartományelőtagja: /24
* Maximális vSphere/vSAN alhálózatok CIDR tartományelőtagja: /21


## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

A [CloudSimple portál](access-cloudsimple-portal.md)elérése.

## <a name="create-a-new-private-cloud"></a>Új magánfelhő létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatásokat.**
3. Válassza ki azt a CloudSimple szolgáltatást, amelyen létre szeretné hozni a magánfelhőt.
4. Az **áttekintés területen**kattintson a **Magánfelhő létrehozása** elemre a CloudSimple portál új böngészőlapjának megnyitásához. Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.

    ![Privát felhő létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. A CloudSimple portálon adja meg a privát felhő nevét.
6. Válassza a **helymeghatározást** a magánfelhőhöz.
7. Válassza ki **a Csomópont típusát,** amely összhangban van az Azure-ban kiépített vel.
8. Adja meg **a csomópontszám számát**.  A magánfelhő létrehozásához legalább három csomópont szükséges.

    ![Privát felhő létrehozása - Alapvető adatok](media/create-private-cloud-basic-info.png)

9. Kattintson a **Tovább: Speciális beállítások gombra.**
10. Adja meg a CIDR-tartományt a vSphere/vSAN alhálózatokhoz. Győződjön meg arról, hogy a CIDR-tartomány nem fedi át a helyszíni vagy más Azure-alhálózatok (virtuális hálózatok) vagy az átjáró alhálózat.

    **CIDR tartomány beállításai:** /24, /23, /22 vagy /21. A /24 CIDR tartomány legfeljebb kilenc csomópontot támogat, a /23 CIDR tartomány legfeljebb 41 csomópontot, a /22 és /21 CIDR tartomány pedig legfeljebb 64 csomópontot támogat (a privát felhőben lévő csomópontok maximális száma).

    > [!IMPORTANT]
    > A vSphere/vSAN CIDR tartományban lévő IP-címek a private cloud infrastruktúra számára vannak fenntartva.  Ne használja az IP-címet ebben a tartományban semmilyen virtuális gépen.

11. Kattintson a **Tovább gombra: Véleményezés és létrehozás.**
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson **az Előző**gombra.
13. Kattintson **a Létrehozás gombra.**

A privát felhőlétesítési folyamat elindul. A magánfelhő kiépítése akár két órát is igénybe vehet.

A meglévő magánfelhő bővítéséről a [Magánfelhő bővítése](expand-private-cloud.md)című témakörben talál.
