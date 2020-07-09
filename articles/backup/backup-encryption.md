---
title: Titkosítás Azure Backup
description: Ismerje meg, hogy a Azure Backup titkosítási funkciói hogyan védik a biztonsági mentési adatait, és megfelelnek a vállalat biztonsági igényeinek.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: ca570cfdc6e78e712715ba075168f4b06c55e4af
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116556"
---
# <a name="encryption-in-azure-backup"></a>Titkosítás Azure Backup

Az Azure Storage encryption használatával a rendszer automatikusan titkosítja a biztonsági másolatok adatait a felhőben, ami segít a biztonsági és megfelelőségi kötelezettségek teljesítésében. Az inaktív adatok titkosítása 256 bites AES-titkosítással történik, amely az egyik legerősebb blokk titkosítási algoritmus, és az FIPS 140-2-kompatibilis.

Amellett, hogy a titkosítás a nyugalmi állapotban van, a rendszer az összes átviteli biztonsági mentési adatát HTTPS-kapcsolaton keresztül továbbítja. Mindig az Azure gerinc hálózatán marad.

További információ: az [Azure Storage titkosítása inaktív adatokhoz](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). A titkosítással kapcsolatban esetlegesen felmerülő kérdések megválaszolásához tekintse meg a [Azure Backup gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) című témakört.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági másolatok titkosítása a platform által felügyelt kulcsokkal

Alapértelmezés szerint az összes adatait a platform által felügyelt kulcsok használatával titkosítja a rendszer. Semmilyen explicit műveletet nem kell tennie a végponttól a titkosítás engedélyezéséhez, és az összes olyan munkaterhelésre vonatkozik, amelyről biztonsági mentés készül a Recovery Services-tárba.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal

Az Azure-Virtual Machines biztonsági mentésekor mostantól titkosíthatja az adatait az Ön által birtokolt és felügyelt kulcsokkal. Azure Backup lehetővé teszi, hogy a Azure Key Vault tárolt RSA-kulcsait a biztonsági mentések titkosításához használja. A biztonsági mentések titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatait egy AES 256-alapú adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) védik, amely viszont a kulcsok használatával védett. Ez teljes körű irányítást biztosít az adat és a kulcsok felett. A titkosítás engedélyezéséhez szükség van arra, hogy a Recovery Services-tároló hozzáférhessen a Azure Key Vault található titkosítási kulcshoz. Ha szükséges, letilthatja a kulcsot, vagy visszavonhatja a hozzáférést. A titkosítást azonban a kulcsok használatával kell engedélyeznie, mielőtt bármilyen elemet a tárolóhoz próbál védelemmel ellátni.

>[!NOTE]
>Ez a szolgáltatás jelenleg korlátozott rendelkezésre állású. Kérjük, töltse ki [ezt a kérdőívet](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) , és küldjön nekünk e-mailt, AskAzureBackupTeam@microsoft.com Ha az ügyfél által felügyelt kulcsok használatával szeretné titkosítani a biztonsági mentési adatait. Vegye figyelembe, hogy a funkció használatának lehetősége a Azure Backup szolgáltatás jóváhagyását képezi.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok használatával titkosított felügyelt lemezes virtuális gépek biztonsági mentése

A Azure Backup lehetővé teszi az Azure-beli virtuális gépek biztonsági mentését is, amelyek a kulcsot használják a [Storage szolgáltatás titkosításához](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). A lemezek titkosításához használt kulcsot a Azure Key Vault tárolja és felügyeli. Storage Service Encryption (SSE) az ügyfél által felügyelt kulcsok használata különbözik a Azure Disk Encryptiontól, mivel az ADE a BitLocker (for Windows) és a DM-Crypt (for Linux) használatával végzi a vendég titkosítást, az SSE titkosítja az adatait a Storage szolgáltatásban, lehetővé téve a virtuális gépekhez tartozó operációs rendszerek vagy rendszerképek használatát. További részletekért tekintse meg a [felügyelt lemezek titkosítása az ügyfél által felügyelt kulcsokkal](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) című témakört.

## <a name="infrastructure-level-encryption-for-backup-data"></a>Infrastruktúra-szintű titkosítás a biztonsági mentési adatvédelemhez

A Recovery Services-tárolóban az ügyfél által felügyelt kulcsok használatával történő titkosítás mellett további titkosítási réteg is beállítható a tárolási infrastruktúrán. Ezt az infrastruktúra-titkosítást a platform felügyeli, és az ügyfél által felügyelt kulcsokkal együtt a REST titkosítással is lehetővé teszi a biztonsági mentési adatok kétrétegű titkosítását. Érdemes megjegyezni, hogy az infrastruktúra titkosítása csak akkor konfigurálható, ha először a saját kulcsokat szeretné használni a titkosításhoz a nyugalmi állapotban. Az infrastruktúra titkosítása platform által felügyelt kulcsokat használ az adattitkosításhoz.

>[!NOTE]
>Az infrastruktúra-titkosítás jelenleg korlátozott előzetes verzióban érhető el, és csak az USA keleti, USA West2 és USA déli középső régiójában érhető el. Ha a funkciót bármelyik régióban használni szeretné, kérjük, töltse ki [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) , és küldjön e-mailt a következő címre: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Az ADE használatával titkosított virtuális gépek biztonsági mentése

A Azure Backup segítségével biztonsági mentést készíthet az Azure-beli virtuális gépekről, amelyek az operációs rendszer vagy a Azure Disk Encryption használatával titkosított adatlemezekkel rendelkeznek. Az ADE a BitLockert használja a Windows rendszerű virtuális gépekhez és a Linux rendszerű virtuális gépekhez készült DM-Crypt titkosítást. Részletekért lásd: [a titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Következő lépések

- [Titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](backup-azure-vms-encryption.md)
