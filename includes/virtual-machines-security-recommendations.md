---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: df577ab2b5e9658fd55324c8fd6fd008621b4d46
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545895"
---
Ez a cikk az Azure virtuális gépek biztonsági javaslatait tartalmazza. Kövesse ezeket az ajánlásokat, hogy segítsen teljesíteni a közös felelősségre vonatkozó modellünkben leírt biztonsági kötelezettségeket. A javaslatok a webalkalmazás-megoldások általános biztonságának javítását is elősegítik. A rról, hogy a Microsoft mit tesz a szolgáltatói feladatok teljesítéséért, olvassa [el a Megosztott felelősségi körök a felhőalapú számítástechnikával kapcsolatban című témakört.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)

A cikk néhány javaslata automatikusan megoldható az Azure Security Center által. Az Azure Security Center az azure-beli erőforrások első védelmi vonala. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a potenciális biztonsági rések azonosításához. Ezután javaslatot tetse a biztonsági rések megszüntetésének. További információ: [Biztonsági javaslatok az Azure Security Centerben.](../articles/security-center/security-center-recommendations.md)

Az Azure Security Centerről a [Mi az Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>Általános kérdések

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Amikor egyéni virtuálisgép-lemezképeket hoz létre, alkalmazza a legújabb frissítéseket. | A lemezképek létrehozása előtt telepítse a legújabb frissítéseket az operációs rendszerhez és az összes olyan alkalmazáshoz, amely a lemezkép része lesz.  | - |
| Tartsa naprakészen a virtuális gépeket. | Az Azure Automation [frissítéskezelési](../articles/automation/automation-update-management.md) megoldásával kezelheti az Azure-beli Windows- és Linux-számítógépek operációsrendszer-frissítéseit. | [Igen](../articles/security-center/security-center-apply-system-updates.md) |
| Biztonsági másolatot a virtuális gépekről. | [Az Azure Backup](../articles/backup/backup-overview.md) segít megvédeni az alkalmazásadatokat, és minimális működési költségekkel rendelkezik. Az alkalmazáshibák megsérülhetnek az adatokban, és az emberi hibák hibákat okozhatnak az alkalmazásokban. Az Azure Backup védi a Windows és Linux operációs rendszert futó virtuális gépeket. | - |
| Több virtuális gép használata a nagyobb rugalmasság és a rendelkezésre állás érdekében. | Ha a virtuális gép olyan alkalmazásokat futtat, amelyeknek magas rendelkezésre állásúnak kell lenniük, használjon több virtuális gépet vagy [rendelkezésre állási készletet.](../articles/virtual-machines/windows/manage-availability.md) | - |
| Üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégia elfogadása. | Az Azure Site Recovery lehetővé teszi, hogy válasszon a különböző lehetőségek et, amelyek célja, hogy támogassa az üzletmenet folytonosságát. Támogatja a különböző replikációs és feladatátvételi forgatókönyvek. További információt a [Webhely-helyreállítás – például a Webhely helyreállítása című témakörben talál.](../articles/site-recovery/site-recovery-overview.md) | - |

## <a name="data-security"></a>Adatbiztonság

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Az operációs rendszer lemezeinek titkosítása. | [Az Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) segítségével titkosíthatja a Windows és a Linux IaaS virtuálisgép-lemezeket. A szükséges kulcsok nélkül a titkosított lemezek tartalma olvashatatlan. A lemeztitkosítás megvédi a tárolt adatokat az illetéktelen hozzáféréstől, amely egyébként lehetséges lenne, ha a lemezt másolná.| [Igen](../articles/security-center/security-center-apply-disk-encryption.md) |
| Adatlemezek titkosítása. | [Az Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) segítségével titkosíthatja a Windows és a Linux IaaS virtuálisgép-lemezeket. A szükséges kulcsok nélkül a titkosított lemezek tartalma olvashatatlan. A lemeztitkosítás megvédi a tárolt adatokat az illetéktelen hozzáféréstől, amely egyébként lehetséges lenne, ha a lemezt másolná.| -  |
| Korlátozza a telepített szoftvert. | Korlátozza a telepített szoftvert a megoldás sikeres alkalmazásához szükséges mennyiségre. Ez az útmutató segít csökkenteni a megoldás támadási felületét. | - |
| Használjon víruskeresőt vagy kártevőirtót. | Az Azure-ban használhat kártevőirtó szoftvert olyan biztonsági gyártóktól, mint a Microsoft, a Symantec, a Trend Micro és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a rosszindulatú fájloktól, adware-ektől és egyéb fenyegetésektől. A Microsoft Antimalware az alkalmazás számítási feladatai alapján telepíthető. Használja az alapértelmezett alapvető biztonságos vagy a speciális egyéni konfigurációt. További információ: [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Biztonságosan tárolja a kulcsokat és a titkokat. | Egyszerűsítse a titkos kulcsok és kulcsok kezelését azáltal, hogy biztonságos, központilag felügyelt lehetőséget biztosít az alkalmazástulajdonosok számára. Ez a kezelés csökkenti a véletlen kisérülés vagy szivárgás kockázatát. Az Azure Key Vault biztonságosan tárolhatja a kulcsokat a FIPS 140-2 level 2 minősítésű hardveres biztonsági modulokban (HSM-ekben). Ha a 140.2-es [3.](../articles/dedicated-hsm/overview.md) | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés 

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Virtuálisgép-hitelesítés központosítása. | Az [Azure Active Directory-hitelesítéssel](../articles/active-directory/develop/authentication-scenarios.md)központosíthatja a Windows- és Linux-alapú virtuális gépek hitelesítését. | - |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A virtuális gépek figyelése. | Az [Azure Monitor virtuális gépekhez](../articles/azure-monitor/insights/vminsights-overview.md) segítségével figyelheti az Azure virtuális gépek és a virtuális gép méretezési csoportjai állapotát. A virtuális gép teljesítményével kapcsolatos problémák szolgáltatáskimaradáshoz vezethetnek, ami sérti a rendelkezésre állás biztonsági elvét. | - |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Korlátozza a felügyeleti portokhoz való hozzáférést. | A támadók a nyilvános felhő IP-tartományait kutatják át a nyitott felügyeleti portok után, és "egyszerű" támadásokat kísérelnek meg, például gyakori jelszavakat és ismert, nem javított biztonsági réseket. A [just-in-time (JIT) virtuális gép-hozzáférés](../articles/security-center/security-center-just-in-time.md) használatával zárolhatja az Azure virtuális gépek beérkező forgalmat, csökkentve a támadásoknak való kitettséget, miközben szükség esetén egyszerű kapcsolatot biztosít a virtuális gépekhez. | - |
| Korlátozza a hálózati hozzáférést. | A hálózati biztonsági csoportok lehetővé teszik a hálózati hozzáférés korlátozását és a kitett végpontok számának szabályozását. További információt a [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../articles/virtual-network/manage-network-security-group.md)című témakörben talál. | - |

## <a name="next-steps"></a>További lépések

További biztonsági követelményekről érdeklődjön az alkalmazás szolgáltatójánál. A biztonságos alkalmazások fejlesztéséről a [Biztonságos fejlesztési dokumentáció](../articles/security/fundamentals/abstract-develop-secure-apps.md)című témakörben olvashat bővebben.
