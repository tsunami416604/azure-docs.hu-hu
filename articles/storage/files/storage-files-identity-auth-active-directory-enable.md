---
title: Áttekintés – helyszíni AD DS hitelesítés az Azure-fájlmegosztás számára
description: Ismerje meg az Azure-fájlmegosztás Active Directory tartományi szolgáltatások (AD DS) hitelesítését. Ez a cikk a támogatási forgatókönyveket, a rendelkezésre állást, valamint azt ismerteti, hogy az engedélyek hogyan működnek a AD DS és az Azure Active Directory között.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: f505f8222bd5959a9bb0213dc5c8c48092723bba
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266627"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Áttekintés – helyszíni Active Directory tartományi szolgáltatások hitelesítés SMB-en keresztül az Azure-fájlmegosztás esetében

[Azure Files](storage-files-introduction.md)   támogatja a kiszolgálói üzenetblokk (SMB) szolgáltatáson keresztüli identitás-alapú hitelesítést a következő két típusú tartományi szolgáltatáson keresztül: Azure Active Directory Domain Services (Azure AD DS) és helyszíni Active Directory tartományi szolgáltatások (AD DS). Az identitás-alapú hitelesítési módszer használatával integrálhatja az Azure-fájlmegosztás meglévő, identitás-alapú hitelesítési folyamatával való elérésének folyamatát ahelyett, hogy külön kellene kezelnie. Ez a cikksorozat a helyszíni AD DS engedélyezésére és konfigurálására koncentrál az Azure-fájlmegosztás segítségével történő hitelesítéshez.

Ha még nem ismeri az Azure-fájlmegosztás újdonságait, javasoljuk, hogy olvassa el a [tervezési útmutatót](storage-files-planning.md) , mielőtt elolvassa a következő cikksorozatot.

## <a name="supported-scenarios-and-restrictions"></a>Támogatott forgatókönyvek és korlátozások

- AD DS Azure Files helyszíni AD DS hitelesítéshez használt identitásokat szinkronizálni kell az Azure AD-vel. A jelszó-kivonat szinkronizálása nem kötelező. 
- A Azure File Sync által felügyelt Azure-fájlmegosztás használatát támogatja.
- Támogatja a Kerberos-hitelesítést az AD-vel az RC4-HMAC titkosítással. Az AES Kerberos-titkosítás még nem támogatott.
- Támogatja az egyszeri bejelentkezési élményt.
- Csak a Windows 7 vagy Windows Server 2008 R2 rendszernél újabb verziójú operációs rendszert futtató ügyfelek esetében támogatott.
- Csak a Storage-fiók által regisztrált AD-erdőben támogatott. Alapértelmezés szerint csak egyetlen erdő AD DS hitelesítő adataival érheti el az Azure-fájlmegosztást. Ha egy másik erdőből kell hozzáférnie az Azure-fájlmegosztás számára, győződjön meg arról, hogy a megfelelő erdőszintű megbízhatósági kapcsolat van konfigurálva. a részletekért tekintse meg a [gyakori kérdéseket](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) .
- A nem támogatja a AD DS-ben létrehozott számítógépfiókok hitelesítését. 

Ha engedélyezi a AD DS az Azure-fájlmegosztás SMB protokollon keresztül történő engedélyezését, a AD DS csatlakoztatott gépek az Azure-fájlmegosztást meglévő AD DS hitelesítő adataival csatlakoztathatók. Ez a funkció AD DS környezettel engedélyezhető, amely a helyszíni gépeken vagy az Azure-ban üzemeltethető.

