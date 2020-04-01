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
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473271"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Lemeztitkosítás alkalmazása az Azure Security Centerben

Az Azure Security Center azt javasolja, hogy az Azure Disk Encryption a titkosítatlan lemezeken a Windows és a Linux virtuális gép. A lemeztitkosítással windowsos és Linux IaaS virtuális gépek lemezei titkosíthatók.  A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen.

A Lemeztitkosítás a Windows szabványos [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját használja. Ezek a funkciók operációs rendszer- és adattitkosítást biztosítanak az adatok védelme és védelme, valamint a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítése érdekében. A Lemeztitkosítás integrálva van az [Azure Key Vaultszolgáltatással,](https://azure.microsoft.com/documentation/services/key-vault/) hogy segítsen a kulcstároló-előfizetés lemeztitkosítási kulcsainak és titkos kulcsainak vezérlésében és kezelésében, miközben biztosítja, hogy a virtuálisgép-lemezeken lévő összes adat inaktív módon titkosítva legyen az Azure [Storage-ban.](https://azure.microsoft.com/documentation/services/storage/)

A Windows és a Linux támogatott verzióinak listáját lásd: [Támogatott virtuális gépek és operációs rendszerek](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) az Azure Disk Encryption dokumentációban.

## <a name="implement-the-recommendation"></a>Az ajánlás végrehajtása
1. A **Javaslatok** lapon válassza a Lemeztitkosítás kell **alkalmazni a virtuális gépeken.**
2. A **nem megfelelő állapotú erőforrások közül**válasszon ki egy virtuális gép, amelylemez-titkosítás ajánlott.
3. Kövesse az utasításokat, hogy titkosítást alkalmazzon ezekre a virtuális gépekre.

![Lemeztitkosítás alkalmazása](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

A Security Center által titkosítást igénylőként azonosított Azure virtuális gépek titkosításához a következő lépéseket javasoljuk:

* Az Azure PowerShell telepítése és konfigurálása. Ez lehetővé teszi az Azure virtuális gépek titkosításához szükséges előfeltételek beállításához szükséges PowerShell-parancsok futtatását.
* Az Azure Disk Encryption sokkal előfeltételeinek beszerzése és futtatása Azure PowerShell-parancsfájl.
* Titkosítsa a virtuális gépeket.

[Windows IaaS virtuális gép titkosítása az Azure PowerShell használatával](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) – Végigvezeti ezeket a lépéseket, és feltételezi, hogy egy Windows-ügyfélgépet használ, amelyről lemeztitkosítást konfigurálhat.

Az Azure virtuális gépek számos módszer használható. Ha már jól ismeri az Azure PowerShell vagy az Azure CLI, akkor előfordulhat, hogy alternatív megközelítések használata. Az egyéb megközelítésekről az [Azure lemeztitkosítás a témakörben olvashat.](../security/fundamentals/encryption-overview.md)

## <a name="see-also"></a>Lásd még
Ez a dokumentum bemutatja, hogyan valósítható meg a Biztonsági központ "Lemeztitkosítás alkalmazása" javaslata. A lemeztitkosításról az:

* [Titkosítás és kulcskezelés az Azure Key Vault segítségével](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (videó, 36 perc 39 mp)- Ismerje meg, hogyan használhatja a lemeztitkosítás-kezelést az IaaS virtuális gépekhez és az Azure Key Vaulthoz az adatok védelme és védelme érdekében.
* [Azure lemeztitkosítás](../security/fundamentals/encryption-overview.md) (dokumentum)- Ismerje meg, hogyan engedélyezheti a lemeztitkosítást a Windows és linuxos virtuális gépek.