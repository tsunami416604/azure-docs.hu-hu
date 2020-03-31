---
title: Lemeztitkosítás alkalmazása az Azure Security Centerben | Microsoft dokumentumok
description: Ez a dokumentum bemutatja, hogyan valósítható meg az Azure Security Center javaslat **a lemeztitkosítás alkalmazása**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604523"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Lemeztitkosítás alkalmazása az Azure Security Centerben
Az Azure Security Center ajánlása szerint lemeztitkosítást kell alkalmazni olyan Windows vagy Linux rendszerű virtuális gépek esetén, amelyek nincsnek az Azure Disk Encryption használatával titkosítva. A lemeztitkosítással windowsos és Linux IaaS virtuális gépek lemezei titkosíthatók.  A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen.

A Lemeztitkosítás a Windows szabványos [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját használja. Ezek a funkciók operációs rendszer- és adattitkosítást biztosítanak az adatok védelme és védelme, valamint a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítése érdekében. A Lemeztitkosítás integrálva van az [Azure Key Vaultszolgáltatással,](https://azure.microsoft.com/documentation/services/key-vault/) hogy segítsen a kulcstároló-előfizetés lemeztitkosítási kulcsainak és titkos kulcsainak vezérlésében és kezelésében, miközben biztosítja, hogy a virtuálisgép-lemezeken lévő összes adat inaktív módon titkosítva legyen az Azure [Storage-ban.](https://azure.microsoft.com/documentation/services/storage/)

> [!NOTE]
> Az Azure Lemeztitkosítás a következő Windows server operációs rendszereken támogatott – Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2. A lemeztitkosítás a következő Linux-kiszolgálóoperációs rendszereken támogatott - Ubuntu, CentOS, SUSE és SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Az ajánlás végrehajtása
1. A **Javaslatok** panelen válassza a **Lemeztitkosítás alkalmazása**lehetőséget.
2. A **Lemeztitkosítás alkalmazása** panelen azoknak a virtuális gépeknek a listája jelenik meg, amelyekhez a lemeztitkosítás ajánlott.
3. Kövesse az utasításokat, hogy titkosítást alkalmazzon ezekre a virtuális gépekre.

![][1]

A Security Center által titkosítást igénylőként azonosított Azure virtuális gépek titkosításához a következő lépéseket javasoljuk:

* Az Azure PowerShell telepítése és konfigurálása. Ez lehetővé teszi az Azure virtuális gépek titkosításához szükséges előfeltételek beállításához szükséges PowerShell-parancsok futtatását.
* Az Azure Disk Encryption sokkal előfeltételeinek beszerzése és futtatása Azure PowerShell-parancsfájl.
* Titkosítsa a virtuális gépeket.

[A Windows IaaS virtuális gép titkosítása az Azure PowerShell-lel](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) végigvezeti ezeket a lépéseket. Ez a témakör feltételezi, hogy olyan Windows ügyfélgépet használ, amelyről lemeztitkosítást konfigurál.

Az Azure virtuális gépek számos módszer használható. Ha Ön jól ismeri az Azure PowerShell vagy az Azure parancssori felületét, érdemes lehet más megközelítést választani. Az egyéb megközelítésekről az [Azure lemeztitkosítás a témakörben olvashat.](../security/fundamentals/encryption-overview.md)

## <a name="see-also"></a>Lásd még
Ez a dokumentum bemutatja, hogyan valósítható meg a Biztonsági központ "Lemeztitkosítás alkalmazása" javaslata. A lemeztitkosításról az alábbi témakörben olvashat bővebben:

* [Titkosítás és kulcskezelés az Azure Key Vault segítségével](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (videó, 36 perc 39 mp) – Ismerje meg, hogyan használhatja a lemeztitkosítás-kezelést az IaaS virtuális gépekhez és az Azure Key Vaulthoz az adatok védelme és védelme érdekében.
* [Azure lemeztitkosítás](../security/fundamentals/encryption-overview.md) (dokumentum) - Ismerje meg, hogyan engedélyezheti a lemeztitkosítást a Windows és linuxos virtuális gépek.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ismerje meg, hogyan konfigurálhatja a biztonsági szabályzatokat.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti és válaszolhat azokra a biztonsági riasztásokra.
* [A biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Ismerje meg, hogyan segíthetnek a javaslatok az Azure-erőforrások védelmében.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések keresése az Azure biztonságáról és megfelelőségéről.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
