---
title: Biztonságfigyelés az Azure Security Centerben | Microsoft Docs
description: Ez a cikk az Azure Security Center figyelési funkcióinak használatába nyújt bevezetést.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: 434b73d4625f86fab195dbda1fed9c841791f5b6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099459"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Biztonsági állapotfigyelés az Azure Security Centerben
Ez a cikk az Azure Security Center figyelési funkcióknak használatához nyújt segítséget a szabályzatoknak való megfelelés ellenőrzése érdekében.

## <a name="what-is-security-health-monitoring"></a>Mi az a biztonsági állapotfigyelés?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonságfigyelés ezzel szemben proaktív stratégiát jelent, amely az erőforrásokat ellenőrizve azonosítja a vállalati szabványoknak vagy ajánlott eljárásoknak meg nem felelő rendszereket.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. A **Megelőzés** szakasz területén megtekintheti az erőforrások biztonsági állapotát és az esetleges problémákat. A problémák listáját a **Javaslatok** csempén is megtekintheti.

A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).

Az **Erőforrás állapotának monitorozása** területen is nyomon követheti erőforrásainak biztonsági állapotát. Az alábbi példában azt láthatja, hogy minden egyes erőforrás csempéjén (Számítási tevékenység és alkalmazások, Hálózat, Adatbiztonság, valamint Identitás és hozzáférés) megtalálható az azonosított hibák összes száma.

