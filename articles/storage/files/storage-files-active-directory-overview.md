---
title: Az Azure Files (előzetes verzió) – Azure Storage SMB-n keresztül az Azure Active Directory-hitelesítés áttekintése
description: Az Azure Files SMB (Server Message Block) (előzetes verzió) révén az Azure Active Directory (Azure AD) Domain Services azonosító-alapú hitelesítést támogatja. A tartományhoz csatlakoztatott Windows virtuális gépek (VM) ezután hozzáférhetnek az Azure-fájlmegosztásokat az Azure AD hitelesítő adatait.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 1962a3237fb54409d17fefa314605bafa91c3e9c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427637"
---
# <a name="overview-of-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Az Azure Files (előzetes verzió) SMB-n keresztül az Azure Active Directory-hitelesítés áttekintése
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Az Azure AD-hitelesítés engedélyezése az Azure Files SMB-n keresztül kapcsolatban lásd: [engedélyezése az Azure Active Directory-hitelesítés SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Szószedet 
Hasznos lehet megérteni az egyes Azure AD-hitelesítéssel kapcsolatos SMB-n keresztül az Azure Files számára legfontosabb kifejezések:

-   **Az Azure Active Directory (Azure AD)**  
    Az Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Az Azure AD alapvető címtárszolgáltatásokat, alkalmazáshozzáférés-felügyeletet és identitásvédelmet egyetlen megoldásban egyesíti. További információkért lásd: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Az Azure AD tartományi szolgáltatások**  
    Az Azure AD Domain Services biztosítja a felügyelt tartományi szolgáltatásokat, például a tartományhoz való csatlakozás, a csoportházirendek, az LDAP és a Kerberos/NTLM hitelesítés. Ezek a szolgáltatások teljes mértékben kompatibilisek a Windows Server Active Directoryval. További információkért lásd: [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md).

-   **Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)**  
    Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. RBAC használata esetén kezelheti erőforrásokhoz való hozzáférés biztosítása a felhasználóknak a feladataik elvégzéséhez szükséges legkevesebb engedélyeket. Az RBAC további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-ban?](../../role-based-access-control/overview.md)

