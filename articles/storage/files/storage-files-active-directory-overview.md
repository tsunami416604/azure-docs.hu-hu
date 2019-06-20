---
title: Az Azure Files (előzetes verzió) – Azure Storage SMB-n keresztül az Azure Active Directory-hitelesítés áttekintése
description: Az Azure Files SMB (Server Message Block) (előzetes verzió) révén az Azure Active Directory (Azure AD) Domain Services azonosító-alapú hitelesítést támogatja. A tartományhoz csatlakoztatott Windows virtuális gépek (VM) ezután hozzáférhetnek az Azure-fájlmegosztásokat az Azure AD hitelesítő adatait.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269358"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Az Azure Files az Azure Active Directory tartományi szolgáltatások (AAD-DS) hitelesítési támogatás a SMB-hozzáférés (előzetes verzió) áttekintése
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Engedélyezése AAD Tartományi hitelesítés az Azure Files kapcsolatban lásd: [engedélyezése az Azure Active Directory tartományi szolgáltatás hitelesítése az Azure Files (előzetes verzió) az SMB-n keresztül](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Szószedet 
Hasznos lehet tudni, hogy néhány Azure Files SMB-n keresztül az Azure AD tartományi szolgáltatások hitelesítési kapcsolatos legfontosabb kifejezések:

-   **Az Azure Active Directory (Azure AD)**  
    Az Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Az Azure AD alapvető címtárszolgáltatásokat, alkalmazáshozzáférés-felügyeletet és identitásvédelmet egyetlen megoldásban egyesíti. További információkért lásd: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Az Azure AD tartományi szolgáltatások**  
    Az Azure AD Domain Services biztosítja a felügyelt tartományi szolgáltatásokat, például a tartományhoz való csatlakozás, a csoportházirendek, az LDAP és a Kerberos/NTLM hitelesítés. Ezek a szolgáltatások teljes mértékben kompatibilisek a Windows Server Active Directoryval. További információkért lásd: [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

-   **Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)**  
    Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. RBAC használata esetén kezelheti erőforrásokhoz való hozzáférés biztosítása a felhasználóknak a feladataik elvégzéséhez szükséges legkevesebb engedélyeket. Az RBAC további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-ban?](../../role-based-access-control/overview.md)

