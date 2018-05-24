---
title: Biztonsági szabályzatok beállítása az Azure Security Centerben | Microsoft Docs
description: Ebből a cikkből megismerheti az Azure Security Center biztonsági szabályzatainak konfigurálásához szükséges lépéseket.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: fed4a587d2258cdab35d09f5e3947a8cacade157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364386"
---
# <a name="set-security-policies-in-azure-security-center"></a>Biztonsági szabályzatok beállítása az Azure Security Centerben
Ebből a cikkből megismerheti a Security Center biztonsági szabályzatainak konfigurálásához szükséges lépéseket.

## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A Security Centerben szerkesztheti a szabályzatokat, és felügyelheti az azoknak való megfelelőséget.

> [!NOTE]
> A korlátozott előzetes verzióban elérhető Azure Policy használatával most kiterjesztheti a Security Center szabályzatait. Ha csatlakozni szeretne az előzetes verzióhoz, lépjen [az Azure Policyre való regisztrálással](https://aka.ms/getpolicy) kapcsolatos részhez. További információ: [A Security Center biztonsági szabályzatainak integrálása az Azure Policyvel](security-center-azure-policy.md).

A fejlesztésben vagy tesztelésben, illetve az éles környezetben használt erőforrásokra vonatkozó biztonsági követelmények eltérhetnek egymástól. A szabályozott adatokat, például személyazonosításra alkalmas adatokat használó alkalmazások magasabb szintű biztonságot követelhetnek meg. Az Azure Security Centerben engedélyezett biztonsági szabályzatok biztonsági javaslatokkal szolgálnak, és figyelést biztosítanak, így segítenek Önnek felismerni a potenciális biztonsági réseket és elhárítani a fenyegetéseket. Az [Azure Security Center tervezéséhez és működtetéséhez készült útmutatóból](security-center-planning-and-operations-guide.md) további segítséget nyújt az önnek legalkalmasabb lehetőség azonosításában.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az egyes Azure-előfizetések alapértelmezett biztonsági szabályzatait a Security Centerben szerkesztheti. Biztonsági szabályzat módosításához az előfizetésben tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie. A Security Center biztonsági szabályzatainak konfigurálásához tegye a következőket:

1. Jelentkezzen be az Azure portálra.

2. A **Security Center** irányítópultjának **Általános** területén válassza a **Biztonsági szabályzat** elemet.

3. Válassza ki azt az előfizetést, amelyhez biztonsági szabályzatot szeretne engedélyezni.

4. A **Szabályzat összetevői** szakaszban válassza a **Biztonsági szabályzat** elemet.  
    Ez a Security Centerhez hozzárendelt alapértelmezett szabályzat. Ki- vagy bekapcsolhatja az elérhető biztonsági javaslatokat.

5. Amikor befejezte a szerkesztést, válassza a **Mentés** elemet.

## <a name="available-security-policy-definitions"></a>Elérhető biztonságiszabályzat-definíciók

Az alapértelmezett biztonsági szabályzatban elérhető szabályzatdefiníciók megértéséhez tekintse át az alábbi táblázatot:

| Szabályzat | A szabályzat működése |
| --- | --- |
| System updates (Rendszerfrissítések) |Lekéri az elérhető biztonsági és kritikus frissítések napi listáját a Windows Update vagy a Windows Server Update Services szolgáltatástól. A lekért lista a virtuális gépek számára beállított szolgáltatástól függ, és javasolja a hiányzó frissítések alkalmazását. Linux rendszerek esetében a szabályzat a disztribúció által biztosított csomagkezelő rendszert használja az elérhető frissítésekkel rendelkező csomagok meghatározásához. Az [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| Biztonsági konfigurációk |Naponta elemzi az operációs rendszer beállításait olyan problémák meghatározása érdekében, amelyek a virtuális gépet sebezhetővé tehetik a támadásokkal szemben. A szabályzat a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat is javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd [a javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (A Windows Server 2016 jelenleg nem részesül teljes mértékű támogatásban.) |
| Endpoint protection (Végpontok védelme) |A szabályzat az összes windowsos virtuális gép (VM) esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártevőszoftverek hatékonyabb azonosításához és eltávolításához. |
| Disk encryption (Lemeztitkosítás) |Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. |
| Network security groups (Hálózati biztonsági csoportok) |[Hálózati biztonsági csoportok](../virtual-network/security-overview.md) konfigurálását ajánlja a be- és kimenő adatforgalom vezérlésére a nyilvános végpontokkal rendelkező virtuális gépek esetében. Az alhálózatra beállított hálózati biztonsági csoportokat az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. Annak figyelése mellett, hogy a hálózati biztonsági csoportok konfigurálása megtörtént-e, ez a szabályzat a bejövő biztonsági szabályokat is ellenőrzi, és azonosítja azokat a szabályokat, amelyek engedélyezik a bejövő forgalmat. |
| Web application firewall (Webalkalmazási tűzfal) |Webalkalmazási tűzfal beállítását javasolja a virtuális gépeken, ha az alábbiak valamelyike teljesül: <ul><li>A rendszer egy [példányszintű nyilvános IP-címet](../virtual-network/virtual-networks-instance-level-public-ip.md) használ, és a kapcsolódó hálózati biztonsági csoportra vonatkozó bejövő biztonsági szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva.</li><li>A rendszer elosztott terhelésű IP-címet használ, és a kapcsolódó terheléselosztás és a hálózati címfordításra (NAT) vonatkozó bejövő szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva. A további információkat az [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.</li> |
| Next generation firewall (Új generációs tűzfal) |Az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felderíti az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés beállítását. |
| SQL-naplózás és fenyegetésészlelés |Javasolja, hogy a megfelelőség és a jobb fenyegetésészlelés miatt, a támadások hatékonyabb kivizsgálása céljából engedélyezze az SQL Database-hozzáférések naplózását. |
| SQL-titkosítás |Javasolja, hogy engedélyezze az inaktív adatok titkosítását az SQL-adatbázisban, a kapcsolódó biztonsági mentésekben és a tranzakciós naplófájlokban. Így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem tudják olvasni. |
| Sebezhetőségi felmérés |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Storage-titkosítás |Ez a szolgáltatás jelenleg a blobokhoz és az Azure Fileshoz érhető el. A Storage szolgáltatás titkosításának engedélyezése után csak az új adatok lesznek titkosítva, és a tárfiók meglévő fájljai titkosítatlanok maradnak. |
| JIT hálózati hozzáférés |Ha az igény szerinti hálózati hozzáférés engedélyezve van, a Security Center minden, az Azure-beli virtuális gépekre érkező forgalmat zárol egy hálózati biztonsági csoport típusú szabály létrehozásával. Ön választja ki a virtuális gép azon portjait, amelyeken a beérkező forgalmat a rendszer zárolja. További információk: [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén). |


## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Az Azure Security Centerhez tartozó tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
