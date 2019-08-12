---
title: Az SMB protokollon keresztüli Azure Active Directory hitelesítésének áttekintése Azure Files-Azure Storage-hoz
description: A Azure Files a Azure Active Directory (Azure AD) tartományi szolgáltatásokon keresztül támogatja az SMB (Server Message Block) protokollon keresztüli identitás-alapú hitelesítést. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek (VM-EK) az Azure AD-beli hitelesítő adatok használatával férhetnek hozzá az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/30/2019
ms.author: rogarana
ms.openlocfilehash: 604cf2bbe0cf8ab036c76ee9223d1ee34fd4bd3d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854577"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Az Azure Files Azure Active Directory tartományi szolgáltatás (Azure AD DS) hitelesítési támogatásának áttekintése SMB-hozzáféréshez
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Ha szeretné megtudni, hogyan engedélyezhető az Azure AD DS-hitelesítés a Azure Files számára, tekintse meg [a Azure Active Directory tartományi szolgáltatás hitelesítésének engedélyezése SMB protokollon keresztül Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Szószedet 
Hasznos megérteni az Azure AD tartományi szolgáltatás hitelesítésével kapcsolatos néhány kulcsfontosságú kifejezést a Azure Files SMB protokollon keresztül:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelési szolgáltatása. Az Azure AD egyetlen megoldásban ötvözi az alapvető Directory-szolgáltatásokat, az alkalmazások hozzáférés-kezelését és az identitások védelmét. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    A Azure AD Domain Services felügyelt tartományi szolgáltatásokat biztosít, például a tartományhoz való csatlakozást, a csoportházirendeket, az LDAP-t és a Kerberos/NTLM-hitelesítést. Ezek a szolgáltatások teljes mértékben kompatibilisek a Windows Server Active Directoryával. További információ: [Azure Active Directory (ad) tartományi szolgáltatások](../../active-directory-domain-services/overview.md).

-   **Azure-beli szerepköralapú Access Control (RBAC)**  
    Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. A RBAC használatával kezelheti az erőforrásokhoz való hozzáférést azáltal, hogy a felhasználóknak a feladatok végrehajtásához szükséges legkevesebb jogosultságot biztosítanak. További információ a RBAC: [Mi az a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-ban?](../../role-based-access-control/overview.md)

-   **Kerberos-hitelesítés**

    A Kerberos egy olyan hitelesítési protokoll, amely a felhasználó vagy a gazdagép identitásának ellenőrzésére szolgál. További információ a Kerberosról: [Kerberos-hitelesítés áttekintése](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protokoll**  
    Az SMB egy iparági szabványnak megfelelő hálózati fájlmegosztás protokoll. Az SMB-t a Common Internet File System vagy CIFS néven is nevezik. Az SMB-vel kapcsolatos további információkért lásd: a [Microsoft SMB protokoll és a CIFS protokoll áttekintése](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Az Azure AD tartományi szolgáltatás hitelesítésének előnyei
Az Azure AD tartományi szolgáltatás hitelesítése Azure Files számos előnyt kínál a megosztott kulcsos hitelesítés használata esetén:

-   **Az Azure AD-vel és az Azure AD tartományi szolgáltatással kiterjesztheti a hagyományos, identitás-alapú fájlmegosztás-hozzáférési élményt a felhőbe**  
    Ha azt tervezi, hogy az alkalmazást a felhőbe helyezi el, és lecseréli a hagyományos fájlkiszolgálók Azure Files segítségével, akkor előfordulhat, hogy az alkalmazásnak hitelesítenie kell az Azure AD hitelesítő adataival a fájlok eléréséhez. Azure Files támogatja az Azure AD-beli hitelesítő adatok használatát az Azure-beli AD DS tartományhoz csatlakoztatott Windows-alapú virtuális gépekről érkező SMB-Azure Files eléréséhez. Azt is megteheti, hogy az összes helyszíni Active Directory objektumot szinkronizálja az Azure AD-vel a felhasználónevek, jelszavak és más csoportos hozzárendelések megőrzése érdekében.

-   **Az Azure-fájlmegosztás részletes hozzáférés-vezérlésének betartatása**  
    Engedélyeket adhat meg egy adott identitáshoz a megosztás, a könyvtár vagy a fájl szintjén. Tegyük fel például, hogy több csapattal rendelkezik egyetlen Azure-fájlmegosztás használatával a projektek együttműködéséhez. Megadhatja, hogy az összes csapat hozzáférjen a nem bizalmas címtárakhoz, miközben a bizalmas pénzügyi adatokat tartalmazó címtárakhoz való hozzáférést csak a pénzügyi csapatának korlátozza. 

-   **ACL-ek biztonsági mentése az adataival együtt**  
    A meglévő helyszíni fájlmegosztás biztonsági mentéséhez Azure Files is használhatja. Azure Files megőrzi a hozzáférés-vezérlési listákat az adataival együtt, amikor biztonsági mentést készít egy fájlmegosztás számára az SMB-Azure Files keresztül.

## <a name="how-it-works"></a>Működés
A Azure Files a Azure AD Domain Services használatával támogatja a Kerberos-hitelesítést az Azure AD hitelesítő adataival a tartományhoz csatlakoztatott virtuális gépekről. Az Azure AD és a Azure Files használatának megkezdése előtt engedélyeznie kell Azure AD Domain Services és csatlakoztatnia kell a tartományt azokról a virtuális gépekről, amelyekről a fájl adatait szeretné elérni. A tartományhoz csatlakoztatott virtuális gépnek ugyanabban a virtuális hálózatban (VNET) kell lennie, mint Azure AD Domain Services. 

Ha egy virtuális gépen futó alkalmazáshoz tartozó identitás megpróbál hozzáférni Azure Filesban lévő adatokhoz, a rendszer elküldi a kérést az Azure AD Domain Servicesnak, hogy hitelesítse az identitást. Ha a hitelesítés sikeres, Azure AD Domain Services egy Kerberos-tokent ad vissza. Az alkalmazás olyan kérelmet küld, amely tartalmazza a Kerberos-tokent, és a Azure Files ezt a tokent használja a kérelem engedélyezéséhez. Azure Files csak a tokent kapja, és nem tart fenn Azure AD-beli hitelesítő adatokat.

![Az Azure AD-hitelesítés az SMB protokollon keresztül ábrázoló ábrája](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Az Azure AD tartományi szolgáltatás hitelesítésének engedélyezése SMB-hozzáféréshez
Az Azure AD tartományi szolgáltatás hitelesítését a 2018-es szeptember 24. után létrehozott új és meglévő Storage-fiókok Azure Files is engedélyezheti. 

A szolgáltatás engedélyezése előtt ellenőrizze, hogy a Azure AD Domain Services telepítve van-e az elsődleges Azure AD-bérlőhöz, amelyhez a Storage-fiók társítva van. Ha még nem állított be Azure AD Domain Services, kövesse az [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../../active-directory-domain-services/create-instance.md)című témakör lépésenkénti útmutatóját.

Azure AD Domain Services központi telepítés általában 10 – 15 percet vesz igénybe. Azure AD Domain Services telepítését követően engedélyezheti az Azure AD-hitelesítést az SMB protokollon keresztül a Azure Fileshoz. További információ: [Azure Active Directory tartományi szolgáltatás hitelesítésének engedélyezése az SMB protokollon keresztül Azure Files](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Megosztási szintű engedélyek konfigurálása Azure Fileshoz
Miután engedélyezte az Azure AD tartományi szolgáltatás hitelesítését, egyéni RBAC-szerepköröket konfigurálhat az Azure AD-identitásokhoz, és hozzáférési jogokat rendelhet a Storage-fiókban lévő összes fájlmegosztás számára.

Ha egy tartományhoz csatlakoztatott virtuális gépen futó alkalmazás megpróbál csatlakoztatni egy Azure-fájlmegosztást, vagy hozzáfér egy könyvtárhoz vagy fájlhoz, a rendszer ellenőrzi az alkalmazás Azure AD-beli hitelesítő adatait, hogy biztosítsa a megfelelő megosztási és NTFS-engedélyeket. További információ a megosztási szintű engedélyek konfigurálásáról: [Azure Active Directory tartományi szolgáltatás hitelesítésének engedélyezése SMB protokollon keresztül](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>A Azure Files könyvtár-vagy fájl szintű engedélyeinek konfigurálása 
Azure Files kikényszeríti a szabványos NTFS-fájlok engedélyeit a könyvtár és a fájl szintjén, beleértve a gyökérkönyvtárat is. A címtár vagy a fájl szintű engedélyek konfigurációja csak SMB protokollal támogatott. Csatlakoztassa a célfájl megosztását a virtuális gépről, és konfigurálja az engedélyeket a Windows fájlkezelő, a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy a [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) paranccsal. 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Rendszergazdai jogosultságok használata a Storage-fiók kulcsaként 
A Storage-fiók kulcsát birtokló felhasználó a rendszergazdai jogosultságokkal rendelkező Azure Fileshoz férhet hozzá. A rendszergazdai jogosultságok meghaladják a megosztási szinten konfigurált hozzáférés-vezérlési korlátozásokat a RBAC, és az Azure AD kényszeríti. Az Azure-fájlmegosztás csatlakoztatásához rendszergazdai jogosultságok szükségesek. 

> [!IMPORTANT]
> A biztonsággal kapcsolatos ajánlott eljárások részeként Kerülje a Storage-fiók kulcsainak megosztását, és ha lehetséges, használja az Azure AD-engedélyeket.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>A könyvtár és a fájl ACL-ek megőrzése az Azure-fájlmegosztás adatimportálásához
Azure Files mostantól támogatja a címtár vagy a fájl ACL-ek megőrzését az Azure-fájlmegosztásba való adatmásoláskor. Az ACL-eket egy könyvtárra vagy fájlra másolhatja Azure Filesba. A [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) és a jelző `/copy:s` együttes használatával például az Azure-fájlmegosztás adatfájljait és az ACL-eket is másolhatja. Az ACL megőrzése alapértelmezés szerint be van kapcsolva, és nem szükséges explicit módon engedélyezni az Azure AD tartományi szolgáltatás-hitelesítési szolgáltatást a Storage-fiókjában. 

## <a name="pricing"></a>Díjszabás
Az Azure AD-hitelesítés az SMB protokollon keresztüli engedélyezéséhez nincs további szolgáltatási díj a Storage-fiókban. A díjszabással kapcsolatos további információkért tekintse meg [Azure Files díjszabási](https://azure.microsoft.com/pricing/details/storage/files/) és [Azure ad Domain Services díjszabási](https://azure.microsoft.com/pricing/details/active-directory-ds/) lapjait.

## <a name="next-steps"></a>További lépések
A Azure Files és az Azure AD-hitelesítés SMB-kapcsolaton keresztüli használatáról további információt az alábbi forrásokban talál:

- [Bevezetés a Azure Filesba](storage-files-introduction.md)
- [Az SMB protokollon keresztüli Azure Active Directory hitelesítés engedélyezése Azure Files](storage-files-active-directory-enable.md)
- [Gyakori kérdések](storage-files-faq.md)
