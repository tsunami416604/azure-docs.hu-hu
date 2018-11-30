---
title: Az Azure Security Centerben lemeztitkosítás alkalmazása |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **lemeztitkosítás alkalmazása**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: c8a2caf2826069824a993294f4eba514ea870d9c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307375"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Az Azure Security Centerben lemeztitkosítás alkalmazása
Az Azure Security Center javasolja lemeztitkosítás alkalmazása Windows vagy Linux rendszerű virtuális gép lemez nem az Azure Disk Encryption használatával titkosított. Disk Encryption lehetővé teszi a Windows és Linux rendszerű IaaS VM-lemezek titkosítása.  A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen.

Lemeztitkosítás használja az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) Linux-szolgáltatás. Ezeket a funkciókat biztosítja, védelme és az adatok biztonságos, valamint biztonsági és megfelelőségi követelmények kielégítésével operációsrendszer- és titkosítását. Lemeztitkosítás integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a Key Vault-előfizetésből, miközben biztosítva, hogy a Virtuálisgép-lemezeken lévő összes adat található, aktívan a titkosítottak[Az azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Az Azure Disk Encryption a következő Windows server operációs rendszerek – Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2 esetében támogatott. Lemeztitkosítás támogatott a következő Linux server operációs rendszerek – Ubuntu, a CentOS, SUSE és SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **lemeztitkosítás alkalmazása**.
2. Az a **lemeztitkosítás alkalmazása** panelen láthatja, amelynek lemeztitkosítás javasolt virtuális gépek listáját.
3. Kövesse az utasításokat, ezek a virtuális gépek titkosítási alkalmazandó.

![][1]

Titkosítási kellene, mint a Security Center által azonosított Azure virtuális gépek titkosításához, a következő lépéseket javasoljuk:

* Az Azure PowerShell telepítése és konfigurálása. Ez lehetővé teszi, hogy az Azure virtuális gépek titkosításához szükséges előfeltételek beállításához szükséges PowerShell-parancsok futtatásával.
* Parancsprogram beszerzése és futtatása az Azure Disk Encryption Előfeltételek Azure PowerShell.
* A virtuális gépek titkosítása.

[Az Azure PowerShell használatával Windows IaaS virtuális gép titkosítása](../security/quick-encrypt-vm-powershell.md) ezeket a lépéseket mutatja be. Ez a témakör feltételezi, hogy egy Windows ügyfél gép, amelyről a lemeztitkosítás konfigurálásához használ.

Nincsenek az Azure Virtual Machines segítségével számos különböző módszer. Ha Ön jól ismeri az Azure PowerShell vagy az Azure parancssori felületét, érdemes lehet más megközelítést választani. Ezek a módszerek kapcsolatos további információkért lásd: [az Azure disk encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Lásd még
Ez a dokumentum láthatta, hogyan valósíthat meg a Security Center javaslatait "Alkalmaz lemeztitkosítás." Lemeztitkosítás kapcsolatos további információkért tekintse meg a következőket:

* [Az Azure Key Vault titkosítási és kulcskezelési](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (videó, 36 perc 39 mp-ben) – ismerje meg, hogyan használja a titkosítási IaaS virtuális gépek és az Azure Key Vault segítségével az adatok biztonságos megőrzésében.
* [Az Azure disk encryption](../security/azure-security-disk-encryption-overview.md) (dokumentum) – a Windows és Linux rendszerű virtuális gépek lemeztitkosítás engedélyezhető.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-azure-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