> [!NOTE]
> A Azure Files AD-hitelesítés néhány gyakori felhasználási esethez való beállításának elősegítése érdekében két videót tettünk közzé a következő forgatókönyvek lépésenkénti útmutatójában:
> - [Helyszíni fájlkiszolgálók cseréje Azure Files (beleértve a fájlokhoz és az AD-hitelesítéshez tartozó magánhálózati kapcsolaton keresztül történő telepítést)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [A Azure Files használata a Windows rendszerű virtuális asztali profil tárolójának használatával (beleértve az AD-hitelesítés és a FsLogix-konfiguráció beállítását is)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Előfeltételek 

Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése előtt győződjön meg arról, hogy végrehajtotta a következő előfeltételeket: 

- Válassza ki vagy hozza létre [AD DS-környezetét](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) , és [szinkronizálja azt az Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) -be Azure ad Connect használatával. 

    A szolgáltatást egy új vagy meglévő helyszíni AD DS környezetben is engedélyezheti. A hozzáféréshez használt identitásokat szinkronizálni kell az Azure AD-vel. Az Azure AD-bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani.

- Tartomány – helyszíni vagy Azure-beli virtuális gép csatlakoztatása helyszíni AD DShoz. További információ a tartományhoz való csatlakozásról: [számítógép csatlakoztatása tartományhoz](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

- Válasszon ki vagy hozzon létre egy Azure Storage-fiókot.  Az optimális teljesítmény érdekében javasoljuk, hogy telepítse a Storage-fiókot ugyanabban a régióban, mint az ügyfél, amelyről a megosztást szeretné elérni. Ezután [csatlakoztassa az Azure-fájlmegosztást](storage-how-to-use-files-windows.md) a Storage-fiók kulcsával. A Storage-fiók kulcsának csatlakoztatása ellenőrzi a kapcsolódást.

    Győződjön meg arról, hogy a fájlmegosztást tartalmazó Storage-fiók még nincs konfigurálva az Azure AD DS-hitelesítéshez. Ha Azure Files Azure AD DS hitelesítés engedélyezve van a Storage-fiókon, akkor azt a helyszíni AD DS használata előtt le kell tiltani. Ez azt jelenti, hogy az Azure AD DS-környezetben konfigurált meglévő ACL-eket újra kell konfigurálni a megfelelő engedélyek kényszerítéséhez.

    Ha a Azure Fileshoz való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [a hibaelhárítási eszközt, amelyet Azure Files csatlakoztatási hibákhoz tettünk közzé a Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)rendszeren. Az 445-as port blokkolása esetén [útmutatást](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) is biztosítunk a forgatókönyvek megoldásához. 

- Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése és konfigurálása előtt végezze el a megfelelő hálózati konfigurációt. További információért tekintse meg [Azure Files hálózatkezelési megfontolásokat](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az AD DS-hitelesítés Azure Files az [összes nyilvános régióban és az Azure gov-régiókban](https://azure.microsoft.com/global-infrastructure/locations/)érhető el.

## <a name="overview"></a>Áttekintés

Ha azt tervezi, hogy engedélyezi a fájlmegosztás hálózati konfigurációját, javasoljuk, hogy olvassa el a [hálózatkezelési megfontolásokat](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) ismertető cikket, és a AD DS hitelesítés engedélyezése előtt fejezze be a kapcsolódó konfigurációt.

Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése lehetővé teszi az Azure-fájlmegosztás hitelesítését a helyszíni AD DS hitelesítő adataival. Továbbá lehetővé teszi az engedélyek jobb kezelését a részletes hozzáférés-vezérlés engedélyezéséhez. Ehhez szükség van a helyszíni AD DS és az Azure AD közötti, AD-kapcsolaton keresztüli identitások szinkronizálására. A megosztási szint hozzáférését az Azure AD-be szinkronizált identitásokkal szabályozhatja, miközben a fájl-és megosztási szinthez való hozzáférést a helyszíni AD DS hitelesítő adataival kezelheti.

Ezután kövesse az alábbi lépéseket a AD DS-hitelesítés Azure Files beállításához: 

1. [Első rész: AD DS hitelesítés engedélyezése a Storage-fiókban](storage-files-identity-ad-ds-enable.md)

1. [Második rész: a megosztás hozzáférési engedélyeinek kiosztása az Azure AD-identitáshoz (felhasználó, csoport vagy szolgáltatásnév), amely szinkronban van a cél AD-identitással](storage-files-identity-ad-ds-assign-permissions.md)

1. [Harmadik rész: a Windows ACL-ek konfigurálása a címtárakhoz és a fájlokhoz](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Negyedik rész: Azure-fájlmegosztás csatlakoztatása a AD DShoz csatlakoztatott virtuális géphez](storage-files-identity-ad-ds-mount-file-share.md)

1. [A Storage-fiók identitásának jelszavának frissítése AD DS](storage-files-identity-ad-ds-update-password.md)

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD-hitelesítés használatát az SMB-en keresztül az Azure-fájlmegosztás számára. 

![Fájlok AD munkafolyamat-diagramja](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Az Azure-fájlmegosztás eléréséhez használt identitásokat szinkronizálni kell az Azure AD-vel, hogy a [szerepköralapú hozzáférés-vezérlési (RBAC)](../../role-based-access-control/overview.md) modell használatával kényszerítse a megosztási szintű fájlok engedélyeit. A meglévő fájlkiszolgálók által átvitt fájlok/könyvtárak [Windows-stílusú DACL-listái](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) megmaradnak és érvényben lesznek. Ez zökkenőmentes integrációt biztosít a vállalati AD DS környezettel. Amikor az Azure-fájlmegosztás használatával cseréli le a helyszíni fájlkiszolgálók szolgáltatást, a meglévő felhasználók az Azure-fájlmegosztást az aktuális ügyfelektől egyszeri bejelentkezéssel érhetik el, a használatban lévő hitelesítő adatok módosítása nélkül.  

## <a name="next-steps"></a>További lépések

Ha engedélyezni szeretné a helyszíni AD DS hitelesítést az Azure-fájlmegosztás számára, folytassa a következő cikkel:

[Első rész: AD DS hitelesítés engedélyezése a fiókhoz](storage-files-identity-ad-ds-enable.md)