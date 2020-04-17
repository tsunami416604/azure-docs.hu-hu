---
title: Áttekintés - Azure Files identitásalapú engedélyezés
description: Az Azure Files támogatja az identitásalapú hitelesítést Az SMB (Server Message Block) az Azure Active Directory tartományi szolgáltatások (AD DS) és az Active Directory használatával. A tartományhoz csatlakozó Windows virtuális gépek (VM-ek) ezután azure-fájlmegosztások azure AD hitelesítő adatok használatával férhetnek hozzá.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536580"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Az Azure Files identitásalapú hitelesítési támogatásának áttekintése az SMB-hozzáféréshez
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Ha tudni szeretné, hogyan engedélyezheti az Azure-fájlmegosztások helyszíni Active Directory tartományi szolgáltatások hitelesítését (előzetes verzió), olvassa el [A helyszíni Active Directory tartományi szolgáltatások hitelesítésének engedélyezése SMB-n keresztül az Azure-fájlmegosztások hoz](storage-files-identity-auth-active-directory-enable.md)című témakört.

Az Azure AD DS-hitelesítés Azure-fájlmegosztásokhoz való engedélyezéséről az [Azure Active Directory tartományi szolgáltatások hitelesítésének engedélyezése az Azure Files szolgáltatásban](storage-files-identity-auth-active-directory-domain-service-enable.md)című témakörben olvashat.

## <a name="glossary"></a>Szószedet 
Hasznos lehet megérteni néhány kulcsfontosságú kifejezést az Azure AD tartományi szolgáltatás Azure-fájlmegosztások SMB-n keresztüli hitelesítésével kapcsolatban:

-   **Kerberos-hitelesítés**

    A Kerberos egy olyan hitelesítési protokoll, amely a felhasználó vagy a gazdagép identitásának ellenőrzésére szolgál. A Kerberos protokollról a [Kerberos-hitelesítés – áttekintés című témakörben olvashat bővebben.](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)

-  **Kiszolgálói üzenetblokk (SMB) protokoll**

    Az SMB egy iparági szabványnak megfelelő hálózati fájlmegosztási protokoll. Az SMB néven is ismert Common Internet File System vagy CIFS néven. Az SMB protokollról további információt a [Microsoft SMB protocol és a CIFS protokoll áttekintése című témakörben talál.](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)

-   **Azure Active Directory (Azure AD)**

    Az Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. Az Azure AD egyetlen megoldásban egyesíti az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-kezelést és az identitásvédelmet. Az Azure AD-hez csatlakoztatott Windows virtuális gépek (VM-ek) az Azure AD-hitelesítő adatokkal érhetik el az Azure-fájlmegosztásokat. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory tartományi szolgáltatások (Azure AD DS)**

    Az Azure AD DS felügyelt tartományi szolgáltatásokat, például tartományhoz való csatlakozást, csoportházirendeket, LDAP-t és Kerberos/NTLM-hitelesítést biztosít. Ezek a szolgáltatások teljes mértékben kompatibilisek az Active Directory tartományi szolgáltatásokkal. További információ: [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/overview.md).

