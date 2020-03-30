---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14a6703b3e256d33ab3b18e1821587cc3eb293db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79381943"
---
Fontos, hogy a virtuális gép (VM) biztonságos az alkalmazások futtatott. A virtuális gépek biztonságossá tétele egy vagy több Azure-szolgáltatást és olyan funkciót tartalmazhat, amelyek lehetővé teszik a virtuális gépekhez való biztonságos hozzáférést és az adatok biztonságos tárolását. Ez a cikk olyan információkat tartalmaz, amelyek lehetővé teszik a virtuális gép és az alkalmazások biztonságát.

## <a name="antimalware"></a>Kártevőirtó

A felhőalapú környezetek modern fenyegetési környezete dinamikus, és növeli a nyomást a hatékony védelem fenntartására a megfelelőségi és biztonsági követelményeknek való megfelelés érdekében. [A Microsoft Antimalware for Azure](../articles/security/fundamentals/antimalware.md) egy ingyenes, valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és más rosszindulatú szoftvereket. Riasztások konfigurálható, hogy értesítse, ha ismert rosszindulatú vagy nemkívánt szoftver megpróbálja telepíteni magát, vagy fut a virtuális gépen. Linux vagy Windows Server 2008 rendszert futtató virtuális gépeken nem támogatott.

## <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../articles/security-center/security-center-intro.md) segítségével megelőzheti, észlelheti és reagálhat a virtuális gépekre leselkedő fenyegetésekre. A Security Center integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben, segít észlelni az egyébként észrevétlenül megjelenő fenyegetéseket, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

A Security Center just-in-time hozzáférése a virtuális gép telepítésén keresztül alkalmazható az Azure virtuális gépek bejövő forgalmának zárolásához, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz. Ha a just-in-time engedélyezve van, és a felhasználó hozzáférést kér egy virtuális géphez, a Security Center ellenőrzi, hogy a felhasználó milyen engedélyekkel rendelkezik a virtuális géphez. Ha rendelkeznek a megfelelő engedélyekkel, a kérés jóváhagyásra kerül, és a Security Center automatikusan konfigurálja a hálózati biztonsági csoportokat (NSG-k), hogy korlátozott ideig engedélyezze a bejövő forgalmat a kiválasztott portokfelé. Az idő lejárta után a Security Center visszaállítja az NSG-ket a korábbi állapotokra. 

## <a name="encryption"></a>Titkosítás

A felügyelt lemezekhez két titkosítási módszer áll rendelkezésre. Titkosítás az operációs rendszer szintjén, amely az Azure Disk Encryption, és a titkosítás a platform szintjén, amely a kiszolgálóoldali titkosítás.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

Az Azure által kezelt lemezek alapértelmezés szerint automatikusan titkosítják az adatokat, amikor a felhőben megőrzik azokat. A kiszolgálóoldali titkosítás védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure-ban kezelt lemezeken lévő adatok transzparens módon vannak titkosítva 256 bites [AES titkosítással,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)amely az egyik legerősebb rendelkezésre álló blokktitkosítás, és fips 140-2-kompatibilis.

A titkosítás nincs hatással a felügyelt lemezek teljesítményére. A titkosításnak nincs további költsége.

A felügyelt lemez titkosításához platformáltal kezelt kulcsokra támaszkodhat, vagy saját kulcsokkal kezelheti a titkosítást. Ha úgy dönt, hogy a titkosítást saját kulcsaival kezeli, megadhatja az *ügyfél által felügyelt kulcsot,* amelyet a felügyelt lemezek összes adatának titkosításához és visszafejtéséhez használhat. 

Ha többet szeretne megtudni a kiszolgálóoldali titkosításról, olvassa el a [Windows](../articles/virtual-machines/windows/disk-encryption.md) vagy [Linux](../articles/virtual-machines/linux/disk-encryption.md)rendszerre vonatkozó cikkeket.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

A továbbfejlesztett [Windows vm](../articles/virtual-machines/windows/encrypt-disks.md) és [Linux virtuális gépek biztonsága](../articles/virtual-machines/linux/disk-encryption-overview.md) és megfelelősége érdekében az Azure-ban lévő virtuális lemezek titkosíthatók. A Windows virtuális gépeken lévő virtuális lemezek in-t a BitLocker használatával titkosítva vannak. A Linux os virtuális lemezek a dm-crypt használatával titkosítva vannak. 

