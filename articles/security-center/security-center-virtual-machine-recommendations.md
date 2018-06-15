---
title: Az Azure Security Centerben a virtuális gépek védelme |} Microsoft Docs
description: Ez a dokumentum címek, amelyek segítenek az Azure Security Center javaslatait a virtuális gépet véd, és maradnak meg a biztonsági házirendeknek megfelelően.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 54375f6f98b4989a7af8bcde649d967f77c6c862
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
ms.locfileid: "27623498"
---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Az Azure Security Centerben a virtuális gépek védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Amikor a Security Center a potenciális biztonsági hiányosságokat azonosít, ajánlásokat, amely végigvezeti Önt a szükséges szabályozási folyamatán hoz létre.  Javaslat alkalmazható azonban az Azure erőforrás típusa: virtuális gépek (VM), hálózati, SQL és alkalmazások.

Ez a cikk foglalkozik, amelyek érvényesek a virtuális gépekhez ajánlott.  VM javaslatok center körül adatgyűjtés kiépítése, rendszer frissítéseinek alkalmazása a virtuális gépek lemezei, és több titkosítását.  Az alábbi táblázatban használja hivatkozásként segít megérteni a rendelkezésre álló virtuális gépekre vonatkozó javaslatok, és hogy a minden egyes milyen fogja elvégezni, ha alkalmazza azt.

## <a name="available-vm-recommendations"></a>Rendelkezésre álló virtuális gépekre vonatkozó javaslatok
| Ajánlás | Leírás |
| --- | --- |
| [Adatgyűjtés engedélyezése az előfizetések számára](security-center-enable-data-collection.md) |Javasolja, hogy minden egyes előfizetés és virtuális gép (VM) esetében kapcsolja be az adatgyűjtést az előfizetéseinek biztonsági házirendjében. |
| [Engedélyezze a titkosítást az Azure Storage-fiók](security-center-enable-encryption-for-storage-account.md) | Az inaktív adatok Azure Storage szolgáltatás titkosítási engedélyezését javasolja. Storage Service Encryption (SSE) beolvasása előtt visszafejti és az Azure storage beíródik esetén az adatok titkosításával működik. SSE jelenleg csak az Azure Blob szolgáltatáshoz érhető el, és nem használható blokkblobokat, lapblobokat, és hozzáfűző blobokat. További tudnivalókért lásd: [Storage szolgáltatás titkosítási az inaktív adatok](../storage/common/storage-service-encryption.md).</br>SSE csak erőforrás-kezelő tárfiókok esetén támogatott. Klasszikus tárfiókokba jelenleg nem támogatottak. A klasszikus és Resource Manager üzembe helyezési modell ismertetése: [Azure üzembe helyezési modellel](../azure-classic-rm.md). |
| [Biztonsági beállításokkal javítása](security-center-remediate-os-vulnerabilities.md) |Javasolja, hogy a az operációs rendszer azon konfigurációit a javasolt biztonsági konfigurációs szabályokkal igazodnak például nem engedélyezik a jelszavak menteni. |
| [Rendszerfrissítések alkalmazása](security-center-apply-system-updates.md) |Javasolja, hogy végezze el a hiányzó rendszerbiztonsági és kritikus frissítések központi telepítését a virtuális gépeken. |
| [A közvetlenül időponthoz kötött alkalmazni a hálózati hozzáférés-vezérlés](security-center-just-in-time.md) | Csak a virtuális gép elérhető telepítését javasolja. A szolgáltatás egyelőre időben csak és érhető el a Security Center Standard csomagra. Lásd: [árazás](security-center-pricing.md) további bővebben a Security Center által tarifacsomag szükséges. |
| [Rendszerfrissítések utáni újraindítás](security-center-apply-system-updates.md#reboot-after-system-updates) |Javasolja, hogy a rendszerfrissítések alkalmazási folyamatának befejezéséhez indítson újra egy virtuális gépet. |
| [Endpoint Protection telepítése](security-center-install-endpoint-protection.md) |Javasolja, hogy telepítsen kártevőirtó programokat a virtuális gépekre (csak Windows rendszerű virtuális gépek esetében). |
| [Virtuálisgép-ügynök engedélyezése](security-center-enable-vm-agent.md) |Lehetővé teszi a virtuálisgép-ügynök alkalmazását igénylő virtuális gépek megtekintését. A virtuálisgép-ügynöknek telepítve kell lennie a virtuális gépeken a javítás- és alapkonfiguráció-keresés, valamint a kártevőirtó programok üzembe helyezéséhez. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) cikkben talál információkat. |
| [Lemeztitkosítás alkalmazása](security-center-apply-disk-encryption.md) |Javasolja, hogy végezze el a virtuális gép titkosítását az Azure Disk Encryption használatával (Windows és Linux rendszerű virtuális gépek esetében). A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen. |
| [Operációs rendszer verziójának frissítése](security-center-update-os-version.md) |Javasolja, hogy meg frissíteni az operációs rendszer verzió a felhőalapú szolgáltatáshoz az elérhető legújabb verzióra a operációsrendszer-család.  Cloud Services kapcsolatos további tudnivalókért tekintse meg a [felhőalapú szolgáltatások – áttekintés](../cloud-services/cloud-services-choose-me.md). |
| [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md) |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| [Sebezhetőségek javítása](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Lehetővé teszi a virtuális gépre telepített sebezhetőségfelmérő megoldás által észlelt rendszer- és alkalmazássebezhetőségek megtekintését. |

## <a name="see-also"></a>Lásd még
Egyéb Azure erőforrástípusok vonatkozó javaslatok kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az alkalmazások az Azure Security Centerben védelme](security-center-application-recommendations.md)
* [Az Azure Security Centerben a hálózat védelme](security-center-network-recommendations.md)
* [Az Azure Security Centerben az Azure SQL-szolgáltatás védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
