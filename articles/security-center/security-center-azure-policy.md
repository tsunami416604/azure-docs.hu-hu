---
title: "Az Azure Security Center biztonsági szabályzatainak integrálása az Azure Policyvel | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti, hogyan konfigurálható az Azure Security Center biztonsági szabályzatainak integrálása az Azure Policyvel."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 5e07cd6891a5ab04012f819b5f6b9379312e530d
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center-powered-by-azure-policy"></a>Az Azure Policy által kezelt biztonsági szabályzatok beállítása a Security Centerben
Ez a dokumentum végigvezeti a Security Center Azure Policy által kezelt biztonsági szabályzatainak beállításához szükséges lépéseken. 


## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A szabályzatot a Security Centerben szerkesztheti, vagy az [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) használatával létrehozhat új szabályzatdefiníciókat, valamint hozzárendelhet szabályzatokat a Felügyeleti csoportokhoz (ezek jelenthetik a teljes céget vagy szervezetet, egy üzleti egységet stb.), és felügyelheti a szabályzatoknak való megfelelőséget.

> [!NOTE]
> Az Azure Policy jelenleg csak korlátozott előzetes verzióban érhető el. Kattintson [ide](https://aka.ms/getpolicy) a csatlakozáshoz. Az Azure-szabályzatokkal kapcsolatos további információkért olvassa el a [megfelelőség szabályzatok létrehozásával és kezelésével történő kikényszerítésével](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) foglalkozó témakört.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az egyes Azure-előfizetések alapértelmezett biztonsági szabályzatait a Security Centerben szerkesztheti. Biztonsági szabályzat módosításához az előfizetésben vagy az azt tartalmazó felügyeleti csoportban tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie. A Security Center biztonsági szabályzatainak megtekintéséhez jelentkezzen be az Azure Portalra, és kövesse az alábbi lépéseket:

1. A **Security Center** irányítópultjának **Általános** területén kattintson a **Biztonsági szabályzat** elemre.
2. Válassza ki azt az előfizetést, amelyhez engedélyezni kívánja a biztonsági szabályzatot.

    ![Szabályzatkezelés](./media/security-center-policies/security-center-policies-fig10.png)

3. A **SZABÁLYZAT ÖSSZETEVŐI** szakaszban kattintson a **Biztonsági szabályzat** elemre.

    ![Szabályzat összetevői](./media/security-center-policies/security-center-policies-fig12.png)

4. Ez a Security Centerhez az Azure Policyn keresztül hozzárendelt alapértelmezett szabályzat. Törölheti a **SZABÁLYZATOK ÉS PARAMÉTEREK** alatt felsorolt összetevőket, vagy új szabályzatdefiníciókat adhat meg az **ELÉRHETŐ LEHETŐSÉGEK** alatt. Ehhez kattintson egyszerűen a definíció neve mellett a plusz jelre.

    ![Szabályzatdefiníciók](./media/security-center-policies/security-center-policies-fig11.png)

5. Ha részletesebb ismertetőre van szüksége valamely szabályzattal kapcsolatban, kattintson a szabályzatra, és a megnyíló oldalon tekintse meg a részleteket és a [szabályzatdefiníció](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy/#policy-definition-structure) szerkezetű JSON-kódot:

    ![JSON](./media/security-center-policies/security-center-policies-fig14.png)

6. A szerkesztés befejeztével kattintson a **Mentés** gombra.


## <a name="available-security-policy-definitions"></a>Elérhető biztonságiszabályzat-definíciók

Az alapértelmezett biztonsági szabályzatban elérhető szabályzatdefiníciók megértéséhez használja az alábbi táblázatot: 

| Szabályzat | Bekapcsolt állapotban |
| --- | --- |
| System updates (Rendszerfrissítések) |Lekéri az elérhető biztonsági és kritikus frissítések napi listáját a Windows Update vagy a Windows Server Update Services szolgáltatástól. A lekért lista az adott virtuális gép számára beállított szolgáltatástól függ, és javasolja a hiányzó frissítések alkalmazását. Linux rendszerek esetében a szabályzat a disztribúció által biztosított csomagkezelő rendszert használja az elérhető frissítésekkel rendelkező csomagok meghatározásához. Az [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| OS vulnerabilities (Operációs rendszerek sebezhetőségei) |Naponta elemzi az operációs rendszer beállításait olyan problémák meghatározása érdekében, amelyek a virtuális gépet sebezhetővé tehetik a támadásokkal szemben. A szabályzat a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat is javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd a [javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (A Windows Server 2016 jelenleg nem részesül teljes mértékű támogatásban.) |
| Endpoint protection (Végpontok védelme) |A szabályzat az összes windowsos virtuális gép esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártékony szoftverek hatékonyabb azonosításához és eltávolításához. |
| Disk encryption (Lemeztitkosítás) |Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. |
| Network security groups (Hálózati biztonsági csoportok) |[Hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) konfigurálását ajánlja a be- és kimenő adatforgalom vezérlésére a nyilvános végpontokkal rendelkező virtuális gépek esetében. Az alhálózatra beállított hálózati biztonsági csoportokat az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. A hálózati biztonsági csoportok konfigurálásának figyelése mellett ez a szabályzat a bejövő biztonsági szabályokat is ellenőrzi, és azonosítja azokat a szabályokat, amelyek engedélyezik a bejövő forgalmat. |
| Web application firewall (Webalkalmazási tűzfal) |Webalkalmazási tűzfal kiépítését javasolja a virtuális gépeken, ha az alábbiak valamelyike teljesül: </br></br>A rendszer [példányszintű nyilvános IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP)-címet használ, és a kapcsolódó hálózati biztonsági csoportra vonatkozó bejövő biztonsági szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva.</br></br>A rendszer elosztott terhelésű IP-címet használ, és a kapcsolódó terheléselosztás és a hálózati címfordításra (NAT) vonatkozó bejövő szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva. (A további információkat az [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Az Azure Resource Manager támogatása a terheléselosztó számára)) című rész tartalmazza. |
| Next generation firewall (Új generációs tűzfal) |Az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felderíti az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés kiépítését. |
| SQL-naplózás és fenyegetésészlelés |Javasolja, hogy a megfelelés, a jobb fenyegetésészlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure Database-hozzáférések naplózását. |
| SQL-titkosítás |Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL Database számára, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan. Így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem fogják tudni olvasni. |
| Sebezhetőségi felmérés |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Storage-titkosítás |Ez a szolgáltatás jelenleg Azure-blobok és -fájlok számára érhető el. A Storage szolgáltatás titkosításának engedélyezése után csak az új adatok lesznek titkosítva, és a tárfiók meglévő fájljai titkosítatlanok maradnak. |
| JIT hálózati hozzáférés |Ha az igény szerinti hozzáférés engedélyezve van, a Security Center minden, az Azure-beli virtuális gépekre érkező forgalmat zárol egy NSG-szabály létrehozásával. Ön választja ki a virtuális gép azon portjait, amelyeken a beérkező forgalmat a rendszer zárolja. További információk: [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén). |


## <a name="next-step"></a>Következő lépés
Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md). A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md). A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md). A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