Az Azure-ban a virtuális lemezek titkosítása díjmentes. A kriptográfiai kulcsok at az Azure Key Vault szoftvervédelemmel tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat a HARDVERbiztonsági modulok (HSM) FIPS 140-2 szintű 2 szabványoknak megfelelően hitelesített hardverbiztonsági modulokban.Cryptographic keys are stored in Azure Key Vault using software-protection, or you can import or generate your keys in Hardware Security Modules (HSM) certified to FIPS 140-2 level 2 standards. Ezek a kriptográfiai kulcsok a virtuális géphez csatlakoztatott virtuális lemezek titkosítására és visszafejtésére szolgálnak. Ön megtartja a titkosítási kulcsok feletti ellenőrzést, és naplózhatja azok használatát. Az Azure Active Directory egyszerű szolgáltatása biztonságos mechanizmust biztosít ezeknek a kriptográfiai kulcsoknak a virtuális gépek be- és kikapcsolása kor.

## <a name="key-vault-and-ssh-keys"></a>Key Vault és SSH-kulcsok

A titkos kulcsok és tanúsítványok erőforrásként modellezhetők, és a [Key Vault](../articles/key-vault/key-vault-whatis.md)biztosítja. Az Azure PowerShell segítségével kulcstárolókat hozhat létre [a Windows virtuális gépekhez](../articles/virtual-machines/windows/key-vault-setup.md) és az Azure CLI [Linuxos virtuális gépekhez.](../articles/virtual-machines/linux/key-vault-setup.md) Titkosításhoz kulcsokat is létrehozhat.

A kulcstartó hozzáférési szabályzatai külön-külön adnak engedélyeket a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz. Egy adott felhasználó számára hozzáférést engedélyezhet például kizárólag a kulcsokhoz, de a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférése ugyanakkor tárolószinten engedélyezett. Más szóval a [key vault hozzáférési szabályzat](../articles/key-vault/key-vault-secure-your-key-vault.md) nem támogatja az objektumszintű engedélyeket.

Amikor virtuális gépekhez csatlakozik, nyilvános kulcsú titkosítást kell használnia, hogy biztonságosabb módot biztosítson a bejelentkezéshez. Ez a folyamat magában foglalja a nyilvános és személyes kulcscsere segítségével a biztonságos shell (SSH) parancsot, hogy hitelesítse magát, nem pedig egy felhasználónevet és jelszót. A jelszavak ki vannak téve a találgatásos támadásoknak, különösen az internetre néző virtuális gépeken, például a webkiszolgálókon. Egy biztonságos shell (SSH) kulcspár, hozhat létre egy [Linux virtuális gép,](../articles/virtual-machines/linux/mac-create-ssh-keys.md) amely SSH-kulcsokat használ a hitelesítéshez, így nincs szükség a jelszavak at bejelentkezéshez. Az SSH-kulcsok segítségével is csatlakozhat a [Windows virtuális gép](../articles/virtual-machines/linux/ssh-from-windows.md) egy Linux virtuális gép.

## <a name="managed-identities-for-azure-resources"></a>Azure-erőforrások felügyelt identitásai

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. 

Az Azure Active Directory (Azure AD) Azure-erőforrások felügyelt identitásai szolgáltatása megoldást kínál erre a problémára. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.  A virtuális gépen futó kód jogkivonatot kérhet két végpontról, amelyek csak a virtuális gépről érhetők el. A szolgáltatással kapcsolatos részletesebb információkért tekintse át az [Azure-erőforrások felügyelt identitások](../articles/active-directory/managed-identities-azure-resources/overview.md) áttekintési lapját.   

## <a name="policies"></a>Házirendek

[Az Azure-szabályzatok](../articles/azure-policy/azure-policy-introduction.md) segítségével meghatározhatja a szervezet [Windows-alapú virtuális gépei](../articles/virtual-machines/windows/policy.md) és [Linux-alapú virtuális gépei](../articles/virtual-machines/linux/policy.md)kívánt viselkedését. A házirendek használatával a szervezet különböző konvenciókat és szabályokat kényszeríthet ki a vállalaton belül. A kívánt viselkedés kényszerítése segíthet a kockázat csökkentésében, miközben hozzájárul a szervezet sikeréhez.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Szerepköralapú [hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md)használatával elkülönítheti a feladatokat a csapaton belül, és csak a virtuális gép felhasználóinak a feladatok elvégzéséhez szükséges hozzáférést biztosíthatja. Ahelyett, hogy mindenkinek korlátlan engedélyeket a virtuális gép, engedélyezheti csak bizonyos műveleteket. A virtuális gép hozzáférés-vezérlését az [Azure Portalon](../articles/role-based-access-control/role-assignments-portal.md)konfigurálhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/role)vagy az[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)használatával.


## <a name="next-steps"></a>További lépések
- Az Azure Security Center for [Linux](../articles/security/fundamentals/overview.md) vagy [A Windows](../articles/virtual-machines/windows/tutorial-azure-security.md)használatával végigvezeti a virtuális gépek biztonságának figyeléséhez szükséges lépéseket.
