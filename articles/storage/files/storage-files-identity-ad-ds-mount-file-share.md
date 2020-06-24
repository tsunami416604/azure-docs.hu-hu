---
title: Azure-fájlmegosztás csatlakoztatása AD DS csatlakoztatott virtuális géphez
description: Megtudhatja, hogyan csatlakoztathat fájlmegosztást a helyszíni Active Directory tartományi szolgáltatások csatlakoztatott gépekhez.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: b061b86226df38c402de3d3d65385ebf583f024b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214417"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Negyedik rész: fájlmegosztási csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről

A cikk elkezdése előtt győződjön meg arról, hogy elvégezte az előző cikket, [konfigurálja a címtár-és a fájl szintű engedélyeket az SMB protokollon keresztül](storage-files-identity-ad-ds-configure-permissions.md).

A cikkben ismertetett folyamat ellenőrzi, hogy a fájlmegosztás és a hozzáférési engedélyek megfelelően vannak-e beállítva, és hogy elérhető-e egy Azure-fájlmegosztás egy tartományhoz csatlakoztatott virtuális gépről. A megosztási szintű RBAC szerepkör-hozzárendelés hosszabb időt is igénybe vehet. 

Jelentkezzen be az ügyfélre az engedélyeket kapott hitelesítő adatokkal, ahogy az az alábbi képen is látható.

![Az Azure AD bejelentkezési képernyőjét bemutató képernyőkép a felhasználói hitelesítéshez](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Csatlakoztatási előfeltételek

A fájlmegosztás csatlakoztatása előtt győződjön meg arról, hogy a következő előfeltételek vannak:

- Ha olyan ügyfélről csatlakoztatja a fájlmegosztást, amely előzőleg csatlakoztatta a fájlmegosztást a Storage-fiók kulcsa alapján, akkor győződjön meg arról, hogy leválasztotta a megosztást, eltávolította a Storage-fiók kulcsának állandó hitelesítő adatait, és jelenleg AD DS hitelesítő adatokat használ a hitelesítéshez.
- Az ügyfélnek a AD DSnak kell lennie. Ha a számítógép vagy a virtuális gép kívül esik a AD DS által felügyelt hálózaton, engedélyeznie kell a VPN-t a hitelesítéshez AD DS eléréséhez.

Cserélje le a helyőrző értékeket a saját értékeire, majd használja az alábbi parancsot az Azure-fájlmegosztás csatlakoztatásához:

```cli
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Ha AD DS hitelesítő adatokkal való csatlakoztatással kapcsolatos problémákba ütközik, tekintse meg a [nem sikerült csatlakoztatni Azure Files az ad hitelesítő adatokkal](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) című témakör útmutatását.

Ha a fájlmegosztás csatlakoztatása sikeres volt, sikeresen engedélyezte és konfigurálta a helyszíni AD DS hitelesítést az Azure-fájlmegosztás számára.

## <a name="next-steps"></a>További lépések

Ha a AD DSban létrehozott identitás olyan tartományban vagy szervezeti egységben található, amely a jelszó-elforgatást kényszeríti, folytassa a következő cikkel a jelszó frissítésével kapcsolatos utasításokért:

[A Storage-fiók identitásának jelszavának frissítése AD DS](storage-files-identity-ad-ds-update-password.md)