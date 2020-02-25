---
title: Áttekintés – Azure Files identitás-alapú hitelesítés
description: Azure Files támogatja az SMB-(Server Message Block) identitás-alapú hitelesítést a Azure Active Directory Domain Services (AD DS) és a Active Directory használatával. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek (VM-EK) az Azure AD-beli hitelesítő adatok használatával férhetnek hozzá az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 3a9a2a903bd9979cd0f9a09b7589edc6d4fd8962
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565084"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítés támogatásának áttekintése
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

További információ az Azure-fájlmegosztás AD-hitelesítésének engedélyezéséről: [Active Directory hitelesítés engedélyezése SMB-en keresztül az Azure-fájlmegosztás számára](storage-files-active-directory-domain-services-enable.md).

Ha szeretné megtudni, hogyan engedélyezhető az Azure-AD DS hitelesítés az Azure-fájlmegosztás számára, tekintse meg a [Azure Active Directory tartományi szolgáltatás hitelesítésének engedélyezése SMB protokollon keresztül Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Szószedet 
Hasznos megérteni az Azure AD tartományi szolgáltatás hitelesítésével kapcsolatos egyes kulcsfontosságú kifejezéseket az Azure-fájlmegosztás SMB szolgáltatásán keresztül:

-   **Kerberos-hitelesítés**

    A Kerberos egy olyan hitelesítési protokoll, amely a felhasználó vagy a gazdagép identitásának ellenőrzésére szolgál. További információ a Kerberosról: [Kerberos-hitelesítés áttekintése](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protokoll**

    Az SMB egy iparági szabványnak megfelelő hálózati fájlmegosztás protokoll. Az SMB-t a Common Internet File System vagy CIFS néven is nevezik. Az SMB-vel kapcsolatos további információkért lásd: a [Microsoft SMB protokoll és a CIFS protokoll áttekintése](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelési szolgáltatása. Az Azure AD egyetlen megoldásban ötvözi az alapvető Directory-szolgáltatásokat, az alkalmazások hozzáférés-kezelését és az identitások védelmét. Az Azure AD lehetővé teszi, hogy a tartományhoz csatlakoztatott Windows-alapú virtuális gépek (VM-EK) hozzáférjenek az Azure-fájlmegosztás Azure AD-beli hitelesítő adataival. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirendek, az LDAP és a Kerberos/NTLM hitelesítés. Ezek a szolgáltatások teljes mértékben kompatibilisek a Windows Server Active Directoryával. További információ: [Azure Active Directory (ad) tartományi szolgáltatások](../../active-directory-domain-services/overview.md).

- **Active Directory tartományi szolgáltatások (AD DS, más néven AD)**

    Az Active Directory (AD) (előzetes verzió) a címtáradatok tárolásának módszereit biztosítja a hálózati felhasználók és a rendszergazdák számára. A biztonság integrálva van Active Directory a bejelentkezési hitelesítéssel és a címtárban található objektumok hozzáférés-vezérlésével. A rendszergazdák egyetlen hálózati bejelentkezéssel kezelhetik a címtáradatokat és a szervezetet a hálózaton belül, és a jogosult hálózati felhasználók hozzáférhetnek az erőforrásokhoz a hálózat bármely pontján. Az AD-t a helyszíni vállalatok általában elfogadják, és az AD hitelesítő adatokat használják identitásként a hozzáférés-vezérléshez. További információ: [Active Directory tartományi szolgáltatások Overview (áttekintés](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

-   **Azure-beli szerepköralapú Access Control (RBAC)**

    Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. A RBAC használatával kezelheti az erőforrásokhoz való hozzáférést azáltal, hogy a felhasználóknak a feladatok végrehajtásához szükséges legkevesebb jogosultságot biztosítanak. További információ a RBAC: [Mi az az Azure-beli szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Az identitás-alapú hitelesítés és a Windows ACL-ek támogatása a Azure Files esetében a legmegfelelőbb a következő felhasználási esetekben:

### <a name="replace-on-premises-file-servers"></a>Helyszíni fájlkiszolgálók cseréje

A szétszórt helyszíni fájlkiszolgálók elavulttá váltása és cseréje gyakori probléma, hogy minden vállalat az informatikai modernizációs folyamat során találkozik. Az AD (előzetes verzió) hitelesítéssel rendelkező Azure-fájlmegosztás itt a legjobb megoldás, ha Azure Filesba telepítheti át az adatátvitelt. A teljes áttelepítéssel kihasználhatja a magas rendelkezésre állást és a méretezhetőség előnyeit, ugyanakkor minimalizálhatja az ügyféloldali változásokat, különösen a bonyolult AD tartományi infrastruktúrát is. Zökkenőmentes áttelepítési élményt biztosít a végfelhasználók számára, így a meglévő tartományhoz csatlakoztatott gépek használatával továbbra is hozzáférhetnek az adataikkal.

### <a name="lift-and-shift-applications-to-azure"></a>Alkalmazások átemelése és átirányítása az Azure-ba

Ha az alkalmazásokat a felhőbe szeretné emelni és átirányítani, ugyanazt a hitelesítési modellt kívánja megőrizni az adataihoz. Ahogy kiterjesztjük az Identity-alapú hozzáférés-vezérlési élményt az Azure-fájlmegosztás számára, nem kell módosítania az alkalmazást a modern hitelesítési módszerekre, és fel kell gyorsítani a felhőbe való bevezetést. Az Azure file shares lehetővé teszi a hitelesítéshez az Azure AD DS (GA) vagy az AD (előzetes verzió) szolgáltatással való integrálás lehetőségét. Ha a terve a 100%-os felhőbe kerül, és a Felhőbeli infrastruktúrák kezelésének minimalizálására törekszik, az Azure AD DS jobban illeszkednek a teljes körűen felügyelt tartományi szolgáltatásokhoz. Ha AD DS (GA) képességekkel való teljes kompatibilitásra van szüksége, érdemes megfontolnia az AD-környezet kibővítését a felhőbe a virtuális gépeken lévő saját üzemeltetésű tartományvezérlők használatával. Mindkét esetben rugalmasságot biztosítunk az üzleti igényeknek megfelelő tartományi szolgáltatások kiválasztásához.

### <a name="backup-and-disaster-recovery-dr"></a>Biztonsági mentés és vész-helyreállítás (DR)

Ha az elsődleges tárterületet a helyszínen tartja, az Azure-fájlmegosztás ideális tárolóhelyként szolgálhat a Backup vagy a DR számára az üzletmenet folytonosságának javítása érdekében. Az Azure-fájlmegosztás használatával biztonsági mentést készíthet az adatokról a meglévő fájlkiszolgálók alapján, miközben megőrizheti a Windows DACL-kiszolgálókat. DR-forgatókönyvek esetén konfigurálhat egy olyan hitelesítési lehetőséget, amely támogatja a megfelelő hozzáférés-vezérlés kényszerítést a feladatátvétel során.

## <a name="supported-scenarios"></a>Támogatott esetek

Az alábbi táblázat az Azure AD DS (GA) és az AD (előzetes verzió) támogatott Azure fájlmegosztás-hitelesítési forgatókönyveit foglalja össze. Azt javasoljuk, hogy válassza ki azt a tartományi szolgáltatást, amelyet az ügyfél-környezethez az Azure Fileshoz való integrációhoz fogadott. Ha az AD (előzetes verzió) már telepítve van a helyszínen vagy az Azure-ban, ahol az eszközök tartományhoz csatlakoznak az AD-hez, az Azure fájlmegosztás-hitelesítéshez az AD (előzetes verzió) szolgáltatást kell használnia. Hasonlóképpen, ha már elvégezte az Azure AD DS (GA) használatát, használja ezt az Azure fájlmegosztás-hitelesítéshez.


|Azure AD DS (GA) hitelesítés  |AD (előzetes verzió) hitelesítés  |
|---------|---------|
|Az Azure AD DS tartományhoz csatlakozó Windows rendszerű gépek az Azure AD hitelesítő adataival érhetik el az Azure-fájlmegosztást az SMB protokollon keresztül.     |Az AD-tartományhoz csatlakoztatott Windows rendszerű gépek olyan AD hitelesítő adatokkal férhetnek hozzá az Azure-fájlmegosztáshoz, amelyeket az SMB-kapcsolaton keresztül szinkronizált az Azure AD         |

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

- Az Azure AD DS (GA) és az AD (előzetes verzió) hitelesítés nem támogatja a számítógép-fiókok hitelesítését. Ehelyett érdemes lehet szolgáltatás-bejelentkezési fiókot használni.
- Az Azure AD DS (GA) hitelesítése nem támogatja az Azure AD Cloud-hoz csatlakoztatott eszközök hitelesítését.

## <a name="advantages-of-identity-based-authentication"></a>Az identitás-alapú hitelesítés előnyei
A Azure Files identitás-alapú hitelesítése számos előnyt kínál a megosztott kulcsos hitelesítés használata esetén:

-   **Terjessze ki a hagyományos, identitás-alapú fájlmegosztást a felhőbe az AD és az Azure AD DS**  
    Ha azt tervezi, hogy az alkalmazást a felhőbe helyezi át, és a hagyományos fájlkiszolgálók cseréjét az Azure-fájlmegosztás használatával szeretné elvégezni, akkor előfordulhat, hogy az alkalmazás az AD vagy az Azure AD hitelesítő adataival fér hozzá a fájlokhoz. Azure Files mindkét AD-vagy Azure AD-hitelesítő adat használatát támogatja az Azure-fájlmegosztás SMB-kapcsolaton keresztüli eléréséhez vagy az AD vagy az Azure AD DS tartományhoz csatlakoztatott virtuális gépekről.

-   **Az Azure-fájlmegosztás részletes hozzáférés-vezérlésének betartatása**  
    Engedélyeket adhat meg egy adott identitáshoz a megosztás, a könyvtár vagy a fájl szintjén. Tegyük fel például, hogy több csapattal rendelkezik egyetlen Azure-fájlmegosztás használatával a projektek együttműködéséhez. Megadhatja, hogy az összes csapat hozzáférjen a nem bizalmas címtárakhoz, miközben a bizalmas pénzügyi adatokat tartalmazó címtárakhoz való hozzáférést csak a pénzügyi csapatának korlátozza. 

-   **A Windows ACL-ek (más néven NTFS) biztonsági mentése az adataival együtt**  
    Az Azure-fájlmegosztást használhatja a meglévő helyszíni fájlmegosztás biztonsági mentésére. Azure Files megőrzi az ACL-eket az adataival együtt, amikor biztonsági másolatot készít az Azure-fájlmegosztás SMB protokollon keresztüli megosztásáról.

## <a name="how-it-works"></a>Működés
Az Azure-fájlmegosztás támogatja a Kerberos-hitelesítést az Azure AD DS (GA) vagy az AD (előzetes verzió) szolgáltatással való integrációhoz. Az Azure-fájlmegosztás hitelesítésének engedélyezéséhez először a tartományi környezetet kell beállítania. Az Azure AD DS (GA) hitelesítéshez engedélyeznie kell a Azure AD Domain Services és a tartományhoz való csatlakozást azokon a virtuális gépeken, amelyeken a fájl adatait el szeretné érni. A tartományhoz csatlakoztatott virtuális gépnek ugyanabban a virtuális hálózatban (VNET) kell lennie, mint a Azure AD Domain Services. Az AD (előzetes verzió) hitelesítéshez hasonlóan be kell állítania a Active Directory tartományvezérlőt és a tartományhoz való csatlakozást a számítógépekhez vagy virtuális gépekhez.

Ha egy virtuális gépen futó alkalmazáshoz tartozó identitás megpróbál hozzáférni az Azure-fájlmegosztás adataihoz, a rendszer elküldi a kérést Azure AD Domain Services az identitás hitelesítéséhez. Ha a hitelesítés sikeres, Azure AD Domain Services egy Kerberos-tokent ad vissza. Az alkalmazás olyan kérelmet küld, amely tartalmazza a Kerberos-tokent, és az Azure-fájlmegosztás ezt a tokent használja a kérelem engedélyezéséhez. Az Azure-fájlmegosztás csak a jogkivonatot fogadja, és nem tart fenn Azure AD-beli hitelesítő adatokat. Az AD-hitelesítés hasonló módon működik, ahol az AD biztosítja a Kerberos-tokent.

![Az Azure AD-hitelesítés az SMB protokollon keresztül ábrázoló ábrája](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Identitás-alapú hitelesítés engedélyezése

Az Azure-fájlmegosztást az új és meglévő Storage-fiókok Azure AD DS (GA) vagy AD (előzetes verzió) használatával engedélyezheti. Csak egy tartományi szolgáltatás használható a fájl-hozzáférési hitelesítéshez a Storage-fiókban, amely a fiókban lévő összes fájlmegosztás esetében érvényes. Részletes útmutató a fájlmegosztás az Azure AD DS (GA) szolgáltatással való beállításához, amely [lehetővé Azure Active Directory Domain Services teszi az SMB-en keresztüli hitelesítés használatát Azure Files](storage-files-active-directory-enable.md) és útmutató az ad (előzetes verzió) számára a további cikkben, az [Azure-fájlmegosztás esetében engedélyezze a Active Directoryt az SMB protokollon](storage-files-active-directory-domain-services-enable.md)keresztül.

### <a name="configure-share-level-permissions-for-azure-files"></a>Megosztási szintű engedélyek konfigurálása Azure Fileshoz

Ha az Azure AD DS (GA) vagy az AD (előzetes verzió) hitelesítés engedélyezve van, használhatja a beépített RBAC-szerepköröket, vagy egyéni szerepköröket konfigurálhat az Azure AD-identitásokhoz, valamint hozzáférési jogosultságokat rendelhet a Storage-fiókokban lévő összes fájlmegosztási megosztáshoz. a hozzárendelt engedély lehetővé teszi, hogy a megadott identitás csak a megosztáshoz kapjon hozzáférést, semmi más, még a gyökérkönyvtárban sem. Az Azure-fájlmegosztás esetében továbbra is külön kell konfigurálnia a címtár-vagy a fájl szintű engedélyeket.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>A Azure Files könyvtár-vagy fájl szintű engedélyeinek konfigurálása

Az Azure-fájlmegosztás a könyvtár és a fájl szintjén is kikényszeríti a Windows-fájlok szabványos engedélyeit, beleértve a gyökérkönyvtárat is. A címtár vagy a fájl szintű engedélyek konfigurációja az SMB és a REST protokollon keresztül is támogatott. Csatlakoztassa a célfájl megosztását a virtuális gépről, és konfigurálja az engedélyeket a Windows fájlkezelő, a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)vagy a [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) parancs használatával.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Rendszergazdai jogosultságok használata a Storage-fiók kulcsaként

A Storage-fiók kulcsát birtokló felhasználó az Azure-fájlmegosztást rendszergazdai jogosultságokkal érheti el. Rendszergazdai jogosultságok az összes hozzáférés-vezérlési korlátozás mellőzése.

> [!IMPORTANT]
> Ajánlott biztonsági eljárásunk a Storage-fiókok kulcsai megosztásának elkerülése, és ha lehetséges, az identitás-alapú hitelesítés kihasználása.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Címtár és fájl ACL-ek megőrzése az Azure-fájlmegosztásba való adatimportáláskor

Azure Files támogatja az adatok Azure-fájlmegosztásba való másolásakor a címtár vagy a fájl szintű ACL-ek megőrzését. Az ACL-eket egy könyvtárra vagy fájlra másolhatja az Azure-fájlmegosztás Azure File Sync vagy közös fájl-áthelyezési eszközkészlet használatával. A [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) és a `/copy:s` jelző használatával például az Adatmásolás és az Azure-fájlmegosztás ACL-jei is használhatók. Az ACL-ek alapértelmezés szerint megmaradnak, és az ACL-ek megőrzése érdekében nem szükséges az identitás-alapú hitelesítés engedélyezése a Storage-fiókban.

## <a name="pricing"></a>Díjszabás
A Storage-fiókjában nem található további szolgáltatás, amely lehetővé teszi az identitás-alapú hitelesítés használatát az SMB protokollon keresztül. A díjszabással kapcsolatos további információkért tekintse meg [Azure Files díjszabási](https://azure.microsoft.com/pricing/details/storage/files/) és [Azure ad Domain Services díjszabási](https://azure.microsoft.com/pricing/details/active-directory-ds/) oldalát, ha HRE DS-információkat keres.

## <a name="next-steps"></a>További lépések
Az SMB-en keresztüli Azure Files-és identitás-alapú hitelesítéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Az Azure-fájlmegosztás SMB-en keresztüli Active Directory hitelesítésének engedélyezése](storage-files-active-directory-domain-services-enable.md)
- [Azure Active Directory Domain Services hitelesítés engedélyezése Azure Files](storage-files-active-directory-enable.md)
- [GYIK](storage-files-faq.md)