-   **A Kerberos-hitelesítés**

    A Kerberos egy olyan hitelesítési protokoll, amely a felhasználó vagy a gazdagép identitásának ellenőrzésére szolgál. A Kerberos további információkért lásd: [Kerberos-hitelesítés áttekintése](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protokoll**  
    Az SMB egy iparági szabványnak megfelelő hálózati fájlmegosztási protokoll. SMB Common Internet File System vagy CIFS is nevezik. SMB további információkért lásd: [Microsoft SMB protokoll és a CIFS protokoll – áttekintés](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-authentication"></a>Az Azure AD-hitelesítés előnyei
Az Azure Files SMB-n keresztül az Azure AD a képest megosztott kulcsos hitelesítés használata számos előnyt biztosít:

-   **Kiterjesztheti a hagyományos azonosító-alapú fájl megosztás élményt a felhőben az Azure ad-vel**  
    Ha azt tervezi, hogy "átemeléses" az alkalmazás a felhőbe, és cserélje le a hagyományos fájlkiszolgálók, az Azure Files, akkor érdemes lehet az alkalmazás hitelesítéséhez az Azure AD-fájlokban tárolt adatokhoz hozzáférést. Az Azure fájlok támogatja az Azure AD hitelesítő adatait, tartományhoz csatlakoztatott virtuális gépek SMB-n keresztül hozzáférés fájl megosztások, könyvtárak vagy fájlok használatát. Szinkronizálja az Azure AD segítségével megőrizheti a felhasználónevek, jelszavak és egyéb csoport-hozzárendeléseit a helyszíni Active Directory-objektumok mindegyike azt is beállíthatja.

-   **Részletes hozzáférés-vezérlés az Azure-fájlmegosztások kényszerítése**  
    Az Azure AD-hitelesítés SMB-n keresztül megadhatja az engedélyeket, a megosztás, könyvtárat vagy fájlt szintjén egy bizonyos identitásra. Tegyük fel, hogy több csapatokkal, egy egyetlen Azure-fájlmegosztás együttműködéshez szükséges. Az összes csapatok hozzáférést biztosíthat a nem bizalmas-címtárak esetén pedig korlátozhatja a hozzáférést a címtárakhoz csak a pénzügyi csapata bizalmas pénzügyi adatokat tartalmazó. 

-   **Hozzáférés-vezérlési listák készíteni az adatokkal együtt**  
    Az Azure Files használatával a meglévő helyszíni fájlmegosztások biztonsági mentése. Az Azure Files megőrzi a hozzáférés-vezérlési listák az adatokkal együtt, megosztásakor a fájlmegosztások biztonsági mentése az Azure Files SMB-n keresztül.

## <a name="how-it-works"></a>Működés
Az Azure Files Azure AD tartományi szolgáltatásokat használja a Kerberos-hitelesítés az Azure AD hitelesítő adatait, tartományhoz csatlakoztatott virtuális gépek. Azure ad-ben az Azure Files használata előtt először az Azure AD tartományi szolgáltatások engedélyezése és a tartományhoz, a virtuális gépekről, amelyből hozzá szeretne férni a fájlokban tárolt adatokhoz. A tartományhoz csatlakoztatott virtuális gép ugyanazon a virtuális hálózaton (VNET), az Azure AD Domain Services kell lennie. 

Amikor a rendszer identitást társít egy virtuális gépen futó alkalmazásokhoz próbál meg hozzáférni az Azure Files adatokhoz, a kérelem érkezik az Azure AD tartományi szolgáltatásokra az identitás hitelesítésére. Sikeres hitelesítés esetén az Azure AD tartományi szolgáltatások Kerberos jogkivonatot ad vissza. Az alkalmazás elküld egy kérelmet, amely tartalmazza a Kerberos-jogkivonatot, és az Azure Files használja ezt a jogkivonatot a kérelem engedélyezéséhez. Az Azure Files csak a jogkivonatot kap, és nem marad meg az Azure AD hitelesítő adatait.

![Képernyőkép ábrázoló diagram SMB-n keresztül az Azure AD-hitelesítés](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-authentication-over-smb"></a>Az SMB-n keresztüli Azure AD-hitelesítés engedélyezése
Az Azure AD-hitelesítés 2018. szeptember 24. után létrehozott új és meglévő tárfiókok az Azure Files SMB-n keresztül engedélyezhető. 

Az Azure AD-hitelesítés engedélyezése az SMB-n keresztül, előtt győződjön meg arról, hogy az Azure AD tartományi szolgáltatások az elsődleges lett-e telepítve. az Azure AD-bérlő, amelyhez társítva a tárfiók. Ha még nem állította be az Azure AD tartományi szolgáltatások, az útmutatót lépésről lépésre követve megadott [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md).

Az Azure AD tartományi szolgáltatások üzembe helyezése általában 10 – 15 percet vesz igénybe. Az Azure AD Domain Services telepítése után engedélyezheti az Azure AD-hitelesítés SMB-n keresztül az Azure Files számára. További információkért lásd: [engedélyezése az Azure Active Directory-hitelesítés SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Az Azure Files megosztási szintű engedélyeinek konfigurálása
Miután az Azure AD-hitelesítés engedélyezve van, Azure AD-identitások egyéni RBAC-szerepkörök konfigurálása, és hozzárendelheti a hozzáférési jogosultsága ahhoz, hogy a tárfiókban lévő összes fájlmegosztást.

Amikor az a tartományhoz csatlakoztatott virtuális gépeken futó alkalmazások csatlakoztatása az Azure-fájlmegosztások vagy fájlok vagy könyvtárak, az alkalmazás Azure AD hitelesítő adatait a rendszer ellenőrzi a megfelelő megosztási szintű engedélyeket és NTFS-engedélyek biztosítása érdekében. Megosztás szintű engedélyek konfigurálásával kapcsolatos további információkért lásd: [engedélyezése az Azure Active Directory-hitelesítés (előzetes verzió) az SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Az Azure Files könyvtár - és fájlszintű-engedélyek konfigurálása 
Az Azure Files standard NTFS engedélyek fájl és könyvtár szinten, beleértve a gyökérkönyvtárban érvénybe lépteti. Címtár - és fájlszintű engedélyekkel a konfiguráció csak támogatott SMB-n keresztül. A célként megadott fájlmegosztás csatlakoztatása a virtuális gépről, és használja a Windows-engedélyek konfigurálása [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) parancsot. 

> [!NOTE]
> Az előzetes verzióban érhető el a Windows Fájlkezelővel NTFS-engedélyek konfigurálása nem támogatott.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>A tárfiók kulcsát használja a felügyelői engedélyekkel 
A tárfiók kulcsát rendelkező felhasználó hozzáférhet az Azure Files felügyelői engedélyekkel. Felügyelői engedélyekkel előremutató minden hozzáférés ellenőrzési korlátozások RBAC rendelkező megosztási szintjén állítható be, és kikényszeríti a Azure ad-ben. Az Azure-fájlmegosztás csatlakoztatása SUPERUSER engedélyek szükségesek. 

> [!IMPORTANT]
> Ajánlott biztonsági eljárások részeként elkerülése érdekében a storage-fiókkulcsok megosztása, és kihasználhatja az Azure AD-engedélyekről, amikor csak lehetséges.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Könyvtár- és hozzáférés-vezérlési adatok importálása az Azure-fájlmegosztások őrizni
SMB-n keresztül az Azure AD-hitelesítés megőrző könyvtár vagy fájl hozzáférés-vezérlési listák támogatja, ha az adatok másolása Azure-fájlmegosztások. Az előzetes kiadásban a hozzáférés-vezérlési listák fájlok vagy könyvtárak másolhatja az Azure Files. Használhatja például [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) jelölővel `/copy:s` adatok és a hozzáférés-vezérlési listák Azure-fájlmegosztás másolása.

## <a name="pricing"></a>Díjszabás
Nem jár további szolgáltatás Azure AD-hitelesítés engedélyezéséhez az SMB-n keresztül a storage-fiók. További információ a díjszabásról lásd: [Azure Files díjszabása](https://azure.microsoft.com/pricing/details/storage/files/) és [az Azure AD Domain Services díjszabási](https://azure.microsoft.com/pricing/details/active-directory-ds/) oldalak.

## <a name="next-steps"></a>További lépések
További információ az Azure Files és az Azure AD hitelesítési SMB-n keresztül az alábbi forrásokban talál:

- [Bevezetés az Azure Files használatába](storage-files-introduction.md)
- [Az Azure Active Directory-hitelesítés engedélyezése az SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-enable.md)
- [Gyakori kérdések](storage-files-faq.md)