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
ms.openlocfilehash: d43860d71b14bdac275df51695c9206539529171
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511480"
---
Ez a cikk az Azure Virtual Machines biztonsági javaslatait tartalmazza. Kövesse ezeket az ajánlásokat a modellben ismertetett biztonsági kötelezettségek teljesítéséhez a közös felelősség érdekében. A javaslatok a webalkalmazás-megoldások általános biztonságának javításához is segítséget nyújtanak. További információ arról, hogy a Microsoft miként teljesíti a szolgáltatói felelősségeket, lásd: [a felhőalapú számítástechnika megosztott feladatai](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

Ennek a cikknek a javaslatait a Azure Security Center automatikusan tudja kezelni. A Azure Security Center az Azure-beli erőforrásainak első védelmi vonala. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezt követően a biztonsági rések kezelésének módját ajánljuk. További információ: [biztonsági javaslatok a Azure Security Center](../articles/security-center/security-center-recommendations.md).

A Azure Security Centertel kapcsolatos általános információkért lásd: [Mi az Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Ajánlatok

| Kategória | Ajánlás | Megjegyzések | Security Center |
|-|-|----|--|
| Általános | Egyéni virtuálisgép-rendszerképek létrehozásakor alkalmazza a legújabb frissítéseket. | A lemezképek létrehozása előtt telepítse az operációs rendszer legújabb frissítéseit, és minden olyan alkalmazáshoz, amely a lemezkép részét képezi majd.  | - |
| Általános | Tartsa naprakészen a virtuális gépeket. | Az Azure-ban lévő Windows-és Linux-számítógépeken az operációs rendszer frissítéseinek kezeléséhez használhatja a Azure Automation [Update Management](../articles/automation/automation-update-management.md) megoldását. | [Igen](../articles/security-center/security-center-apply-system-updates.md) |
| Általános | Készítse elő a virtuális gépek biztonsági mentését. | [Azure Backup](../articles/backup/backup-overview.md) segít az alkalmazásadatok védelme terén, és minimális működési költségekkel jár. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. Azure Backup védi a Windows és a Linux rendszerű virtuális gépeket. | - |
| Általános | Több virtuális gép használata nagyobb rugalmasság és rendelkezésre állás érdekében. | Ha a virtuális gép olyan alkalmazásokat futtat, amelyek számára elérhetőnek kell lennie, használjon több virtuális gépet vagy [rendelkezésre állási készletet](../articles/virtual-machines/windows/manage-availability.md). | - |
| Általános | Egy üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiát kell alkalmaznia. | Azure Site Recovery lehetővé teszi az üzletmenet folytonosságának támogatására szolgáló különböző lehetőségek kiválasztását. Különböző replikációs és feladatátvételi forgatókönyveket támogat. További információ: [About site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |
| Identitás- és hozzáférés-kezelés | A virtuális gép hitelesítésének központosítása. | A Windows-és Linux-alapú virtuális gépek hitelesítését [Azure Active Directory hitelesítés](../articles/active-directory/develop/authentication-scenarios.md)használatával központosíthatja. | - |
| Adatbiztonság | Operációs rendszer lemezek titkosítása. | A [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) segítségével titkosíthatja Windows-és Linux-IaaS VM-lemezeit. A szükséges kulcsok nélkül a titkosított lemezek tartalma nem olvasható. A lemezes titkosítás védi a tárolt adatok jogosulatlan hozzáférését, amely egyébként a lemez másolásakor lenne lehetséges.| [Igen](../articles/security-center/security-center-apply-disk-encryption.md) |
| Adatbiztonság | Adatlemezek titkosítása. | A [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) segítségével titkosíthatja Windows-és Linux-IaaS VM-lemezeit. A szükséges kulcsok nélkül a titkosított lemezek tartalma nem olvasható. A lemezes titkosítás védi a tárolt adatok jogosulatlan hozzáférését, amely egyébként a lemez másolásakor lenne lehetséges.| -  |
| Adatbiztonság | A telepített szoftverek korlátozása. | A telepített szoftverek korlátozása a megoldás sikeres alkalmazásához szükséges értékre. Ez az útmutató segít csökkenteni a megoldás támadási felületét. | - |
| Adatbiztonság | Vírusirtó vagy antimalware használata. | Az Azure-ban olyan biztonsági gyártóktól származó kártevő szoftvereket is használhat, mint például a Microsoft, a Symantec, a Trend Micro és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a kártékony fájlokkal, a reklámprogramokkal és más fenyegetésekkel szemben. Az alkalmazás munkaterhelései alapján telepítheti a Microsoft antimalware-t. Használja az alapszintű biztonságos vagy a speciális egyéni konfigurációt. További információ: [Microsoft antimalware for Azure Cloud Services és Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Adatbiztonság | Kulcsok és titkos kódok biztonságos tárolása. | Egyszerűbbé teheti a titkok és kulcsok felügyeletét azáltal, hogy az alkalmazás tulajdonosait biztonságos, központilag felügyelt lehetőséggel látja el. Ez a kezelés csökkenti a véletlen sérülés vagy szivárgás kockázatát. Azure Key Vault biztonságosan tárolhatók a kulcsokat hardveres biztonsági modulokban (HSM), amelyek a 2. szintű FIPS 140-2-es szintre vannak hitelesítve. Ha a kulcsokat és titkos kulcsokat az FIPs 140,2 3. szintű használatával szeretné tárolni, használhatja az [Azure DEDIKÁLT HSM](../articles/dedicated-hsm/overview.md)-et. | - |
| Hálózatkezelés | A felügyeleti portok elérésének korlátozása. | A támadók nyilvános Felhőbeli IP-tartományokat vizsgálnak meg a nyílt felügyeleti portok számára, és megkísérlik a közös jelszavakat, valamint az ismert, nem javított biztonsági réseket. Igény szerinti [(JIT)](../articles/security-center/security-center-just-in-time.md) virtuálisgép-hozzáférést használhat az Azure-beli virtuális gépek bejövő forgalmának zárolására, így csökkentve a támadásoknak való kitettséget, miközben egyszerű kapcsolatokat biztosít a virtuális gépekhez, amikor szükségesek. | - |
| Hálózatkezelés | Korlátozza a hálózati hozzáférést. | A hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozását és a kitett végpontok számának szabályozását. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../articles/virtual-network/manage-network-security-group.md). | - |
| Monitoring | A virtuális gépek figyelése. | Az Azure-beli virtuális gépek és a virtuálisgép-méretezési csoportok állapotának figyeléséhez [Azure monitor for VMst](../articles/azure-monitor/insights/vminsights-overview.md) használhat. A virtuális gépekkel kapcsolatos teljesítményproblémák a szolgáltatás megszakadásához vezethetnek, ami sérti a rendelkezésre állás biztonsági elvét. | - |

## <a name="next-steps"></a>Következő lépések

A további biztonsági követelmények megismeréséhez forduljon az alkalmazás-szolgáltatóhoz. A biztonságos alkalmazások fejlesztésével kapcsolatos további információkért lásd: a [biztonságos fejlesztés dokumentációja](../articles/security/fundamentals/abstract-develop-secure-apps.md).
