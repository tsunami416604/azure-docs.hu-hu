---
title: A Linux rendszerű virtuális gépekhez való Azure Disk Encryption engedélyezése
description: Ez a cikk a Linux rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez nyújt útmutatást.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 053c52d7d1a0282d72ad76408b77c96aa3b0e3e4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174707"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption Linux rendszerű virtuális gépekhez 

Azure Disk Encryption segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. A Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatásával biztosítja a kötetek titkosítását az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei számára, és integrálva van [Azure Key Vault](../../key-vault/index.yml) a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. 

Ha [Azure Security Center](../../security-center/index.yml)használ, a rendszer riasztást küld, ha nem titkosított virtuális gépek vannak. A riasztások magas súlyossági szintű szabályzatként jelenik meg, és az javasoljuk, hogy ezek a virtuális gépek titkosításához.

![Az Azure Security Center titkosítási riasztás](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
> - Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit. Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.

A Linux-alapú [virtuális gépek létrehozása és](disk-encryption-cli-quickstart.md) titkosítása az Azure CLI gyors üzembe helyezésével, valamint a Linux rendszerű [virtuális gép létrehozása és titkosítása az Azure PowerShell](disk-encryption-powershell-quickstart.md)gyors üzembe helyezésével – néhány percen belül megismerheti a linuxos Azure Disk Encryption alapjait.

## <a name="supported-vms-and-operating-systems"></a>Támogatott virtuális gépek és operációs rendszerek

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

A Linux rendszerű virtuális gépek [számos méretben](sizes.md)érhetők el. Azure Disk Encryption nem érhető el az [alapszintű, a-sorozatú](https://azure.microsoft.com/pricing/details/virtual-machines/series/)virtuális gépeken, illetve a minimális memória követelményeinek nem megfelelő virtuális gépeken:

| Virtuális gép | Minimális memória követelménye |
|--|--|
| Linux rendszerű virtuális gépek, ha csak az adatkötetek titkosítását| 2 GB |
| Linux rendszerű virtuális gépek az adatok és az operációsrendszer-kötetek titkosításakor, valamint a gyökér (/) fájlrendszer használatának helye 4 GB vagy kevesebb | 8 GB |
| Linux rendszerű virtuális gépek az adatok és az operációsrendszer-kötetek titkosítása esetén, valamint a gyökér (/) fájlrendszer használatának helye meghaladja a 4GB-ot | A rendszerindító fájlrendszer használata * 2. Például egy 16 GB-os rendszerszintű fájlrendszer-használat legalább 32GB RAM memóriát igényel |

Miután az operációsrendszer-lemez titkosítási folyamata befejeződött a Linux rendszerű virtuális gépeken, a virtuális gép beállítható úgy, hogy kevesebb memóriával fusson. 

A Premium Storage szolgáltatással rendelkező virtuális gépek esetében Azure Disk Encryption is elérhető. 

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Azure Disk Encryption az [Azure által támogatott Linux-disztribúciók](endorsed-distros.md)egy részhalmazán támogatott, amely az összes Linux-kiszolgáló lehetséges disztribúciójának részhalmaza.

![A Azure Disk Encryptiont támogató Linux Server-disztribúciók Venn ábrája](./media/disk-encryption/ade-supported-distros.png)

Az Azure által nem támogatott Linux Server-disztribúciók nem támogatják a Azure Disk Encryption; a támogatottak közül csak a következő disztribúciók és verziók támogatják a Azure Disk Encryption:

| Linux-disztribúció | Verzió | A kötettípus titkosítás támogatott|
| --- | --- |--- |
| Ubuntu | 18,04| Operációsrendszer- és lemez |
| Ubuntu | 16.04| Operációsrendszer- és lemez |
| Ubuntu | 14.04.5</br>[Az Azure-ban beállított kernel 4,15-es vagy újabb verzióra frissült](disk-encryption-troubleshooting.md) | Operációsrendszer- és lemez |
| RHEL | 7,7 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7,6 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.5 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.4 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.3 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.2 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.8 | Adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.7 | Adatlemez (lásd az alábbi megjegyzést) |
| CentOS | 7,7 | Operációsrendszer- és lemez |
| CentOS | 7,6 | Operációsrendszer- és lemez |
| CentOS | 7.5 | Operációsrendszer- és lemez |
| CentOS | 7.4 | Operációsrendszer- és lemez |
| CentOS | 7.3 | Operációsrendszer- és lemez |
| CentOS | 7.2n | Operációsrendszer- és lemez |
| CentOS | 6.8 | Adatlemez |
| openSUSE | 42,3 | Adatlemez |
| SLES | 12-SP4 | Adatlemez |
| SLES | 12-SP3 | Adatlemez |

> [!NOTE]
> Az új ADE-implementáció a RHEL operációs rendszer és az adatlemez RHEL7-elszámolású lemezképekhez való használata esetén támogatott. Az ADE jelenleg nem támogatott a RHEL saját előfizetéses (BYOS) lemezképek esetében. 

## <a name="additional-vm-requirements"></a>További virtuális gépekre vonatkozó követelmények

Azure Disk Encryption megköveteli, hogy a dm-crypt és a VFAT modulok jelen legyenek a rendszeren. A VFAT eltávolítása vagy letiltása az alapértelmezett rendszerképből megakadályozza, hogy a rendszer beolvassa a kulcs kötetét, és beszerezze a lemezek zárolásának feloldásához szükséges kulcsot a későbbi újraindítások során. A VFAT modul rendszerből való eltávolítására szolgáló rendszer-megerősítési lépések nem kompatibilisek a Azure Disk Encryptionokkal. 

A titkosítás engedélyezése előtt a titkosítani kívánt adatlemezeknek megfelelően szerepelniük kell a/etc/fstab. Ez a bejegyzés állandó blokk eszköz nevét a "/ dev/sdX" formátumban nevek nem támaszkodnak társítani kell ugyanazon a lemezen újraindítások, különösen akkor, ha a rendszer titkosítást alkalmaz eszközként használja. A viselkedésről további részleteket a következő témakörben talál: [A Linux rendszerű virtuális gép eszköz nevének módosításainak megoldása](troubleshoot-device-names-problems.md)

Ellenőrizze, hogy a /etc/fstab beállításokat a csatlakoztatáshoz szükséges megfelelően van beállítva. Ezeket a beállításokat, a mount - parancs futtatása vagy a virtuális gépet, és aktiválja a újracsatlakoztatást ezzel a módszerrel. Miután ez befejeződött, ellenőrizze, ellenőrizze, hogy a meghajtó is csatlakoztatva van a lsblk parancs kimenete. 
- Ha a /etc/fstab fájl megfelelően titkosítás engedélyezése előtt nem csatlakoztatja a meghajtót, az Azure Disk Encryption nem lehet megfelelően csatlakoztatásához.
- Az Azure Disk Encryption folyamat kerül át a csatlakoztatási adatokat /etc/fstab és a saját konfigurációs fájlba a titkosítási folyamat részeként. Nem lehet terekbe megtekintéséhez a data encryption meghajtó után a /etc/fstab hiányzó bejegyzés befejeződött.
- A titkosítás megkezdése előtt mindenképpen állítsa le az összes olyan szolgáltatást és folyamatot, amely a csatlakoztatott adatlemezekre írhat, és tiltsa le őket, hogy újraindítás után ne induljon el automatikusan. Ezek megtarthatják a fájlok megnyitását ezeken a partíciókon, így megakadályozva a titkosítási eljárás újracsatlakoztatását, ami hibát okoz a titkosításban. 
- Az újraindítást követően, az Azure Disk Encryption folyamat csatlakoztathatja az újonnan titkosított lemezek időt vesz igénybe. Ezek nem érhető el azonnal a számítógép újraindítása után. A folyamat elindításához, zárolásának feloldásához, és csatlakoztassa a titkosított meghajtók legyenek elérhetők más folyamatokkal való hozzáférés előtt időt kell. Ez a folyamat a rendszer jellegétől függően a rendszer újraindítása után több mint egy percet is igénybe vehet.

Az adatlemezek csatlakoztatásához és a szükséges/etc/fstab bejegyzések létrehozásához használható parancsok például a [Azure Disk Encryption előfeltételek CLI-szkriptben](https://github.com/ejarvi/ade-cli-getting-started) (244-248-es sorok) és a [Azure Disk Encryption előfeltételek PowerShell-ben találhatók. parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Hálózati követelmények

A Azure Disk Encryption funkció engedélyezéséhez a Linux rendszerű virtuális gépeknek meg kell felelniük a hálózati végpont következő konfigurációs követelményeinek:
  - Ahhoz, hogy jogkivonatot kapjon a kulcstartóhoz való kapcsolódáshoz, a linuxos virtuális gépnek csatlakoznia kell egy Azure Active Directory-végponthoz, @no__t -0login. microsoftonline. com @ no__t-1.
  - A titkosítási kulcsok a kulcstartóba való írásához a linuxos virtuális gépnek csatlakoznia kell a Key Vault-végponthoz.
  - A linuxos virtuális gépnek képesnek kell lennie csatlakozni egy Azure Storage-végponthoz, amely az Azure-bővítmény adattárát és a VHD-fájlokat tároló Azure Storage-fiókot üzemelteti.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információkért lásd: [Azure Key Vault tűzfal mögötti](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

További információ: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminológia
Az alábbi táblázat az Azure Disk Encryption dokumentációjában használt általános kifejezéseket ismerteti:

| Terminológia | Meghatározás |
| --- | --- |
| Azure Key Vault | A Key Vault szolgáltatás titkosítási, key management, amelyek az rendelkezik a Federal Information Processing szabványok (FIPS) hitelesített hardveres biztonsági modulokban. Ezen irányelvek segítenek a kriptográfiai kulcsok és a bizalmas, titkos kulcsok védelme érdekében. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) kezelésére és felügyeletére az Azure-erőforrások parancssorból van optimalizálva.|
| DM-Crypt |A [dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) a Linux-alapú, transzparens lemezes titkosítási alrendszer, amely lehetővé teszi a lemezes titkosítás használatát a Linux rendszerű virtuális gépeken. |
| Kulcs titkosítási kulcsa (KEK) | Az aszimmetrikus kulcs (RSA 2048), amellyel védetté teheti vagy becsomagolhatja a titkos kulcsot. Megadhat egy hardveres biztonsági modul (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| PowerShell-parancsmagok | További információkért lásd: [Azure PowerShell-parancsmagok](/powershell/azure/overview). |


## <a name="next-steps"></a>További lépések

- [Rövid útmutató – linuxos virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Rövid útmutató – linuxos virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption forgatókönyvek Linux rendszerű virtuális gépeken](disk-encryption-linux.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md)


