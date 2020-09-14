---
title: Azure Security teljesítményteszt v2 – biztonsági mentés és helyreállítás
description: Azure Security teljesítményteszt v2 – biztonsági mentés és helyreállítás
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fe6861a3319b9d9c0e6535ee3303c90f0a0f26c8
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059262"
---
# <a name="security-control-backup-and-recovery"></a>Biztonsági ellenőrzés: biztonsági mentés és helyreállítás

A biztonsági mentés és helyreállítás kiterjed a vezérlőkre, így biztosítva, hogy az adatokat és a konfigurációkat a különböző szolgáltatási rétegek végzik, ellenőrzik és védik.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: rendszeres automatikus biztonsági mentések biztosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Győződjön meg arról, hogy biztonsági mentést készít a rendszerekről és az adatbázisokról, hogy egy váratlan esemény után fenntartsa az üzletmenet folytonosságát. Ehhez útmutatást kell adni a helyreállítási pontok célkitűzésének (RPO) és a helyreállítási idő célkitűzésének (RTO) bármely célkitűzésének.

Engedélyezze Azure Backup és konfigurálja a biztonsági mentési forrást (például Azure-beli virtuális gépeket, SQL Server, HANA-adatbázisokat vagy fájlmegosztást), valamint a kívánt gyakoriságot és megőrzési időt.  

A magasabb szintű redundancia érdekében engedélyezheti a Geo-redundáns tárolási lehetőséget, hogy a biztonsági mentési adatokat egy másodlagos régióba replikálja, és a helyreállítást a régiók közötti visszaállítással.

- [A Azure Backup engedélyezése](/azure/backup/)

- [A régiók közötti visszaállítás engedélyezése](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Szabályzat és szabványok](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: biztonsági mentési adataik titkosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| BR – 2 | 10,2 | CP-9 |

Győződjön meg arról, hogy a biztonsági másolatok védelmet biztosítanak a támadások ellen. Ennek tartalmaznia kell a biztonsági másolatok titkosítását a titkosság elvesztése elleni védelem érdekében.   

A helyszíni biztonsági mentéshez Azure Backup használatával a rendszer az Ön által megadott jelszót használja. Az Azure-szolgáltatások rendszeres biztonsági mentéséhez a biztonsági mentési adatai automatikusan titkosítva vannak az Azure platform által felügyelt kulcsok használatával. A biztonsági mentést az ügyfél által felügyelt kulcs használatával titkosíthatja. Ebben az esetben ügyeljen arra, hogy az ügyfél által felügyelt kulcs a Key vaultban is a biztonsági mentési hatókörben legyen. 

A biztonsági másolatok és az ügyfelek által felügyelt kulcsok elleni védelem érdekében Azure Backup, Azure Key Vault vagy más erőforrások szerepköralapú hozzáférés-vezérlését használhatja. Emellett speciális biztonsági funkciók is engedélyezhetők az MFA megköveteléséhez, mielőtt a biztonsági másolatok módosíthatók vagy törölhetők.

- [A Azure Backup biztonsági funkcióinak áttekintése](/azure/backup/security-overview)

- [Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal](/azure/backup/encryption-at-rest-with-cmk) 

- [Key Vault kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidens előkészítése](/) az Azure/Cloud-bevezetésre – keretrendszer/rendszerezés/felhő – biztonság – incidens – előkészítés

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| BR – 3 | 10,3 | CP-4, CP-9 |

Rendszeres időközönként végezze el a biztonsági mentés adatvisszaállítását. Győződjön meg arról, hogy visszaállíthatja az ügyfél által felügyelt kulcsok biztonsági mentését.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](/azure/backup/backup-azure-restore-files-from-vm)

- [Key Vault kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: az elveszett kulcsok kockázatának enyhítése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| BR – 4 | 10,4 | CP-9 |

Győződjön meg arról, hogy rendelkezik olyan mértékekkel, amelyek megakadályozzák a kulcsok elvesztését és helyreállítását. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Azure Key Vault a védelem lágy törlését és kiürítését.  

- [A védelem lágy törlésének és kiürítésének engedélyezése a Key Vaultban](/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Felelősség**: ügyfél

**Ügyfelek biztonsági résztvevői**:

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

