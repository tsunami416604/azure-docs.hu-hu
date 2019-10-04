---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163942"
---
Ez a cikk az Azure Virtual Machines biztonsági javaslatait tartalmazza. A javaslatok megvalósítása a közös felelősségi modellben leírtak szerint biztosítja a biztonsági kötelezettségek teljesítését, és javítja a webalkalmazás-megoldások általános biztonságát. Ha többet szeretne megtudni arról, hogy a Microsoft hogyan teljesíti a szolgáltatói feladatokat, olvassa el a [megosztott felelősségek a felhőalapú számítástechnika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)terén című témakört.

A cikkben szereplő ajánlások némelyikét a Azure Security Center automatikusan nyomon követheti. A Azure Security Center az Azure-beli erőforrások védelmének első védelmi vonala. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a megoldására.

- Azure Security Center javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok a Azure Security Centerban](../articles/security-center/security-center-recommendations.md).
- További információ az Azure Security Centerről: [What is Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Javaslatok

| Category | Ajánlás | Megjegyzések | Security Center |
|-|-|----|--|
| Általános | Egyéni virtuálisgép-rendszerképek létrehozásakor használja a legújabb frissítéseket | A lemezképek létrehozása előtt győződjön meg arról, hogy az operációs rendszer és a lemezkép részét képező összes alkalmazás telepítve van a legújabb frissítésekkel.  | - |
| Általános | Tartsa naprakészen a virtuális gépeket | Az Azure-beli Windows-és Linux-számítógépeken az operációs rendszer frissítéseinek kezeléséhez használhatja a Azure Automation [Update Management](../articles/automation/automation-update-management.md) megoldását | [Igen](../articles/security-center/security-center-apply-system-updates.md) |
| Általános | Virtuális gépek biztonsági mentése | A [Azure Backup](../articles/backup/backup-overview.md) minimális működési költségekkel segíti az alkalmazás adatainak védelme. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. A Azure Backup a Windows és a Linux rendszerű virtuális gépek védettek. | - |
| Általános | Több virtuális gép használata nagyobb rugalmasság és rendelkezésre állás érdekében | Ha a virtuális gép magas rendelkezésre állást igénylő alkalmazásokat futtat, több virtuális gépnek vagy [rendelkezésre állási csoportnak](../articles/virtual-machines/windows/manage-availability.md) kell lennie | - |
| Általános | Üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégia bevezetése | Azure Site Recovery lehetővé teszi az üzletmenet-folytonossági igények kielégítésére szolgáló különböző beállítások kiválasztását. Különböző replikációkat és feladatátvételi feladatokat támogat. További információ: [About site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| Identitás- és hozzáférés-kezelés | Virtuális gép hitelesítésének központosítása | A Windows-és Linux-alapú virtuális gépek hitelesítését az [Azure ad-hitelesítés](../articles/active-directory/develop/authentication-scenarios.md)használatával központosíthatja. | - |
| Adatbiztonság | Operációsrendszer-lemezek titkosítása | A [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) segítségével titkosíthatja a Windows-és Linux-IaaS virtuális gépek lemezeit. A titkosító lemezek a szükséges kulcsok nélkül nem olvashatók be a tartalomba. A lemezes titkosítás védi a tárolt adatok jogosulatlan hozzáférését, amely egyébként a lemez másolásakor lenne lehetséges.| [Igen](../articles/security-center/security-center-apply-disk-encryption.md) |
| Adatbiztonság | Adatlemezek titkosítása | A [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) segítségével titkosíthatja a Windows-és Linux-IaaS virtuális gépek lemezeit. A titkosító lemezek a szükséges kulcsok nélkül nem olvashatók be a tartalomba. A lemezes titkosítás védi a tárolt adatok jogosulatlan hozzáférését, amely egyébként a lemez másolásakor lenne lehetséges.| -  |
| Adatbiztonság | Telepített szoftverek korlátozása | A telepített szoftverek a megoldás sikeres megvalósításához szükséges korlátozásával csökkentheti a megoldás támadási felületét | - |
| Adatbiztonság | Vírusirtó/antimalware használata | Az Azure-ban a Microsoft, a Symantec, a Trend Micro és a Kaspersky kártevő szoftvereket is használhat. Ez a szoftver segít megvédeni a virtuális gépeket a kártékony fájlokkal, a reklámprogramokkal és más fenyegetésekkel szemben. Az alkalmazás munkaterhelései alapján telepítheti a Microsoft antimalware-védelmet, amely alapszintű, biztonsági vagy speciális egyéni konfigurációval rendelkezik. Az Azure-hoz készült Microsoft antimalware szolgáltatással kapcsolatos további információkért lásd: [Microsoft antimalware for azure Cloud Services és Virtual Machines](../articles/security/azure-security-antimalware.md) | - |
| Adatbiztonság | Kulcsok és titkos kódok biztonságos tárolása | A titkok és kulcsok felügyeletének egyszerűsítése azáltal, hogy az alkalmazás tulajdonosai biztonságos, központilag felügyelt lehetőséget biztosítanak, lehetővé teszi a véletlen vagy szivárgások kockázatának csökkentését. A Azure Key Vault biztonságosan tárolhatják a kulcsokat a 2. szintű FIPS 140-2 szabványnak minősített hardveres biztonsági modulokban (HSM). Ha a kulcsokat és a titkokat a 3. szintű FIPs 140,2-es szinttel kell tárolnia, használhatja az [Azure DEDIKÁLT HSM](../articles/dedicated-hsm/overview.md) -t | - |
| Hálózat | Felügyeleti portok hozzáférésének korlátozása | A támadók nyilvános Felhőbeli IP-tartományokat vizsgálnak meg a nyílt felügyeleti portok számára, és megkísérlik a közös jelszavakat, valamint az ismert, nem javított biztonsági réseket. Az igény szerinti [(JIT) virtuálisgép-hozzáférés](../articles/security-center/security-center-just-in-time.md) lehetővé teszi az Azure-beli virtuális gépek bejövő forgalmának zárolását, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. | - |
| Hálózat | Hálózati hozzáférés korlátozása | A hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozását és a kitett végpontok számának szabályozását. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../articles/virtual-network/manage-network-security-group.md) | - |
| Figyelés | Virtuális gépek figyelése | Az [Azure monitor for VM](../articles/azure-monitor/insights/vminsights-overview.md) szolgáltatással figyelheti az Azure-beli virtuális gépek és a virtuálisgép-méretezési csoportok állapotát. A virtuális gépekkel kapcsolatos teljesítményproblémák a szolgáltatás megszakadásához vezethetnek, ami sérti a rendelkezésre állás biztonsági elvét. | - |

## <a name="next-steps"></a>További lépések

Érdeklődjön az alkalmazás szolgáltatójánál, hogy vannak-e további biztonsági követelmények. A biztonságos alkalmazások fejlesztésével kapcsolatos további információkért lásd a [biztonságos fejlesztési dokumentációt](../articles/security/fundamentals/abstract-develop-secure-apps.md).