![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Számítási tevékenység és alkalmazások monitorozása
További információkat a [gépek és alkalmazások az Azure Security Centerben való védelméről](security-center-virtual-machine-recommendations.md) szóló témakörben olvashat.

### <a name="monitor-virtual-networks"></a>Virtuális hálózatok figyelése
A **Hálózat** csempére kattintva megnyílik az alábbi képernyőképen látható **Hálózat** panel, amelyen további információk érhetők el:

![Hálózat panel](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Hálózatokra vonatkozó javaslatok
A virtuális gépek erőforrás-állapotára vonatkozó információkhoz hasonlóan a képernyő felső részén a problémák összefoglaló listája látható, alább pedig a megfigyelt hálózatok listája.

A hálózati állapotot részletező listában megtekintheti a potenciális biztonsági problémákat, valamint az ezekre vonatkozó [javaslatokat](security-center-network-recommendations.md). Problémát jelenthetnek például a következők:

* Nincs telepítve újgenerációs tűzfal (NGFW)
* Az alhálózatokon nincsenek bekapcsolva a hálózati biztonsági csoportok
* Az virtuális gépeken nincsenek bekapcsolva a hálózati biztonsági csoportok
* Külső hozzáférés korlátozása nyilvános külső végponton keresztül
* Megfelelő állapotú internet felé néző végpontok

Ha egy javaslatra kattint, az alábbi példában látható módon további részleteket tekinthet meg a javaslatról:

![Az adott javaslat részletei a Hálózat panelen](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Ebben a példában a **Configure Missing Network Security Groups for Subnets** (Hiányzó hálózati biztonsági csoportok konfigurálása az alhálózatok számára) panelen láthatja azoknak az alhálózatoknak és virtuális gépeknek a listáját, amelyekhez nem tartoznak hálózati biztonsági csoportok védelme alá. Ha arra az alhálózatra kattint, amelyre a hálózati biztonsági csoportot szeretné alkalmazni, megnyílik a **Hálózati biztonsági csoport választása** panel. Itt kiválaszthatja az alhálózathoz legmegfelelőbb hálózati biztonsági csoportot, illetve akár új hálózati biztonsági csoportot is létrehozhat.

#### <a name="internet-facing-endpoints-section"></a>Internet facing endpoints (Internet felé néző végpontok) szakasz
Az **Internet facing endpoints** (Internet felé néző végpontok) részben azokat a virtuális gépeket láthatja, amelyeken jelenleg internet felé néző végpont működik, illetve megtekinthető a végpont aktuális állapota is.

![Internet felé néző végpontokkal konfigurált virtuális gépek és a végpontok állapota](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ez a táblázat tartalmazza a virtuális gépet tartalmazó végpont nevét, az internet felé néző IP-címet, valamint a hálózati biztonsági csoport és az NGFW aktuális súlyossági állapotát. A táblázat elemei súlyosság szerint rendezve jelennek meg:

* Piros (legfelül): a legmagasabb prioritás, azonnali beavatkozást igényel
* Narancssárga: közepes szintű prioritás, a lehető legrövidebb időn belül beavatkozást igényel
* Zöld (utolsó): megfelelő állapot

#### <a name="networking-topology-section"></a>Networking topology (Hálózati topológia) szakasz
A **Networking topology** (Hálózati topológia) rész az erőforrások hierarchikus nézetét mutatja, ahogy az alábbi képernyőfelvételen is látható:

![Az erőforrások hierarchikus nézete a Hálózati topológia szakaszban](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

A táblázat elemei (a virtuális gépek és az alhálózatok) súlyosság szerint rendezve jelennek meg:

* Piros (legfelül): a legmagasabb prioritás, azonnali beavatkozást igényel
* Narancssárga: közepes szintű prioritás, a lehető legrövidebb időn belül beavatkozást igényel
* Zöld (utolsó): megfelelő állapot

A topológia e nézetében az első szinten vannak a [virtuális hálózatok](../virtual-network/virtual-networks-overview.md), a [virtuális hálózati átjárók](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) és a [ (klasszikus) virtuális hálózatok](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). A második szinthez tartoznak az alhálózatok, míg a harmadik szinten az ezekhez az alhálózatokhoz tartozó virtuális gépek. A jobb oldali oszlopban az adott erőforrásokhoz tartozó hálózati biztonsági csoport aktuális állapota található, ahogyan az alábbi példában is látható:

![A hálózati biztonsági csoport állapota a Networking topology (Hálózati topológia) szakaszban](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

A panel alsó részén a virtuális gépre vonatkozó javaslatok láthatók, a fentiekben leírtakhoz hasonlóan. A javaslatra kattintva további információkat tekinthet meg, illetve alkalmazhatók a szükséges biztonsági rendszabályok/konfigurációk.

### <a name="monitor-data-security"></a>Adatbiztonság monitorozása

A **Megelőzés** szakasz **Adatbiztonság** elemére kattintva megnyithatja az **Adatforrások** panelt, amelyen az SQL és a Storage használatával kapcsolatos javaslatokat olvashat. Ezenfelül általános [javaslatokat](security-center-sql-service-recommendations.md) is talál itt az adatbázis állapotára vonatkozóan. A tárolás titkosításáról további információkat az [Azure-tárfiókok titkosításának engedélyezése az Azure Security Centerben](security-center-enable-encryption-for-storage-account.md) című cikkben találhat.

![Adatforrások](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Az **SQL Recommendations** (Javaslatok az SQL használatával kapcsolatban) területen bármely javaslatra kattintva részletesebb információt kap a probléma megoldásához szükséges további intézkedésről. A következő példában a **Database Auditing & Threat detection on SQL databases** (Naplózás és fenyegetésészlelés az SQL-adatbázisokban) javaslat kibontott nézete látható.

![SQL-javaslat részletei](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Az **Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokban** panelen a következő információk láthatók:

* Az SQL-adatbázisok listája
* A kiszolgáló, amely tárolja ezeket
* Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
* Az aktuális állapot
* A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képernyőn látható módon megnyílik az **Naplózás és fenyegetésészlelés** panel.

![Naplózás és fenyegetésészlelés](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

A naplózás engedélyezéséhez módosítsa az **Auditing** (Naplózás) beállítását **ON** (BE) értékre.

### <a name="monitor-identity--access"></a>Identitás és hozzáférés monitorozása

További információkat az [identitás és hozzáférés az Azure Security Centerben való monitorozásáról](security-center-identity-access.md) szóló témakörben olvashat.

## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
