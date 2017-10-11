---
title: "Alkalmazza az adatok titkosítása az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás ** lemez titkosítási ** alkalmazni."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Az Azure Security Centerben lemeztitkosítás alkalmazása
Az Azure Security Center azt javasolja, hogy a lemez titkosítása alkalmazni, ha a Windows vagy Linux rendszerű virtuális lemezek nem titkosított Azure Disk Encryption használatával. Adatok titkosítása lehetővé teszi a Windows és Linux rendszerű infrastruktúra-szolgáltatási virtuális lemezek titkosításához.  A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen.

Lemeztitkosítás használja az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows-szolgáltatás és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) Linux szolgáltatása. Ezeket a szolgáltatásokat adja meg az operációs rendszer és az adatok titkosítása védelmének elősegítésére és az adatok védelme a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítésére. Lemeztitkosítás integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítséget vezérlésére és felügyeletére, a lemez titkosítási kulcsok és titkos kulcsokat a Key Vault előfizetésben, miközben a győződjön meg arról, hogy a virtuális gépek lemezei az összes adat titkosítva legyenek-e aktívan a [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Az Azure Disk Encryption támogatott a következő Windows server operációs rendszerek – Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2. Adatok titkosítása a következő Linux server operációs rendszereken támogatott - Ubuntu, CentOS, SUSE és SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **lemeztitkosítás alkalmazása**.
2. Az a **lemeztitkosítás alkalmazása** panelen, amelynek lemeztitkosítás ajánlott virtuális gépek listájának megtekintéséhez.
3. Kövesse az utasításokat, titkosítási alkalmazása a virtuális gépeken.

![][1]

Mint a titkosítást igénylő Security Center által azonosított Azure virtuális gépek titkosításához, a következő lépéseket javasoljuk:

* Az Azure PowerShell telepítése és konfigurálása. Ez lehetővé teszi, hogy az Azure virtuális gépek titkosításához szükséges előfeltételek beállításához szükséges PowerShell-parancsok futtatásához.
* Szerezze be és futtassa az Azure lemez titkosítási Előfeltételek Azure PowerShell-parancsfájlt.
* A virtuális gépek titkosítása.

[Azure virtuális gép titkosítása](security-center-disk-encryption.md) végigvezeti a lépéseken.  Ez a témakör feltételezi, hogy a Windows 10, az ügyfélszámítógépen, amelyen lemeztitkosítás konfigurálhat.

Nincsenek számos különböző módszer használható az Azure virtuális gépek. Ha Ön jól ismeri az Azure PowerShell vagy az Azure parancssori felületét, érdemes lehet más megközelítést választani. Ezek a módszerek, lásd: [Azure disk encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Lásd még:
Ez a dokumentum bemutatta megvalósításához a Security Center javaslat "Alkalmaz lemeztitkosítás." Lemeztitkosítás kapcsolatos további tudnivalókért olvassa el a következőket:

* [Titkosítás és a kulcs-kezelés az Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (videó, 36 perc 39 másodperc) – megtudhatja, hogyan védheti és az adatok védelme titkosítás Lemezkezelés IaaS virtuális gépek és az Azure Key Vault használatával.
* [Azure virtuális gép titkosítása](security-center-disk-encryption.md) (dokumentumok) – útmutató: Azure virtuális gépek titkosításához.
* [Az Azure lemeztitkosítás](../security/azure-security-disk-encryption.md) (dokumentumok) – útmutató a Windows és Linux virtuális gépek lemez-titkosítás engedélyezéséhez.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – megtudhatja, hogyan konfigurálhatja a biztonsági szabályzatokat.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
