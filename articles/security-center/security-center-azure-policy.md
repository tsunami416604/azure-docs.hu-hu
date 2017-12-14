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
ms.openlocfilehash: c359b77efbfdbcf7d2c0193dc015ce418a3d997d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>A Security Center biztonsági szabályzatainak integrálása az Azure Policyvel
Ez a cikk segítséget nyújt az Azure Security Center biztonsági szabályzatainak konfigurálásában, amelyek az Azure Policyre épülnek. 

## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A szabályzatokat szerkesztheti a Security Centerben, vagy az [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) segítségével elvégezheti a következőket:
* Új szabályzatdefiníciók létrehozása.
* Szabályzatok hozzárendelése felügyeleti csoportokhoz, amelyek jelölhetnek egy teljes szervezetet, vagy egy adott üzleti egységet a szervezeten belül.
* Szabályzatoknak való megfelelés figyelése.

> [!NOTE]
> Az Azure Policy jelenleg csak korlátozott előzetes verzióban érhető el. Ha csatlakozni szeretne, lépjen [az Azure Policyre való regisztrálással](https://aka.ms/getpolicy) kapcsolatos részhez. Az Azure Policyvel kapcsolatos további információkért olvassa el [a megfelelőség szabályzatok létrehozásával és kezelésével történő kikényszerítésével](http://docs.microsoft.com/azure/azure-policy/create-manage-policy) foglalkozó témakört.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az egyes Azure-előfizetések alapértelmezett biztonsági szabályzatait a Security Centerben szerkesztheti. Biztonsági szabályzat módosításához az előfizetésben vagy az azt tartalmazó felügyeleti csoportban tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie. A Security Centerben a biztonsági szabályzatok megtekintéséhez tegye a következőket:

1. Jelentkezzen be az Azure portálra.

2. A **Security Center** irányítópultjának **Általános** területén válassza a **Biztonsági szabályzat** elemet.

    ![A Szabályzatkezelés panel](./media/security-center-policies/security-center-policies-fig10.png)

3. Válassza ki azt az előfizetést, amelyhez biztonsági szabályzatot szeretne engedélyezni.  

4. A **Szabályzat összetevői** szakaszban válassza a **Biztonsági szabályzat** elemet.  
    Megnyílik az **Alapvető beállítások** ablak.

    ![Szabályzat összetevői](./media/security-center-policies/security-center-policies-fig12.png)

5. Szabályzatdefiníció törléséhez a **Szabályzatok és paraméterek** területen, a törölni kívánt definíció mellett válassza a **Törlés** elemet.

6. Kattintson a **Save** (Mentés) gombra.  
    Megnyílik az **Elérhető definíciók** ablak, amely a Security Centerhez az Azure Policyn keresztül hozzárendelt alapértelmezett szabályzatot jeleníti meg. 

7. (Nem kötelező) Az **Elérhető definíciók** ablakban a következő lehetőségek közül választhat:

    * Szabályzatdefiníció hozzáadásához válassza a definíció melletti a plusz jelet (+).

    ![Elérhető szabályzatdefiníciók](./media/security-center-policies/security-center-policies-fig11.png)

    * A szabályzatok részletes leírásáért válassza ki a kívánt szabályzatot.  
    Megnyílik egy **Előnézet** ablak a definícióhoz. Ez megjeleníti a definíció leírását és egy, a [szabályzatdefiníció](../azure-policy/policy-definition.md) struktúráját megadó JSON-kódra mutató hivatkozást.

    ![Előnézet ablak a definícióhoz](./media/security-center-policies/security-center-policies-fig14.png)

7. Amikor befejezte a szerkesztést, válassza a **Mentés** elemet.

## <a name="available-security-policy-definitions"></a>Elérhető biztonságiszabályzat-definíciók

Az alapértelmezett biztonsági szabályzatban elérhető szabályzatdefiníciók megértéséhez tekintse át az alábbi táblázatot: 

| Szabályzat | Az engedélyezett szabályzat működése |
| --- | --- |
| System updates (Rendszerfrissítések) |Lekéri az elérhető biztonsági és kritikus frissítések napi listáját a Windows Update vagy a Windows Server Update Services szolgáltatástól. A lekért lista a virtuális gépek számára beállított szolgáltatástól függ, és javasolja a hiányzó frissítések alkalmazását. Linux rendszerek esetében a szabályzat a disztribúció által biztosított csomagkezelő rendszert használja az elérhető frissítésekkel rendelkező csomagok meghatározásához. Az [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| OS vulnerabilities (Operációs rendszerek sebezhetőségei) |Naponta elemzi az operációs rendszer beállításait olyan problémák meghatározása érdekében, amelyek a virtuális gépet sebezhetővé tehetik a támadásokkal szemben. A szabályzat a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat is javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd [a javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (A Windows Server 2016 jelenleg nem részesül teljes mértékű támogatásban.) |
| Endpoint protection (Végpontok védelme) |A szabályzat az összes windowsos virtuális gép (VM) esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártevőszoftverek hatékonyabb azonosításához és eltávolításához. |
| Disk encryption (Lemeztitkosítás) |Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. |
| Network security groups (Hálózati biztonsági csoportok) |[Hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) konfigurálását ajánlja a be- és kimenő adatforgalom vezérlésére a nyilvános végpontokkal rendelkező virtuális gépek esetében. Az alhálózatra beállított hálózati biztonsági csoportokat az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. Annak figyelése mellett, hogy a hálózati biztonsági csoportok konfigurálása megtörtént-e, ez a szabályzat a bejövő biztonsági szabályokat is ellenőrzi, és azonosítja azokat a szabályokat, amelyek engedélyezik a bejövő forgalmat. |
| Web application firewall (Webalkalmazási tűzfal) |Webalkalmazási tűzfal beállítását javasolja a virtuális gépeken, ha az alábbiak valamelyike teljesül: <ul><li>A rendszer egy [példányszintű nyilvános IP-címet](../virtual-network/virtual-networks-instance-level-public-ip.md) használ, és a kapcsolódó hálózati biztonsági csoportra vonatkozó bejövő biztonsági szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva.</li><li>A rendszer elosztott terhelésű IP-címet használ, és a kapcsolódó terheléselosztás és a hálózati címfordításra (NAT) vonatkozó bejövő szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva. A további információkat az [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.</li> |
| Next generation firewall (Új generációs tűzfal) |Az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felderíti az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés beállítását. |
| SQL-naplózás és fenyegetésészlelés |Javasolja, hogy a megfelelőség, a jobb fenyegetésészlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure Database-hozzáférések naplózását. |
| SQL-titkosítás |Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL Database számára, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan. Így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem tudják olvasni. |
| Sebezhetőségi felmérés |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Storage-titkosítás |Ez a szolgáltatás jelenleg az Azure Blob Storage-hoz és az Azure Fileshoz érhető el. A Storage szolgáltatás titkosításának engedélyezése után csak az új adatok lesznek titkosítva, és a tárfiók meglévő fájljai titkosítatlanok maradnak. |
| JIT hálózati hozzáférés |Ha az igény szerinti hálózati hozzáférés engedélyezve van, a Security Center minden, az Azure-beli virtuális gépekre érkező forgalmat zárol egy hálózati biztonsági csoport típusú szabály létrehozásával. Ön választja ki a virtuális gép azon portjait, amelyeken a beérkező forgalmat a rendszer zárolja. További információk: [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén). |


## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Az Azure Security Centerhez tartozó tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
