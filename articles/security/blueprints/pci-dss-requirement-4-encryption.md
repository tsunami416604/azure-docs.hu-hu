---
title: "Az Azure támogatási feldolgozási tervezetének - titkosítási követelmények"
description: "PCI DSS követelmény 4"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Adattitkosítási követelmények PCI DSS-kompatibilis környezetben 
## <a name="pci-dss-requirement-4"></a>PCI DSS követelmény 4

**Adatátvitel kártya tulajdonosát titkosítása nyitott, nyilvános hálózatokon**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Bizalmas adatokat titkosítani kell átvitel közben könnyen rosszindulatú támadók által elért hálózatokon keresztül. Helytelenül konfigurált vezeték nélküli hálózatokat és a korábbi titkosítási és a hitelesítési protokollok biztonsági továbbra is kártya tulajdonosát adatok környezetekben való privilegizált hozzáférést rések kihasználásával rosszindulatú felhasználók céljainak.

## <a name="pci-dss-requirement-41"></a>4.1 a PCI DSS követelmény

**4.1** (például a TLS, IPSEC, SSH stb.) erős titkosítás és a biztonsági protokoll segítségével megakadályozhatja a bizalmas kártya tulajdonosát adatokat átvitel közben nyitott, nyilvános hálózatokon, például az alábbiakat:
- Csak a megbízható kulcsok és a tanúsítványok használata engedélyezett.
- A használt protokoll csak támogatja a biztonságos verzióira vagy konfigurációira.
- A titkosítás megfelelő használt titkosítási módszert. 

> [!NOTE]
> Korai SSL/TLS használatos, ahol a követelmények az A2 el kell végezni.
>
> Nyitott, nyilvános hálózatokhoz példái közé tartoznak, de közt:
> - Az Internet
> - Vezeték nélküli technológiák, például 802.11 és Bluetooth
> - Cellás technológiák, például globális rendszer mobil kommunikációhoz (GSM) Code osztás több access (CDMA)
> - Általános csomag rádió szolgáltatás (GPRS)
> - Műholdas kommunikáció


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló egy PaaS megoldás, amely megvalósítja az erős titkosítás a központi telepítés a következőképpen:<br /><br />A titkosított adatokat nyugalmi követelményeinek megfelelően, [Azure Storage](https://azure.microsoft.com/services/storage/) a következőt:<br /><br /><ul><li>[Az Azure Storage szolgáltatás titkosítási (SSE) inaktív adatok](/azure/storage/storage-service-encryption)</li><li>SQL-adatbázis: A Platformszolgáltatásos SQL Database-példányt, adatbázis biztonsági intézkedéseket megjelenítve szolgál. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).</li><li>[Az Azure Disk Encryption (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Az Azure Key Vault használatával igazodik Azure Government, PCI DSS és HIPAA követelményeket.|



### <a name="pci-dss-requirement-411"></a>PCI DSS követelmény 4.1.1.

**4.1.1** kártya tulajdonosát adatok átvitele vezeték nélküli hálózatok biztosítása, vagy csatlakozik a kártya tulajdonosát adatok környezet, az ágazatban kialakult bevált gyakorlaton (például IEEE 802.11i) segítségével valósítja meg a hitelesítéshez és átviteli erős titkosítást.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Vezeték nélküli és SNMP nem valósult meg a megoldást.|



## <a name="pci-dss-requirement-42"></a>4.2 a PCI DSS követelmény

**4.2** soha ne küldjön a nem védett PANs végfelhasználói üzenetkezelési technológiák (például e-mail, azonnali üzenetküldés, SMS, csevegési stb.).

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nincs minden üzenetküldési megoldást megvalósítva, amely nem védett elsődleges fiók száma (PÁSZTÁZÁS) adatokat küldhet.|



## <a name="pci-dss-requirement-43"></a>4.3 a PCI DSS követelmény

**4.3** győződjön meg arról, hogy a biztonsági házirendek és üzemeltetési eljárások kártya tulajdonosát adatátvitel titkosítására dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége dokumentálásáért és kártya tulajdonosát adatokat tartalmazó adatátvitel titkosításához.|




