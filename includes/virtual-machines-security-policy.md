Fontos a futtatott alkalmazások biztonsága a virtuális gép (VM). A virtuális gépek védelme tartalmazhat egy vagy több Azure-szolgáltatásokat és funkciókat, amelyek biztonságos hozzáférést a virtuális gépek és az adatok biztonságos tárolására. Ez a cikk nyújt információt, amely lehetővé teszi a virtuális gép és az alkalmazások biztonságos.

## <a name="antimalware"></a>Kártevőirtó

A modern fenyegetésekről alkotott képet felhőalapú környezetek dinamikus, a terhelés fenntartása hatékony védelme érdekében végzett eszközfigyelés a megfelelőségi és biztonsági követelmények növelését. [Az Azure-hoz a Microsoft Antimalware](../articles/security/azure-security-antimalware.md) szabad valós idejű védelem képesség, amely segít a azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek. Riasztások beállítható úgy, hogy értesítést küld, ha ismert kártevő vagy nemkívánatos szoftver telepítést vagy a virtuális gép futó kísérletek.

## <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../articles/security-center/security-center-intro.md) megakadályozása, észlelésében és kezelésében a virtuális gépek fenyegetések nyújt segítséget. A Security Center itt integrált biztonsági monitorozást és kezelése az Azure-előfizetések között, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik.

## <a name="encryption"></a>Titkosítás

Továbbfejlesztett [Windows virtuális gép](../articles/virtual-machines/windows/encrypt-disks.md) és [Linux virtuális gép](../articles/virtual-machines/linux/encrypt-disks.md) biztonsági és megfelelőségi, az Azure-ban virtuális lemezek titkosíthatók. A Windows virtuális gépek virtuális lemezek vannak titkosítása a Bitlocker használatával. A Linux virtuális gépek virtuális lemezek használatával a dm-crypt program segítségével aktívan vannak titkosítva. 

Nincs nem kell fizetni az Azure-ban virtuális lemezek titkosítására. Titkosítási kulcsok Azure Key Vault szoftver-védelemmel vannak tárolva, vagy importálhat vagy a tanúsított FIPS 140-2 2. szint szabványok hardveres biztonsági modulokkal (HSM) a kulcsok létrehozásához. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. A titkosítási kulcsokat a felügyeletet, és naplózhatja a használatukat. Egy egyszerű Azure Active Directory szolgáltatás lehetővé teszi a biztonságos kiadása a titkosítási kulcsokat, a virtuális gépek vannak kapcsolva, és ki.

## <a name="key-vault-and-ssh-keys"></a>Kulcstartó és az SSH-kulcsok

Tanúsítványok és titkos kulcsok erőforrások modellezve és által biztosított [Key Vault](../articles/key-vault/key-vault-whatis.md). Azure PowerShell hozhat létre a kulcstárolók [Windows virtuális gépek](../articles/virtual-machines/windows/key-vault-setup.md) és az Azure parancssori felületen [Linux virtuális gépek](../articles/virtual-machines/linux/key-vault-setup.md). Titkosítási kulcsok pedig is létrehozhat.

Kulcstároló hozzáférési szabályzatainak engedélyeket kulcsokat, a titkos kulcsok és a tanúsítványok külön-külön. Egy adott felhasználó számára hozzáférést engedélyezhet például kizárólag a kulcsokhoz, de a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférése ugyanakkor tárolószinten engedélyezett. Más szóval [tároló házirend kulcs](../articles/key-vault/key-vault-secure-your-key-vault.md) nem támogatja az oszlopszintű engedélyek objektum.

Amikor virtuális gépek csatlakoznak, a nyilvános kulcsú próbál bejelentkezni a azokat egy biztonságosabb megoldást biztosítson a kell használnia. A folyamat során a nyilvános és titkos kulcsok cseréjét saját maga helyett egy felhasználónév és jelszó hitelesítéséhez a secure shell (SSH) parancs használatával. Ki téve találgatásos támadások, különösen az internetre irányuló virtuális gépeken futó, például jelszavakat. A secure shell (SSH) kulcspár hozhat létre egy [Linux virtuális gép](../articles/virtual-machines/linux/mac-create-ssh-keys.md) , amely SSH-kulcsokat használ a hitelesítéshez, így nincs szükség a bejelentkezési jelszót. SSH-kulcsok használata a csatlakozáshoz egy [Windows virtuális gép](../articles/virtual-machines/linux/ssh-from-windows.md) egy Linux virtuális géphez.

## <a name="policies"></a>Házirendek

[Az Azure házirendek](../articles/azure-policy/azure-policy-introduction.md) segítségével határozza meg a kívánt viselkedés, a szervezet [Windows virtuális gépek](../articles/virtual-machines/windows/policy.md) és [Linux virtuális gépek](../articles/virtual-machines/linux/policy.md). Házirendek segítségével a szervezetek kényszerítheti a különböző egyezmények és szabályok a vállalaton belül. A kívánt viselkedés végrehajtását segítségével mérsékelhetik a kockázatokat hozzájárul a szervezet sikeres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Használatával [szerepköralapú hozzáférés-vezérlést (RBAC)](../articles/active-directory/role-based-access-control-what-is.md), feladatokat elkülönítse a munkacsoporton belül, és csak olyan mértékű hozzáférést adhat a felhasználóknak a feladataik elvégzéséhez szükséges virtuális gépén. Jogosultságot ad a Mindenki helyett korlátlan engedélyeket a virtuális Gépre, engedélyezheti a csak bizonyos műveleteket. Hozzáférés-vezérlés a virtuális gép állíthatja be a [Azure-portálon](../articles/active-directory/role-based-access-control-configure.md)használatával a [Azure CLI](https://docs.microsoft.com/cli/azure/role), vagy[Azure PowerShell](../articles/active-directory/role-based-access-control-manage-access-powershell.md).


## <a name="next-steps"></a>Következő lépések
- A virtuális gép biztonsági figyelése az Azure Security Center segítségével lépéseit ismerteti [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).