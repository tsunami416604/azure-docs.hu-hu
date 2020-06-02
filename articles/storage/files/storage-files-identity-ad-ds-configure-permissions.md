---
title: A felhasználó által a fájl szintjén elvégezhető műveletek szabályozása – Azure-fájlmegosztás
description: Ismerje meg, hogyan konfigurálhatja a Windows ACL-engedélyeket helyszíni AD DS hitelesítésre az Azure-fájlmegosztás számára. Lehetővé teszi, hogy kihasználhassa a részletes hozzáférés-vezérlés előnyeit.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 6e49201b0574e0a1235cc9e2cb313b40b0563f93
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268491"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Harmadik rész: a könyvtár-és a fájl szintű engedélyek konfigurálása SMB protokollon keresztül 

A cikk elkezdése előtt győződjön meg arról, hogy végrehajtotta az előző cikket, [rendeljen hozzá megosztási szintű engedélyeket egy identitáshoz](storage-files-identity-ad-ds-assign-permissions.md). Győződjön meg arról, hogy a megosztási szintű engedélyek vannak érvényben.

Miután megosztási szintű engedélyeket rendelt a RBAC-hez, hozzá kell rendelnie a megfelelő Windows ACL-eket (más néven NTFS-engedélyeket) a gyökér, a könyvtár vagy a fájl szintjén, hogy kihasználhassa a részletes hozzáférés-vezérlés előnyeit. Gondoljon arra, hogy a RBAC engedélyek magas szintű forgalomirányító, amely meghatározza, hogy a felhasználó hozzáférhet-e a megosztáshoz. Míg a Windows ACL-ek részletesebben határozzák meg, hogy a felhasználó milyen műveleteket végezhet el a címtár vagy a fájl szintjén.

Miután megosztási szintű engedélyeket rendelt a RBAC-hez, a megfelelő Windows ACL-eket kell konfigurálnia a gyökér, a könyvtár vagy a fájl szintjén, hogy kihasználhassa a részletes hozzáférés-vezérlés előnyeit. Gondoljon arra, hogy a RBAC engedélyek magas szintű forgalomirányító, amely meghatározza, hogy a felhasználó hozzáférhet-e a megosztáshoz. Míg a Windows ACL-ek részletesebben működnek, hogy megtudja, milyen műveleteket végezhet a felhasználó a címtár vagy a fájl szintjén. A megosztási szint és a fájl/könyvtár szintű engedélyek akkor is érvénybe lépnek, ha egy felhasználó egy fájlhoz vagy könyvtárhoz próbál hozzáférni, tehát ha a kettő között különbség van, akkor csak a legszigorúbb korlátozás lesz érvényben. Ha például egy felhasználó rendelkezik írási/olvasási hozzáféréssel a fájl szintjén, de csak egy megosztási szinten olvas, akkor csak a fájlt tudja olvasni. Ugyanez igaz lehet, ha fordították, és a felhasználó olvasási/írási hozzáféréssel rendelkezik a megosztás szintjén, de csak a fájl szintjén olvashatja el.

## <a name="supported-permissions"></a>Támogatott engedélyek

Azure Files támogatja az alapszintű és a speciális Windows ACL-ek teljes készletét. Az Azure-fájlmegosztás könyvtárain és fájljain a Windows ACL-eket megtekintheti és konfigurálhatja úgy, hogy összekapcsolja a megosztást, majd a Windows fájlkezelővel, a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) parancs futtatásával vagy a [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) parancs használatával. 

Az ACL-ek rendszergazdai jogosultságokkal való konfigurálásához a megosztást a tartományhoz csatlakoztatott virtuális gépen lévő Storage-fiók kulcsa használatával kell csatlakoztatnia. Kövesse a következő szakaszban található utasításokat egy Azure-fájlmegosztás parancssorból történő csatlakoztatásához és a Windows ACL-ek konfigurálásához.

A fájlmegosztás gyökérkönyvtárában a következő engedélyek szerepelnek:

- Builtin\rendszergazda: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT AUTHORITY\Authenticated-felhasználók: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- LÉTREHOZÓ TULAJDONOS: (OI) (CI) (IO) (F)

## <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

`net use`Csatlakoztassa az Azure-fájlmegosztást a Windows parancs használatával. Ne felejtse el lecserélni az alábbi példában szereplő helyőrző értékeket a saját értékeire. A fájlmegosztás csatlakoztatásával kapcsolatos további információkért lásd: [Azure-fájlmegosztás használata a Windowsban](storage-how-to-use-files-windows.md). 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```

Ha a Azure Fileshoz való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [a hibaelhárítási eszközt, amelyet Azure Files csatlakoztatási hibákhoz tettünk közzé a Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)rendszeren. Az 445-as port blokkolása esetén [útmutatást](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) is biztosítunk a forgatókönyvek megoldásához. 

## <a name="configure-windows-acls"></a>Windows ACL-ek konfigurálása

Miután a fájlmegosztás csatlakoztatva lett a Storage-fiók kulcsához, konfigurálnia kell a Windows ACL-eket (más néven NTFS-engedélyeket). A Windows ACL-eket Windows fájlkezelővel vagy icacls-vel is konfigurálhatja.

Ha a helyi fájlkiszolgálón lévő címtárakkal vagy fájlokkal rendelkezik a AD DS identitásokkal konfigurált Windows DACL-ekkel, akkor átAzure Files másolhatja az ACL-eket a hagyományos fájlmásolás-eszközökkel, például a Robocopy vagy az [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases)használatával. Ha a címtárakat és a fájlokat a rendszer a Azure File Syncon Azure Fileson, akkor az ACL-eket a rendszer a natív formában tartja át és őrzi meg.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows-ACL-ek konfigurálása a Windows fájlkezelővel

A Windows fájlkezelővel teljes hozzáférést biztosíthat a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is.

1. Nyissa meg a Windows fájlkezelő alkalmazást, és kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok**lehetőséget.
1. Válassza a **Biztonság** fület.
1. Válassza a **Szerkesztés lehetőséget.** az engedélyek módosításához.
1. Módosíthatja a meglévő felhasználók engedélyeit, vagy kiválaszthatja a **Hozzáadás...** lehetőséget az új felhasználók engedélyeinek megadásához.
1. Az új felhasználók hozzáadására szolgáló kérdés ablakban adja meg azt a cél felhasználónevet, amely számára engedélyezni kívánja az engedélyek megadását az **adja meg a kijelölendő objektumok nevét** mezőbe, **majd válassza a Névellenőrzés lehetőséget** a MEGcélzott felhasználó teljes UPN-nevének megkereséséhez.
1.    Kattintson az **OK** gombra.
1.    A **Biztonság** lapon válassza ki az összes olyan engedélyt, amely számára engedélyezni szeretné az új felhasználót.
1.    Kattintson az **Alkalmaz** gombra.

### <a name="configure-windows-acls-with-icacls"></a>Windows ACL-ek konfigurálása icacls-val

A következő Windows-paranccsal teljes körű engedélyeket adhat meg a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

A Windows ACL-ek és a különböző típusú támogatott engedélyek beállításával kapcsolatos további információkért tekintse meg [az icacls parancssori útmutatója](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)című témakört.

## <a name="next-steps"></a>További lépések

Most, hogy a funkció engedélyezve és konfigurálva van, folytassa a következő cikkel, amelyben csatlakoztathatja az Azure-fájlmegosztást egy tartományhoz csatlakoztatott virtuális gépről.

[Negyedik rész: fájlmegosztási csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről](storage-files-identity-ad-ds-mount-file-share.md)