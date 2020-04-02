---
title: Az Azure Disk Encryption engedélyezése Linux rendszerű virtuális gépekhez
description: Ez a cikk a Microsoft Azure lemeztitkosítás linuxos virtuális gépekhez való engedélyezésével kapcsolatos utasításokat tartalmaz.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d058ff5f9863642f73725db3472c942161447f25
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548436"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure lemeztitkosítás Linuxos virtuális gépekhez 

Az Azure Disk Encryption segít az adatok biztonságos megőrzésében a vállalat által előírt biztonsági és megfelelőségi követelmények kielégítése érdekében. A Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását használja az Azure virtuális gépek operációs rendszerének és adatlemezeinek kötettitkosításához és adatlemezeinek biztosításához, és az [Azure Key Vaultmal](../../key-vault/index.yml) integrálva van a lemeztitkosítási kulcsok és titkos kulcsok vezérléséhez és kezeléséhez. 

Ha [az Azure Security Center,](../../security-center/index.yml)a rendszer figyelmezteti, ha a virtuális gépek, amelyek nem titkosított. A riasztások nagy súlyosságú, és a javaslat a virtuális gépek titkosítása.

![Az Azure Security Center lemeztitkosítási riasztása](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel a virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részletekért tekintse meg [az Azure Disk Encryption with Azure AD (előző kiadás)](disk-encryption-overview-aad.md) című témakört. 
> - Bizonyos javaslatok növelhetik az adatok, a hálózat vagy a számítási erőforrás-használat, ami további licenc vagy előfizetési költségeket. Érvényes aktív Azure-előfizetéssel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az Azure-ban a támogatott régiókban.
> - A 2. További információt [a 2. generációs virtuális gépek támogatása az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)

Az Azure Disk Encryption for Linux alapjait néhány perc alatt megismerheti az [Azure CLI gyorsindítással, illetve](disk-encryption-cli-quickstart.md) a [Linux os virtuális gép létrehozása és titkosítása az Azure Powershell gyorsindítással.](disk-encryption-powershell-quickstart.md)

## <a name="supported-vms-and-operating-systems"></a>Támogatott virtuális gépek és operációs rendszerek

### <a name="supported-vms"></a>Támogatott virtuális gépek

A Linux virtuális gépek [számos méretben](sizes.md)kaphatók. Az Azure Disk Encryption nem érhető el [alapszintű, A-sorozatú virtuális gépeken](https://azure.microsoft.com/pricing/details/virtual-machines/series/)vagy olyan virtuális gépeken, amelyek nem felelnek meg ezeknek a minimális memóriakövetelményeknek:

| Virtuális gép | Minimális memóriakövetelmény |
|--|--|
| Linux virtuális gépek, ha csak az adatkötetek titkosítása| 2 GB |
| Linux virtuális gépek az adat- és operációsrendszer-kötetek titkosításakor, és ahol a gyökér (/) fájlrendszer használata 4 GB vagy kevesebb | 8 GB |
| Linux virtuális gépek az adat- és operációsrendszer-kötetek titkosításakor, és ahol a gyökér (/) fájlrendszer-használat nagyobb, mint 4 GB | A gyökér fájlrendszer használata * 2. Például egy 16 GB-os gyökérfájlrendszer-használathoz legalább 32 GB RAM-ra van szükség |

Miután az operációs rendszer lemeztitkosítási folyamat a Linux virtuális gépeken befejeződött, a virtuális gép beállítható, hogy kevesebb memóriával fusson. 

Az Azure Disk Encryption prémium szintű tárhellyel rendelkező virtuális gépekhez is elérhető.

Az Azure Disk Encryption nem érhető el [Lsv2-series VMs](../lsv2-series.md)a [2.](generation-2.md#generation-1-vs-generation-2-capabilities) További kivételek: [Azure Disk Encryption: Nem támogatott forgatókönyvek.](disk-encryption-linux.md#unsupported-scenarios)

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az Azure Disk Encryption az [Azure által jóváhagyott Linux-disztribúciók](endorsed-distros.md)egy részhalmaza támogatja, amely maga is az összes lehetséges Linux-kiszolgáló-disztribúció egy részhalmaza.

![Az Azure lemeztitkosítást támogató Linux-kiszolgálói disztribúciók Venn-diagramja](./media/disk-encryption/ade-supported-distros.png)

Az Azure által nem jóváhagyott Linux-kiszolgáló-disztribúciók nem támogatják az Azure lemeztitkosítást; a jóváhagyottverziók közül csak a következő disztribúciók és verziók támogatják az Azure Disk Encryption szolgáltatást:

| Linux disztribúció | Verzió | Titkosításhoz támogatott kötettípus|
| --- | --- |--- |
| Ubuntu | 18.04| Operációs rendszer és adatlemez |
| Ubuntu | 16.04| Operációs rendszer és adatlemez |
| Ubuntu | 14.04.5</br>[4.15-re vagy újabb verzióra frissített Azure-kernellel](disk-encryption-troubleshooting.md) | Operációs rendszer és adatlemez |
| RHEL | 7.7 | Operációs rendszer és adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.6 | Operációs rendszer és adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7,5 | Operációs rendszer és adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.4 | Operációs rendszer és adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.3 | Operációs rendszer és adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.2 | Operációs rendszer és adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.8 | Adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.7 | Adatlemez (lásd az alábbi megjegyzést) |
| CentOS | 7.7 | Operációs rendszer és adatlemez |
| CentOS | 7.6 | Operációs rendszer és adatlemez |
| CentOS | 7,5 | Operációs rendszer és adatlemez |
| CentOS | 7.4 | Operációs rendszer és adatlemez |
| CentOS | 7.3 | Operációs rendszer és adatlemez |
| CentOS | 7.2n. | Operációs rendszer és adatlemez |
| CentOS | 6.8 | Adatlemez |
| openSUSE | 42.3 | Adatlemez |
| SLES | 12-SP4 | Adatlemez |
| SLES | 12-SP3 | Adatlemez |

> [!NOTE]
> Az új Azure Disk Encryption implementáció támogatja rhel operációs rendszer és az adatlemez RHEL7 pay-as-you-go rendszerképek.  
>
> Az ADE az RHEL Bring-Your-Own-Subscription Gold Images esetében is támogatott, de csak az előfizetés regisztrálása **után.** További információ: [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images az Azure-ban](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>További virtuális gépre vonatkozó követelmények

Az Azure Disk Encryption megköveteli, hogy a dm-crypt és a vfat modulok jelen legyenek a rendszeren. A vfat eltávolítása vagy letiltása az alapértelmezett lemezképről megakadályozza, hogy a rendszer elolvassa a kulcskötetet, és beszerezze a lemezek feloldásához szükséges kulcsot a későbbi újraindítások során. A rendszermegerősítési lépések, amelyek eltávolítják a vfat modult a rendszerből, nem kompatibilisek az Azure Disk Encryption szolgáltatással. 

A titkosítás engedélyezése előtt az adatlemezeknek megfelelően szerepelniük kell az /etc/fstab könyvtárban. Ehhez a bejegyzéshez használjon állandó blokkeszköz-nevet, mivel a "/dev/sdX" formátumú eszköznevek nem hivatkozhatnak arra, hogy ugyanahhoz a lemezhez legyenek társítva az újraindítások során, különösen a titkosítás alkalmazása után. A viselkedéssel kapcsolatos további részletekért lásd: [Linux virtuális gép eszköznevének módosításának elhárítása](troubleshoot-device-names-problems.md)

Győződjön meg arról, hogy az /etc/fstab beállítások megfelelően vannak konfigurálva a csatlakoztatáshoz. A beállítások konfigurálásához futtassa a mount -a parancsot, vagy indítsa újra a virtuális gépet, és indítsa el így az újracsatlakoztatást. Ha ez befejeződött, ellenőrizze az lsblk parancs kimenetét, és ellenőrizze, hogy a meghajtó még csatlakoztatva van-e. 
- Ha az /etc/fstab fájl nem csatlakoztatja megfelelően a meghajtót a titkosítás engedélyezése előtt, az Azure Disk Encryption nem tudja megfelelően csatlakoztatni.
- Az Azure Disk Encryption folyamat áthelyezi a csatlakoztatási adatokat az /etc/fstab és a saját konfigurációs fájl a titkosítási folyamat részeként. Ne ijedjen meg, ha az adatmeghajtó titkosításának befejezése után hiányzik az /etc/fstab bejegyzés.
- A titkosítás megkezdése előtt állítsa le az összes olyan szolgáltatást és folyamatot, amely a csatlakoztatott adatlemezekre írhat, és tiltsa le őket, hogy újraindítás után ne induljanak újra automatikusan. Ezek a partíciók nyitva tarthatják a fájlokat ezeken a partíciókon, megakadályozva a titkosítási eljárást, ami a titkosítás hibáját okozza. 
- Újraindítás után időbe telik az Azure disk encryption folyamat az újonnan titkosított lemezek csatlakoztatása. Az újraindítás után nem lesznek azonnal elérhetők. A folyamatnak időre van szüksége a titkosított meghajtók elindításához, feloldásához és csatlakoztatásához, mielőtt más folyamatok számára elérhetővé vésné. Ez a folyamat a rendszer jellemzőitől függően az újraindítás után több mint egy percet is igénybe vehet.

Egy példa az adatlemezek csatlakoztatására és a szükséges /etc/fstab bejegyzések létrehozására használható parancsokra az [Azure Disk Encryption prerequisites CLI parancsfájlban](https://github.com/ejarvi/ade-cli-getting-started) (244-248. sor) és az [Azure Disk Encryption előfeltételei ben található A PowerShell-parancsfájl.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts) 

## <a name="networking-requirements"></a>Hálózati követelmények

Az Azure lemeztitkosítási szolgáltatás engedélyezéséhez a Linux-virtuális gépeknek meg kell felelniük a következő hálózati végpontkonfigurációs követelményeknek:
  - A kulcstárolóhoz való csatlakozáshoz a Linux virtuális gépnek képesnek kell lennie egy \[\]Azure Active Directory-végponthoz, login.microsoftonline.com.
  - A titkosítási kulcsok at a key vault, a Linux virtuális gép képesnek kell lennie arra, hogy csatlakozzon a key vault végponthoz.
  - A Linux virtuális gépnek képesnek kell lennie egy Azure-tárolóvégponthoz csatlakozni, amely az Azure-bővítménytárházat és a Virtuális merevlemez-fájlokat tároló Azure-tárfiókot üzemelteti.
  -  Ha a biztonsági szabályzat korlátozza a hozzáférést az Azure virtuális gépek az internetre, feloldhatja az előző URI-t, és konfigurálhat egy adott szabályt, hogy lehetővé tegye a kimenő kapcsolatot az IP-k. További információ: [Azure Key Vault tűzfal mögött.](../../key-vault/key-vault-access-behind-firewall.md)  

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Az Azure Disk Encryption használatához egy Azure Key Vault szükséges a lemeztitkosítási kulcsok és titkos kulcsok vezérléséhez és kezeléséhez. A key vault és a virtuális gépek ugyanabban az Azure-régióban és előfizetésben kell lennie.

További információt az [Azure Disk Encryption kulcstartójának létrehozása és konfigurálása című témakörben talál.](disk-encryption-key-vault.md)

## <a name="terminology"></a>Terminológia
Az alábbi táblázat az Azure lemeztitkosítási dokumentációjában használt néhány általános kifejezést határozza meg:

| Terminológia | Meghatározás |
| --- | --- |
| Azure Key Vault | A Key Vault egy kriptográfiai, kulcskezelő szolgáltatás, amely a Federal Information Processing Standards (FIPS) által ellenőrzött hardveres biztonsági modulokon alapul. Ezek a szabványok segítenek a kriptográfiai kulcsok és a bizalmas titkok védelmében. További információt az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) [dokumentációjában, valamint az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál. |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) az Azure-erőforrások parancssorból történő kezelésére és felügyeletére van optimalizálva.|
| DM-kripta |[A DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) a Linux-alapú, átlátható lemeztitkosítási alrendszer, amely a linuxos virtuális gépek lemeztitkosításának engedélyezésére szolgál. |
| Kulcstitkosítási kulcs (KEK) | Az aszimmetrikus kulcs (RSA 2048), amely a titkos kulcs védelmére vagy burkolására használható. Hardveres biztonsági modul (HSM) által védett kulcsot vagy szoftverrel védett kulcsot is biztosíthat. További információt az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) [dokumentációjában, valamint az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál. |
| PowerShell-parancsmagok | További információ: [Azure PowerShell-parancsmagok.](/powershell/azure/overview) |


## <a name="next-steps"></a>További lépések

- [Rövid útmutató – Linuxos virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Rövid útmutató – Linuxos virtuális gép létrehozása és titkosítása az Azure Powershell használatával](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépekhez](disk-encryption-linux.md)
- [Az Azure Disk Encryptions CLI-parancsfájl előfeltételei](https://github.com/ejarvi/ade-cli-getting-started)
- [Az Azure disk encryptions powershell-parancsfájl előfeltételei](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](disk-encryption-key-vault.md)


