---
title: Titkosítás az Azure Backupban
description: Ismerje meg, hogy a Azure Backup titkosítási funkciói hogyan védik a biztonsági mentési adatait, és megfelelnek a vállalat biztonsági igényeinek.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: 099e736bfb321f0f92bd3a57f9c24e88293b42bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538751"
---
# <a name="encryption-in-azure-backup"></a>Titkosítás az Azure Backupban

Az Azure Storage encryption használatával a rendszer automatikusan titkosítja a biztonsági másolatok adatait a felhőben, ami segít a biztonsági és megfelelőségi kötelezettségek teljesítésében. Az inaktív adatok titkosítása 256 bites AES-titkosítással történik, amely az egyik legerősebb blokk titkosítási algoritmus, és az FIPS 140-2-kompatibilis.

Amellett, hogy a titkosítás a nyugalmi állapotban van, a rendszer az összes átviteli biztonsági mentési adatát HTTPS-kapcsolaton keresztül továbbítja. Mindig az Azure gerinc hálózatán marad.

További információ: az [Azure Storage titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md). A titkosítással kapcsolatban esetlegesen felmerülő kérdések megválaszolásához tekintse meg a [Azure Backup gyakori kérdések](./backup-azure-backup-faq.md#encryption) című témakört.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági másolatok titkosítása a platform által felügyelt kulcsokkal

Alapértelmezés szerint az összes adatait a platform által felügyelt kulcsok használatával titkosítja a rendszer. Semmilyen explicit műveletet nem kell tennie a végponttól a titkosítás engedélyezéséhez, és az összes olyan munkaterhelésre vonatkozik, amelyről biztonsági mentés készül a Recovery Services-tárba.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal

Az Azure-Virtual Machines biztonsági mentésekor mostantól titkosíthatja az adatait az Ön által birtokolt és felügyelt kulcsokkal. Azure Backup lehetővé teszi, hogy a Azure Key Vault tárolt RSA-kulcsait a biztonsági mentések titkosításához használja. A biztonsági mentések titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatait egy AES 256-alapú adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) védik, amely viszont a kulcsok használatával védett. Ez teljes körű irányítást biztosít az adat és a kulcsok felett. A titkosítás engedélyezéséhez szükség van arra, hogy a Recovery Services-tároló hozzáférhessen a Azure Key Vault található titkosítási kulcshoz. Ha szükséges, letilthatja a kulcsot, vagy visszavonhatja a hozzáférést. A titkosítást azonban a kulcsok használatával kell engedélyeznie, mielőtt bármilyen elemet a tárolóhoz próbál védelemmel ellátni.

További információk arról, hogyan titkosíthatja a biztonsági mentési adatait az ügyfél által [felügyelt kulcsokkal](encryption-at-rest-with-cmk.md).

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok használatával titkosított felügyelt lemezes virtuális gépek biztonsági mentése

A Azure Backup lehetővé teszi az Azure-beli virtuális gépek biztonsági mentését is, amelyek a kulcsot használják a [Storage szolgáltatás titkosításához](../storage/common/storage-service-encryption.md). A lemezek titkosításához használt kulcsot a Azure Key Vault tárolja és felügyeli. Storage Service Encryption (SSE) az ügyfél által felügyelt kulcsok használata különbözik a Azure Disk Encryptiontól, mivel az ADE a BitLocker (for Windows) és a DM-Crypt (for Linux) használatával végzi a vendég titkosítást, az SSE titkosítja az adatait a Storage szolgáltatásban, lehetővé téve a virtuális gépekhez tartozó operációs rendszerek vagy rendszerképek használatát. További részletekért tekintse meg a [felügyelt lemezek titkosítása az ügyfél által felügyelt kulcsokkal](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) című témakört.

## <a name="infrastructure-level-encryption-for-backup-data"></a>Infrastruktúra-szintű titkosítás a biztonsági mentési adatvédelemhez

A Recovery Services-tárolóban az ügyfél által felügyelt kulcsok használatával történő titkosítás mellett további titkosítási réteg is beállítható a tárolási infrastruktúrán. Ezt az infrastruktúra-titkosítást a platform felügyeli, és az ügyfél által felügyelt kulcsokkal együtt a REST titkosítással is lehetővé teszi a biztonsági mentési adatok kétrétegű titkosítását. Érdemes megjegyezni, hogy az infrastruktúra titkosítása csak akkor konfigurálható, ha először a saját kulcsokat szeretné használni a titkosításhoz a nyugalmi állapotban. Az infrastruktúra titkosítása platform által felügyelt kulcsokat használ az adattitkosításhoz.

>[!NOTE]
>Az infrastruktúra-titkosítás jelenleg korlátozott előzetes verzióban érhető el, és az USA keleti, USA-beli West2, az USA déli középső régiójában, US Gov Arizona és USA Ha a funkciót bármelyik régióban használni szeretné, kérjük, töltse ki [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) , és küldjön e-mailt a következő címre: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Az ADE használatával titkosított virtuális gépek biztonsági mentése

A Azure Backup segítségével biztonsági mentést készíthet az Azure-beli virtuális gépekről, amelyek az operációs rendszer vagy a Azure Disk Encryption használatával titkosított adatlemezekkel rendelkeznek. Az ADE a BitLockert használja a Windows rendszerű virtuális gépekhez és a Linux rendszerű virtuális gépekhez készült DM-Crypt titkosítást. Részletekért lásd: [a titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](./backup-azure-vms-encryption.md).

## <a name="next-steps"></a>Következő lépések

- [Titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](backup-azure-vms-encryption.md)
