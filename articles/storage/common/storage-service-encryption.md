---
title: Az Azure Storage Service Encryption az inaktív adatok |} A Microsoft Docs
description: Azure Blob storage Szolgáltatásoldali titkosítását, ha az adatok tárolása Azure Storage Service Encryption szolgáltatással, és visszafejteni az adatok lekérésekor.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.openlocfilehash: f35697139a4be49be8a645cfd4d451ad8e3c8094
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412355"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Az Azure Storage Service Encryption az inaktív adatok
Inaktív adatok az Azure Storage Service Encryption segítségével a szervezeti biztonsági és megfelelőségi követelmények kielégítése érdekében az adatok védelme. Ezzel a funkcióval az Azure storage-platform automatikusan titkosítja az adatokat előtt, az Azure Blob storage, Azure Files és az Azure Queue storage megőrzése, és mindig visszafejti az adatokat lekérés előtt. A titkosítás és a rest, visszafejtési és kulcskezelési a Storage Service Encryption titkosítás kezelése a felhasználók számára átlátható. Az Azure storage-platformra írt összes adat titkosítva van segítségével 256 bites [AES-titkosítás](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), egyik legerősebb Rejtjelek érhető el.

A Storage Service Encryption engedélyezve van az összes meglévő és új storage-fiók, és nem tiltható le. Alapértelmezés szerint az adatok biztonságos, mert nem kell módosítani a kód vagy a Storage Service Encryption szolgáltatással is.

A funkció automatikusan titkosítja az adatokat:

- Az Azure Blob storage, Azure Files között, az Azure Queue storage, az Azure Table storage.  
- Mindkét teljesítményszint (Standard és prémium).
- Mindkét üzemi modellben (Azure Resource Manager és Klasszikus modell).

> [!Note]  
> Nem áll rendelkezésre a Storage Service Encryption [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Javasoljuk, használjon titkosítást az operációs rendszer szintjén, például [az Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), iparági szabványt használó [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) a Windows és [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít Linux rendszeren titkosítási KeyVault integrálva.

A Storage Service Encryption az Azure storage szolgáltatások teljesítményét nem befolyásolja.

A Storage Service Encryptionnel is használhatja a Microsoft által felügyelt titkosítási kulcsokat, vagy használhatja a saját titkosítási kulcsokat. A saját kulcsok használatával kapcsolatos további információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Nézet titkosítási beállítások az Azure Portalon
A Storage Service Encryption beállításainak megtekintéséhez jelentkezzen be a [az Azure portal](https://portal.azure.com) és a egy tárfiók kiválasztását. Az a **beállítások** panelen válassza a **titkosítási** beállítás.

![Portál képernyőképe a titkosítási beállítás](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>A Storage Service Encryption szolgáltatással kapcsolatos gyakori kérdések
**Hogyan titkosíthatom az adatokat egy Resource Manager-storage-fiókban?**  
A Storage Service Encryption engedélyezve van az összes storage-fiókok – klasszikus és Resource Manager, a storage-fiók létrehozása előtt engedélyezték a titkosítást a meglévő fájljai fog visszamenőlegesen beolvasása egy titkosítási háttérfolyamat titkosítja.

**A Storage Service Encryption alapértelmezés szerint engedélyezve van a tárfiók létrehozásakor?**  
Igen, a Storage Service Encryption engedélyezett összes storage-fiókok és az Azure storage-szolgáltatásokhoz.

**Rendelkezem egy Resource Manager-tárfiókot. Rá lehet engedélyezni a Storage Service Encryption?**  
A Storage Service Encryption szolgáltatást minden meglévő Resource Manager-tárfiókok alapértelmezés szerint engedélyezve van. Ez az Azure Blob storage, Azure Files között, az Azure Queue storage, Table storage esetében támogatott. 

**Is titkosítást is letiltja a storage-fiókom?**  
Alapértelmezés szerint engedélyezve van a titkosítás, és nem rendelkeznek a tárfiók titkosításának letiltása. 

**Több IP-címek fenntartási az Azure Storage szolgáltatás Ha engedélyezve van a Storage Service Encryption szolgáltatással?**  
Nincs további költség nélkül.

**Használhatja a saját titkosítási kulcsokat?**  
Igen, használhatja a saját titkosítási kulcsokat. További információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md).

**Visszavonhatja a hozzáférést a titkosítási kulcsokat?**  
Igen, ha Ön [saját titkosítási kulcsok használatához](storage-service-encryption-customer-managed-keys.md) az Azure Key Vaultban.

**Az Azure Managed Disks elérhető-e a Storage Service Encryption szolgáltatással?**  
Nem, nem áll rendelkezésre a Storage Service Encryption [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Javasoljuk, használjon titkosítást az operációs rendszer szintjén, például [az Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), iparági szabványt használó [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) a Windows és [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít Linux rendszeren titkosítási KeyVault integrálva.

**Miben különbözik a Storage Service Encryption az Azure Disk Encryption?**  
Az Azure Disk Encryption biztosít operációsrendszer-alapú megoldások, például a BitLocker és a DM-Crypt és az Azure KeyVault közötti integrációt. A Storage Service Encryption natív módon a rétegben az Azure storage platform, a virtuális gép alábbi titkosítását biztosítja.

**Klasszikus tárfiók van. Rá lehet engedélyezni a Storage Service Encryption?**  
A Storage Service Encryption engedélyezve van az összes storage-fiókok (klasszikus és Resource Manager).

**Hogyan használatával titkosítsa a klasszikus tárfiókot az adatokat?**  
A titkosítás alapértelmezés szerint engedélyezve van az Azure storage szolgáltatásban tárolt minden adat automatikusan titkosítva lesznek. 

**Létrehozhatok storage-fiókok Storage Service Encryption az Azure PowerShell és az Azure CLI használatával engedélyezve van?**  
A Storage Service Encryption alapértelmezés szerint engedélyezve van minden olyan storage-fiók létrehozása idején (klasszikus vagy Resource Manager). Azure PowerShell-lel és az Azure CLI használatával ellenőrizheti a fiók tulajdonságait.

**A storage-fiókomat geo-redundantly replikálandó van beállítva. A Storage Service Encryptionnel saját redundáns példány is titkosítva lesznek?**  
Igen, a storage-fiók összes másolatát vannak titkosítva. Beállítások támogatottak – redundancia helyileg redundáns tárolás, a zónaredundáns tárolás, a georedundáns tárolás és az írásvédett georedundáns tárolás.

**A Storage Service Encryption csak bizonyos régiókban engedélyezett?**  
A Storage Service Encryption az összes régióban érhető el.

**Storage Service Encryption FIPS 140-2 szabványnak megfelelő van?**  
Igen, a Storage Service Encryption a FIPS 140-2 szabványnak megfelelő.

**Hogyan vehetem fel a kapcsolatot valaki Ha I problémába ütközik, vagy visszajelzést szeretne biztosítani?**  
Kapcsolattartó [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) bármilyen problémák és a Storage Service Encryption szolgáltatással kapcsolatos visszajelzéseket.

## <a name="next-steps"></a>További lépések
Az Azure Storage kínál átfogó készlete biztonsági képességeket, hogy együtt a fejlesztők biztonságos alkalmazásokat hozhat létre. További információkért lásd: a [Storage biztonsági útmutatóját](../storage-security-guide.md).