-   **A Kerberos-hitelesítés**

    A Kerberos egy olyan hitelesítési protokoll, amely a felhasználó vagy a gazdagép identitásának ellenőrzésére szolgál. A Kerberos további információkért lásd: [Kerberos-hitelesítés áttekintése](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protokoll**  
    Az SMB egy iparági szabványnak megfelelő hálózati fájlmegosztási protokoll. SMB Common Internet File System vagy CIFS is nevezik. SMB további információkért lásd: [Microsoft SMB protokoll és a CIFS protokoll – áttekintés](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Azure AD tartományi szolgáltatások hitelesítés előnyei
Az Azure Files számára az Azure AD tartományi szolgáltatások authentication keresztül megosztott kulcsos hitelesítést használ, olyan előnyöket kínál:

-   **Kiterjesztheti a hagyományos azonosító-alapú fájl megosztási élményt a felhőben az Azure AD és az Azure AD tartományi szolgáltatások**  
    Ha azt tervezi, hogy a "lift and shift" az alkalmazás a felhőbe, és cserélje le a hagyományos fájlkiszolgálókhoz az Azure Files, akkor érdemes lehet az Azure AD-hitelesítést az alkalmazás hozzáférési fájlokban tárolt adatokhoz hitelesítő adatok. Az Azure Files támogatja az Azure AD hitelesítő adatait az AAD Tartományi SMB-n keresztül az Azure Files eléréséhez használt tartományhoz csatlakoztatott Windows virtuális gépek. Szinkronizálja az Azure AD segítségével megőrizheti a felhasználónevek, jelszavak és egyéb csoport-hozzárendeléseit a helyszíni Active Directory-objektumok mindegyike azt is beállíthatja.

-   **Részletes hozzáférés-vezérlés az Azure-fájlmegosztások kényszerítése**  
    Megadhatja az engedélyeket, a megosztás, könyvtárat vagy fájlt szintjén egy bizonyos identitásra. Tegyük fel, hogy több csapatokkal, egy egyetlen Azure-fájlmegosztás együttműködéshez szükséges. Az összes csapatok hozzáférést biztosíthat a nem bizalmas-címtárak esetén pedig korlátozhatja a hozzáférést a címtárakhoz csak a pénzügyi csapata bizalmas pénzügyi adatokat tartalmazó. 

-   **Hozzáférés-vezérlési listák készíteni az adatokkal együtt**  
    Az Azure Files használatával a meglévő helyszíni fájlmegosztások biztonsági mentése. Az Azure Files megőrzi a hozzáférés-vezérlési listák az adatokkal együtt, megosztásakor a fájlmegosztások biztonsági mentése az Azure Files SMB-n keresztül.

## <a name="how-it-works"></a>Működés
Az Azure Files Azure AD tartományi szolgáltatásokat használja a Kerberos-hitelesítés az Azure AD hitelesítő adatait, tartományhoz csatlakoztatott virtuális gépek. Azure ad-ben az Azure Files használata előtt először az Azure AD tartományi szolgáltatások engedélyezése és a tartományhoz, a virtuális gépekről, amelyből hozzá szeretne férni a fájlokban tárolt adatokhoz. A tartományhoz csatlakoztatott virtuális gép az Azure AD Domain Services azonos virtuális hálózaton (VNET) kell lennie. 

Amikor a rendszer identitást társít egy virtuális gépen futó alkalmazásokhoz próbál meg hozzáférni az Azure Files adatokhoz, a kérelem érkezik az Azure AD tartományi szolgáltatásokra az identitás hitelesítésére. Sikeres hitelesítés esetén az Azure AD tartományi szolgáltatások Kerberos jogkivonatot ad vissza. Az alkalmazás elküld egy kérelmet, amely tartalmazza a Kerberos-jogkivonatot, és az Azure Files használja ezt a jogkivonatot a kérelem engedélyezéséhez. Az Azure Files csak a jogkivonatot kap, és nem marad meg az Azure AD hitelesítő adatait.

![Képernyőkép ábrázoló diagram SMB-n keresztül az Azure AD-hitelesítés](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Az SMB-hozzáférést Azure AD tartományi szolgáltatások hitelesítésének engedélyezése
A 2018. szeptember 24. után létrehozott új és meglévő storage-fiókok az Azure Files Azure AD tartományi szolgáltatások hitelesítési engedélyezheti. 

A szolgáltatás engedélyezése előtt győződjön meg arról, hogy az Azure AD tartományi szolgáltatások az elsődleges lett-e telepítve. az Azure AD-bérlő, amelyhez társítva a tárfiók. Ha még nem állította be az Azure AD tartományi szolgáltatások, az útmutatót lépésről lépésre követve megadott [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/create-instance.md).

Az Azure AD tartományi szolgáltatások üzembe helyezése általában 10 – 15 percet vesz igénybe. Az Azure AD Domain Services telepítése után engedélyezheti az Azure AD-hitelesítés SMB-n keresztül az Azure Files számára. További információkért lásd: [SMB-n keresztül az Azure Files (előzetes verzió) engedélyezése az Azure Active Directory tartományi szolgáltatások hitelesítés](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Az Azure Files megosztási szintű engedélyeinek konfigurálása
Azure AD tartományi szolgáltatások hitelesítés engedélyezését követően, az Azure AD-identitások egyéni RBAC-szerepkörök konfigurálása, és hozzárendelheti hozzáférési jogosultsága ahhoz, hogy a tárfiókban lévő összes fájlmegosztást.

Amikor az a tartományhoz csatlakoztatott virtuális gépeken futó alkalmazások csatlakoztatása az Azure-fájlmegosztások vagy fájlok vagy könyvtárak, az alkalmazás Azure AD hitelesítő adatait a rendszer ellenőrzi a megfelelő megosztási szintű engedélyeket és NTFS-engedélyek biztosítása érdekében. Megosztás szintű engedélyek konfigurálásával kapcsolatos további információkért lásd: [(előzetes verzió) az SMB engedélyezéséhez az Azure Active Directory tartományi szolgáltatások hitelesítési](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Az Azure Files könyvtár - és fájlszintű-engedélyek konfigurálása 
Az Azure Files standard NTFS engedélyek fájl és könyvtár szinten, beleértve a gyökérkönyvtárban érvénybe lépteti. Címtár - és fájlszintű engedélyekkel a konfiguráció csak támogatott SMB-n keresztül. A célként megadott fájlmegosztás csatlakoztatása a virtuális gépről, és használja a Windows-engedélyek konfigurálása [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) parancsot. 

> [!NOTE]
> Az előzetes verzióban érhető el a Windows Fájlkezelővel NTFS-engedélyek konfigurálása nem támogatott.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>A tárfiók kulcsát használja a felügyelői engedélyekkel 
A tárfiók kulcsát rendelkező felhasználó hozzáférhet az Azure Files felügyelői engedélyekkel. Felügyelői engedélyekkel előremutató minden hozzáférés ellenőrzési korlátozások RBAC rendelkező megosztási szintjén állítható be, és kikényszeríti a Azure ad-ben. Az Azure-fájlmegosztás csatlakoztatása SUPERUSER engedélyek szükségesek. 

> [!IMPORTANT]
> Ajánlott biztonsági eljárások részeként elkerülése érdekében a storage-fiókkulcsok megosztása, és kihasználhatja az Azure AD-engedélyekről, amikor csak lehetséges.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Könyvtár- és hozzáférés-vezérlési adatok importálása az Azure-fájlmegosztások őrizni
Az Azure Files már támogatja a fájl vagy könyvtár hozzáférés-vezérlési listák megőrzi az adatok Azure-fájlmegosztások másolásakor. Az Azure Files másolhatja a hozzáférés-vezérlési listák egy könyvtárat vagy fájlt. Használhatja például [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) jelölővel `/copy:s` adatok és a hozzáférés-vezérlési listák Azure-fájlmegosztás másolása. ACL megőrzését alapértelmezés szerint be van kapcsolva, és nem kell explicit módon engedélyezze az Azure AD tartományi szolgáltatások hitelesítési funkció, a storage-fiókjában. 

## <a name="pricing"></a>Díjszabás
Nem jár további szolgáltatás Azure AD-hitelesítés engedélyezéséhez az SMB-n keresztül a storage-fiók. További információ a díjszabásról lásd: [Azure Files díjszabása](https://azure.microsoft.com/pricing/details/storage/files/) és [az Azure AD Domain Services díjszabási](https://azure.microsoft.com/pricing/details/active-directory-ds/) oldalak.

## <a name="next-steps"></a>További lépések
További információ az Azure Files és az Azure AD hitelesítési SMB-n keresztül az alábbi forrásokban talál:

- [Bevezetés az Azure Files használatába](storage-files-introduction.md)
- [Az Azure Active Directory-hitelesítés engedélyezése az SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-enable.md)
- [Gyakori kérdések](storage-files-faq.md)