- **Helyszíni Active Directory tartományi szolgáltatások (AD DS)**

    A helyszíni Active Directory tartományi szolgáltatások (AD DS) integrációaz Azure Files (előzetes verzió) biztosítja a címtáradatok tárolására, miközben elérhetővé teszi a hálózati felhasználók és a rendszergazdák számára. A biztonság integrálva van az Active Directory tartományi szolgáltatásokkal a bejelentkezési hitelesítés és a címtárban lévő objektumok hozzáférés-vezérlése révén. Egyetlen hálózati bejelentkezéssel a rendszergazdák kezelhetik a címtáradatokat és a szervezetet a hálózatukon keresztül, és az engedéllyel rendelkező hálózati felhasználók a hálózat bármely pontján hozzáférhetnek az erőforrásokhoz. Az AD DS-t a helyszíni környezetekben működő vállalatok általában alkalmazzák, és az AD DS hitelesítő adatait használják a hozzáférés-vezérlés identitásaként. További információt az [Active Directory tartományi szolgáltatások – áttekintés című témakörben talál.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

-   **Azure szerepköralapú hozzáférés-vezérlés (RBAC)**

    Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használatával kezelheti az erőforrásokhoz való hozzáférést, ha a felhasználóknak a legkevesebb, a feladatok elvégzéséhez szükséges engedélyeket adja meg. Az RBAC-ról a [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-ban.](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Az Azure Files rendszerű Windows-ak szintű ACL-ek identitásalapú hitelesítése és támogatása a következő használati esetekben a legjobb:

### <a name="replace-on-premises-file-servers"></a>Helyszíni fájlkiszolgálók cseréje

A szétszórt helyszíni fájlkiszolgálók elavulttá válása és cseréje gyakori probléma, amellyel minden vállalat találkozik az informatikai modernizációs út során. A helyszíni AD DS (előzetes verzió) hitelesítéssel rendelkező Azure-fájlmegosztások itt a legjobban illeszkednek, amikor áttelepítheti az adatokat az Azure Files szolgáltatásba. A teljes áttelepítés lehetővé teszi, hogy kihasználja a magas rendelkezésre állás és a méretezhetőség előnyeit, miközben minimalizálja az ügyféloldali változásokat. Zökkenőmentes áttelepítési élményt biztosít a végfelhasználók számára, így továbbra is hozzáférhetnek az adataikhoz ugyanazzal a hitelesítő adatokkal a meglévő tartományhoz csatlakozó gépek használatával.

### <a name="lift-and-shift-applications-to-azure"></a>Alkalmazások felemelése és átcsoportosítása az Azure-ba

Amikor felemeli és áthelyezi az alkalmazásokat a felhőbe, meg szeretné tartani ugyanazt a hitelesítési modellt az adatokhoz. Ahogy kiterjesztjük az identitásalapú hozzáférés-vezérlési élményt az Azure-fájlmegosztásokra, szükségtelenné teszi az alkalmazás modern hitelesítési módszerekre való módosítását és a felhő bevezetésének felgyorsítása. Az Azure-fájlmegosztások lehetővé teszik az Azure AD DS vagy a helyszíni AD DS (előzetes verzió) hitelesítéssel való integrálását. Ha a csomag 100%-os felhőalapú natív, és minimalizálja a felhőalapú infrastruktúrák kezelésére irányuló erőfeszítéseket, az Azure AD DS jobban illeszkedik, mint egy teljes körűen felügyelt tartományi szolgáltatás. Ha teljes kompatibilitásra van szüksége az AD DS-funkciókkal, érdemes lehet az AD DS-környezet felhőre való kiterjesztését a virtuális gépekön lévő tartományvezérlők önkiszolgáló tartományvezérlőivel. Akárhogy is, rugalmasságot biztosítunk az üzleti igényeinek megfelelő tartományi szolgáltatások kiválasztásához.

### <a name="backup-and-disaster-recovery-dr"></a>Biztonsági mentés és vészhelyreállítás (DR)

Ha az elsődleges fájltárolást a helyszínen tartja, az Azure-fájlmegosztások ideális tárolóként szolgálhatnak a biztonsági mentéshez vagy a VÉSZ-mentéshez, az üzletmenet folytonosságának javítása érdekében. Az Azure-fájlmegosztások segítségével biztonsági másolatot kaphat az adatokról a meglévő fájlkiszolgálókról, miközben megőrzi a Windows dakta-kat. A VÉSZ-forgatókönyvek konfigurálhat egy hitelesítési lehetőséget a feladatátvételmegfelelő hozzáférés-vezérlési kényszerítés támogatásához.

## <a name="supported-scenarios"></a>Támogatott esetek

Az alábbi táblázat összefoglalja a támogatott Azure-fájlmegosztások hitelesítési forgatókönyvek az Azure AD DS és a helyszíni AD DS (előzetes verzió). Azt javasoljuk, hogy válassza ki a tartományi szolgáltatás, amely az ügyfélkörnyezetben az Azure Files integráció. Ha az AD DS (előzetes verzió) már be van állítva a helyszínen vagy az Azure-ban, ahol az eszközök tartományhoz csatlakoznak az AD-hez, akkor az AD DS (előzetes verzió) azure-fájlmegosztások hitelesítéséhez használja az AD DS-t(előzetes verzió). Hasonlóképpen, ha már elfogadta az Azure AD DS (GA), akkor használja, hogy az Azure-fájlmegosztások hitelesítéséhez.


|Azure AD DS-hitelesítés  | helyszíni AD DS (előzetes verziójú) hitelesítés  |
|---------|---------|
|Az Azure AD DS-hez csatlakoztatott Windows-gépek sMB-n keresztül id-s adatokon keresztül érhetik el az Azure-fájlmegosztásokat az Azure AD-hitelesítő adatokkal.     |A helyszíni AD DS-hez csatlakozó Windows-gépek az Azure-fájlmegosztásokhoz helyszíni Active Directory-hitelesítő adatokkal férhetnek hozzá, amelyek SMB-n keresztül vannak szinkronizálva az Azure AD-vel.         |

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

- Az Azure AD DS és a helyszíni AD DS-hitelesítés nem támogatja a számítógépfiókok elleni hitelesítést. Érdemes inkább egy szolgáltatás bejelentkezési fiókját használni.
- Az Azure AD DS-hitelesítés nem támogatja az Azure AD-hez csatlakozó eszközök hitelesítését.

## <a name="advantages-of-identity-based-authentication"></a>Az identitásalapú hitelesítés előnyei
Az Azure Files identitásalapú hitelesítése számos előnnyel jár a megosztott kulcsú hitelesítés használatával szemben:

-   **Bővítse ki a hagyományos identitásalapú fájlmegosztási hozzáférési élményt a felhőre a helyszíni AD DS és az Azure AD DS segítségével**  
    Ha azt tervezi, hogy szüntesse meg, és helyezze át az alkalmazást a felhőbe, a hagyományos fájlkiszolgálók at Azure-fájlmegosztások helyett, majd érdemes lehet az alkalmazás hitelesítése akár helyszíni AD DS vagy Az Azure AD DS hitelesítő fájlok eléréséhez. Az Azure Files támogatja a helyszíni AD DS vagy az Azure AD DS hitelesítő adatok használatával az Azure-fájlmegosztások eléréséhez SMB-n keresztül a helyszíni AD DS vagy az Azure AD DS tartományhoz csatlakozott virtuális gépek.

-   **Részletes hozzáférés-vezérlés kényszerítése az Azure-fájlmegosztásokon**  
    A megosztás, a könyvtár vagy a fájl szintjén engedélyeket adhat egy adott identitásnak. Tegyük fel például, hogy több csapat használja egyetlen Azure-fájlmegosztást a projekt együttműködéshez. Minden csapatnak hozzáférést biztosíthat a nem bizalmas könyvtárakhoz, miközben a bizalmas pénzügyi adatokat tartalmazó könyvtárakhoz való hozzáférést csak a pénzügyi csapatra korlátozhatja. 

-   **Biztonsági másolatot a Windows ACL-król (más néven NTFS) az adatokkal együtt**  
    Az Azure-fájlmegosztások segítségével biztonsági másolatot a meglévő helyszíni fájlmegosztások. Az Azure Files megőrzi az ACL-k és az adatok, amikor biztonsági másolatot afájlmegosztást az Azure-fájlmegosztások SMB-n keresztül.

## <a name="how-it-works"></a>Működés

Az Azure fájlmegosztások támogatja a Kerberos-hitelesítést az Azure AD DS vagy a helyszíni AD DS (előzetes verzió) integrálásához. Mielőtt engedélyezheti a hitelesítést az Azure-fájlmegosztásokon, először be kell állítania a tartományi környezetet. Az Azure AD DS-hitelesítés, engedélyeznie kell az Azure AD tartományi szolgáltatások és a tartomány csatlakozzon a virtuális gépek hez, amelyekről a fájladatok eléréséhez. A tartományhoz csatlakozó virtuális gépnek ugyanabban a virtuális hálózatban (VNET) kell lennie, mint az Azure AD DS-nek. Hasonlóképpen a helyszíni AD DS (előzetes verzió) hitelesítéshez be kell állítania a tartományvezérlőt, és a tartománynak csatlakoznia kell a gépekhez vagy a virtuális gépekhez.

Ha egy virtuális gépen futó alkalmazáshoz társított identitás megpróbál hozzáférni az Azure-fájlmegosztásokban lévő adatokhoz, a rendszer elküldi a kérelmet az Azure AD DS-nek az identitás hitelesítéséhez. Ha a hitelesítés sikeres, az Azure AD DS egy Kerberos-jogkivonatot ad vissza. Az alkalmazás küld egy kérelmet, amely tartalmazza a Kerberos-jogkivonatot, és az Azure-fájlmegosztások ezt a jogkivonatot használja a kérelem engedélyezéséhez. Az Azure-fájlmegosztások csak a jogkivonatot kapják, és nem maradnak meg az Azure AD DS hitelesítő adatai. A helyszíni AD DS-hitelesítés hasonló módon működik, ahol az AD DS biztosítja a Kerberos-jogkivonatot.

![Az Azure AD-hitelesítés sMB-n keresztüli diagramját ábrázoló képernyőkép](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Identitásalapú hitelesítés engedélyezése

Engedélyezheti az identitásalapú hitelesítést az Azure AD DS vagy a helyszíni AD DS (előzetes verzió) az Azure-fájlmegosztások az új és a meglévő tárfiókok. Csak egy tartományi szolgáltatás használható fájl-hozzáférési hitelesítéshez a tárfiókban, amely a fiók összes fájlmegosztására vonatkozik. Részletes útmutatást a fájlmegosztások Azure AD DS-hitelesítéshez való beállításához az [Azure Active Directory tartományi szolgáltatások hitelesítésének engedélyezése az Azure Files-on](storage-files-identity-auth-active-directory-domain-service-enable.md) és útmutatást a helyszíni AD DS -hez (előzetes verzió) című másik cikkünkben, az [On-premises Active Directory tartományi szolgáltatások hitelesítésének engedélyezése SMB-n keresztül az Azure-fájlmegosztások számára](storage-files-identity-auth-active-directory-enable.md)című cikkünkben.

### <a name="configure-share-level-permissions-for-azure-files"></a>Megosztásszintű engedélyek konfigurálása az Azure Files alkalmazáshoz

Ha az Azure AD DS vagy a helyszíni AD DS (előzetes verzió) hitelesítés engedélyezve van, használhat beépített RBAC-szerepköröket, vagy egyéni szerepköröket konfigurálhat az Azure AD-identitásokhoz, és hozzáférési jogokat rendelhet a tárfiókok bármely fájlmegosztásához. A hozzárendelt engedély lehetővé teszi, hogy a megadott identitás csak a megosztáshoz férjen hozzá, semmi más, még a gyökérkönyvtár sem. Továbbra is külön kell konfigurálnia az Azure-fájlmegosztások könyvtár- vagy fájlszintű engedélyeit.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Könyvtár- vagy fájlszintű engedélyek konfigurálása az Azure Files alkalmazáshoz

Az Azure fájlmegosztások szabványos Windows-fájlengedélyeket kényszerítenek ki mind a könyvtár, mind a fájl szintjén, beleértve a gyökérkönyvtárat is. A címtár- vagy fájlszintű engedélyek konfigurálása az SMB és a REST számára is támogatott. Csatlakoztassa a célfájlmegosztást a virtuális gépről, és konfigurálja az engedélyeket a Windows Fájlkezelő, a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)vagy a [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) paranccsal.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>A tárfiók kulcsának használata a rendszergazdai engedélyekhez

A tárfiók kulccsal rendelkező felhasználó rendszergazdai engedélyekkel férhet hozzá az Azure-fájlmegosztásokhoz. A rendszergazdai engedélyek megkerülik az összes hozzáférés-vezérlési korlátozást.

> [!IMPORTANT]
> Ajánlott biztonsági ajánlott eljárásunk, hogy kerüljük a tárfiók kulcsainak megosztását, és lehetőség szerint használjuk ki az identitásalapú hitelesítést.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Könyvtár- és fájlacl-ok megőrzése az Azure-fájlmegosztásokba történő adatok importálásakor

Az Azure Files támogatja a könyvtár- vagy fájlszintű ACL-ek megőrzését az Azure-fájlmegosztásokba történő másoláskor. Az Azure File Sync vagy a common file movement toolsets használatával átmásolhatja az ACL-eket egy címtárba vagy fájlmegosztásba az Azure File Sync vagy a common file movement toolsets használatával. Például [használhatja a robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) a `/copy:s` jelzővel adatok másolása, valamint az AC-k egy Azure-fájlmegosztásra. Az ACL-ok alapértelmezés szerint megőrződnek, az ACL-ok megőrzése érdekében nem kell engedélyeznie az identitásalapú hitelesítést a tárfiókban.

## <a name="pricing"></a>Díjszabás
Nincs további szolgáltatási díj az identitásalapú hitelesítés engedélyezéséhez sMB-n keresztül a tárfiókon. A díjszabásról az [Azure Files díjszabása](https://azure.microsoft.com/pricing/details/storage/files/) és az [Azure AD tartományi szolgáltatások díjszabása című](https://azure.microsoft.com/pricing/details/active-directory-ds/)témakörben talál további információt.

## <a name="next-steps"></a>További lépések
Az Azure Files szolgáltatásról és az SMB-n keresztüli identitásalapú hitelesítésről az alábbi forrásokban talál további információt:

- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Helyszíni Active Directory tartományi szolgáltatások hitelesítésének engedélyezése SMB-n keresztül az Azure-fájlmegosztások számára](storage-files-identity-auth-active-directory-enable.md)
- [Az Azure Active Directory tartományi szolgáltatások hitelesítésének engedélyezése az Azure-fájlokban](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [GYIK](storage-files-faq.md)
