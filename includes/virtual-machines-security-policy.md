Fontos a futtatott alkalmazások biztonságát a virtuális gép (VM). A virtuális gépek védelme is tartalmazhat, egy vagy több Azure-szolgáltatásokat és funkciókat, mind a biztonságos hozzáférés a virtuális gépek és az adatok biztonságos tárolását. Ez a cikk nyújt információt, amely lehetővé teszi a virtuális gép és az alkalmazások biztonságának megőrzéséhez.

## <a name="antimalware"></a>Kártevőirtó

A modern fenyegetések világának felhőalapú környezetek dinamikus növelése a nyomás megfelelőségi és biztonsági követelmények teljesítéséhez hatékony védelem fenntartása céljából. [A Microsoft Antimalware az Azure](../articles/security/azure-security-antimalware.md) egy ingyenes, valós idejű védelem képesség, amellyel a vírusok, kémprogramok és más kártevők azonosításában és. Riasztások konfigurálható arra az esetre, ha ismert kártevő vagy nemkívánatos szoftver megkísérli a telepítést vagy a virtuális gépen futtatható.

## <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../articles/security-center/security-center-intro.md) megelőzését, észlelését és háríthatja el a fenyegetéseket a virtuális gépek segítségével. Security Center integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetésekre, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és a biztonsági megoldások széles ökoszisztémájával képes együttműködni.

## <a name="encryption"></a>Titkosítás

Bővített [Windows virtuális gép](../articles/virtual-machines/windows/encrypt-disks.md) és [Linux rendszerű virtuális gép](../articles/virtual-machines/linux/encrypt-disks.md) biztonsági és megfelelőségi, virtuális lemezeket az Azure-ban is titkosíthatók. Windows virtuális gépek a virtuális lemezek vannak titkosítása a Bitlocker használatával. Linux rendszerű virtuális gépek a virtuális lemezek titkosítása inaktív-dm-crypt. 

Nem jár költséggel az Azure-beli virtuális lemezek titkosításához. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát. Egy Azure Active Directory egyszerű szolgáltatás lehetővé teszi a biztonságos kiállító a kriptográfiai kulcsokat, a virtuális gépek vannak kapcsolva, és ki.

## <a name="key-vault-and-ssh-keys"></a>A Key Vault és az SSH-kulcsok

Titkos kulcsok és tanúsítványok erőforrásként modellezése és által biztosított [Key Vault](../articles/key-vault/key-vault-whatis.md). Azure PowerShell segítségével hozzon létre a kulcstartókhoz [Windows virtuális gépek](../articles/virtual-machines/windows/key-vault-setup.md) és az Azure parancssori felületen [Linux rendszerű virtuális gépek](../articles/virtual-machines/linux/key-vault-setup.md). A titkosítási kulcsokat is létrehozhat.

A kulcstartó-hozzáférési házirendekkel külön engedélyezheti a kulcsok, titkos kódok és tanúsítványok. Egy adott felhasználó számára hozzáférést engedélyezhet például kizárólag a kulcsokhoz, de a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférése ugyanakkor tárolószinten engedélyezett. Más szóval [key vault hozzáférési szabályzattal](../articles/key-vault/key-vault-secure-your-key-vault.md) nem támogatja az Objektumszintű engedélyeket.

Virtuális gépek csatlakozni, ha a nyilvános kulcsú jelentkezzen be őket egy biztonságosabb módszert biztosít kell használnia. E folyamat magában foglalja egy nyilvános és titkos kulcs exchange a secure shell-(SSH) paranccsal a saját maga helyett egy felhasználónév és jelszó hitelesítéséhez. Jelszavak kitéve a találgatásos támadások, különösen az internetkapcsolattal rendelkező virtuális gépeken, például az. Egy secure shell-(SSH) kulcspárt, létrehozhat egy [Linux rendszerű virtuális gép](../articles/virtual-machines/linux/mac-create-ssh-keys.md) , amelyek SSH-kulcsokat használ a hitelesítéshez, aminek köszönhetően nincs szükség jelszavakra a bejelentkezéshez. SSH-kulcsok használata csatlakozni egy [Windows virtuális gép](../articles/virtual-machines/linux/ssh-from-windows.md) egy Linux rendszerű virtuális gépre.

## <a name="managed-identities-for-azure-resources"></a>Azure-erőforrások felügyelt identitásai

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. 

Az Azure-erőforrások szolgáltatásokhoz az Azure Active Directoryban (Azure AD) a felügyelt identitásokból megoldja a problémát. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.  A kódot, amely a virtuális gép fut-e elérhető csak a virtuális gépen két végpontot is jogkivonatot kérhet. További részletes információ a szolgáltatásról, tekintse át a [felügyelt identitások az Azure-erőforrások](../articles/active-directory/managed-identities-azure-resources/overview.md) – áttekintés oldalra.   

## <a name="policies"></a>Házirendek

[Az Azure házirendek](../articles/azure-policy/azure-policy-introduction.md) a szervezet számára a kívánt viselkedés meghatározására használható [Windows virtuális gépek](../articles/virtual-machines/windows/policy.md) és [Linux rendszerű virtuális gépek](../articles/virtual-machines/linux/policy.md). Házirendek segítségével egy szervezet különböző konvenciók és szabályokat a vállalaton kényszeríthető. A kívánt viselkedés kényszerítésének segíthet hozzájárul a sikeres, a szervezet kockázatok.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Használatával [szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md), feladatköröket a csapaton belül, és csak olyan mértékű hozzáférést biztosítson a felhasználók számára, amelyek a feladataik elvégzéséhez szükségük van a virtuális gépen. Helyett, így mindenki korlátozás nélküli engedélyeket a virtuális gépen, engedélyezheti csak bizonyos műveleteket. Hozzáférés-vezérlés konfigurálhatja a virtuális géphez a [az Azure portal](../articles/role-based-access-control/role-assignments-portal.md)révén a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/role), vagy[Azure PowerShell-lel](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>További lépések
- Lépésről lépésre bemutatjuk a virtuális gép biztonságának monitorozása az Azure Security Center használatával [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).