---
title: Lemez titkosításának alkalmazása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan valósítja meg a Azure Security Center a **lemez titkosítását**.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604523"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Lemez titkosításának alkalmazása Azure Security Center
Azure Security Center azt javasolja, hogy a lemez titkosítását akkor alkalmazza, ha olyan Windows vagy Linux rendszerű virtuálisgép-lemezekkel rendelkezik, amelyek nincsenek titkosítva Azure Disk Encryption használatával. A lemezek titkosítása lehetővé teszi a Windows-és Linux-IaaS virtuálisgép-lemezek titkosítását.  A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen.

A lemezes titkosítás a Windows iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását használja. Ezek a funkciók az operációs rendszer és az adattitkosítás biztosítására szolgálnak, amelyek segítenek megvédeni és védeni az adatait, és megfelelnek a szervezeti biztonsági és megfelelőségi kötelezettségeknek. A lemezes titkosítás integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) a lemezes titkosítási kulcsok és a titkos kódok kezeléséhez a Key Vault-előfizetésben, miközben biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek az [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)-ban.

> [!NOTE]
> A Azure Disk Encryption a következő Windows Server operációs rendszereken támogatott: Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2. A lemez titkosítása a következő Linux Server operációs rendszereken támogatott: Ubuntu, CentOS, SUSE és SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok** panelen válassza a **lemez titkosításának alkalmazása**lehetőséget.
2. A **lemez titkosításának alkalmazása** panelen megtekintheti azoknak a virtuális gépeknek a listáját, amelyekhez ajánlott a lemez titkosítása.
3. A virtuális gépek titkosításának alkalmazásához kövesse az utasításokat.

![][1]

A Security Center által azonosított Azure-Virtual Machines titkosításhoz való titkosításához a következő lépéseket javasoljuk:

* Az Azure PowerShell telepítése és konfigurálása. Ez lehetővé teszi az Azure-Virtual Machines titkosításához szükséges előfeltételek beállításához szükséges PowerShell-parancsok futtatását.
* Szerezze be és futtassa a Azure Disk Encryption előfeltételek Azure PowerShell parancsfájlt.
* Titkosítsa a virtuális gépeket.

[A Windows IAAS virtuális gépek titkosítása Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) végigvezeti Önt ezen lépések végrehajtásán. Ez a témakör azt feltételezi, hogy olyan Windows-ügyfélszámítógépet használ, amelyről a lemez titkosítását konfigurálja.

Számos módszer használható az Azure Virtual Machines. Ha Ön jól ismeri az Azure PowerShell vagy az Azure parancssori felületét, érdemes lehet más megközelítést választani. További információ ezekről az egyéb megközelítésekről: az [Azure Disk Encryption](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudhatta, hogyan implementálhatja a "lemez titkosításának alkalmazása" Security Center javaslatot. A lemezek titkosításával kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Titkosítás és kulcskezelő Azure Key Vaultokkal](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (videó, 36 perc 39 másodperc) – megtudhatja, hogyan használhatja a IaaS-alapú virtuális gépeket és az adatvédelmet a lemezes titkosítás kezelésével az adatai védelméhez és Azure Key Vault.
* [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) (dokumentum) – megtudhatja, hogyan engedélyezhető a lemez titkosítása Windows és Linux rendszerű virtuális gépeken.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – útmutató a biztonsági házirendek konfigurálásához.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
