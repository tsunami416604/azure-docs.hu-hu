---
title: Azure-fájlmegosztás csatlakoztatása AD DS csatlakoztatott virtuális géphez
description: Megtudhatja, hogyan csatlakoztathat fájlmegosztást a helyszíni Active Directory tartományi szolgáltatások csatlakoztatott gépekhez.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9807563c768b82c823ff754aaa679ddc917bf62d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535059"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Negyedik rész: fájlmegosztási csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről

A cikk elkezdése előtt győződjön meg arról, hogy elvégezte az előző cikket, [konfigurálja a címtár-és a fájl szintű engedélyeket az SMB protokollon keresztül](storage-files-identity-ad-ds-configure-permissions.md).

A cikkben ismertetett folyamat ellenőrzi, hogy a fájlmegosztás és a hozzáférési engedélyek megfelelően vannak-e beállítva, és hogy elérhető-e egy Azure-fájlmegosztás egy tartományhoz csatlakoztatott virtuális gépről. A megosztási szintű Azure szerepkör-hozzárendelés eltarthat egy ideig. 

Jelentkezzen be az ügyfélre az engedélyeket kapott hitelesítő adatokkal, ahogy az az alábbi képen is látható.

![Az Azure AD bejelentkezési képernyőjét bemutató képernyőkép a felhasználói hitelesítéshez](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Csatlakoztatási előfeltételek

A fájlmegosztás csatlakoztatása előtt győződjön meg arról, hogy a következő előfeltételek vannak:

- Ha olyan ügyfélről csatlakoztatja a fájlmegosztást, amely előzőleg csatlakoztatta a fájlmegosztást a Storage-fiók kulcsa alapján, akkor győződjön meg arról, hogy leválasztotta a megosztást, eltávolította a Storage-fiók kulcsának állandó hitelesítő adatait, és jelenleg AD DS hitelesítő adatokat használ a hitelesítéshez.
- Az ügyfélnek a AD DSnak kell lennie. Ha a számítógép vagy a virtuális gép kívül esik a AD DS által felügyelt hálózaton, engedélyeznie kell a VPN-t a hitelesítéshez AD DS eléréséhez.

Cserélje le a helyőrző értékeket a saját értékeire, majd használja az alábbi parancsot az Azure-fájlmegosztás csatlakoztatásához:

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Ha AD DS hitelesítő adatokkal való csatlakoztatással kapcsolatos problémákba ütközik, tekintse meg a [nem sikerült csatlakoztatni Azure Files az ad hitelesítő adatokkal](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) című témakör útmutatását.

Ha a fájlmegosztás csatlakoztatása sikeres volt, sikeresen engedélyezte és konfigurálta a helyszíni AD DS hitelesítést az Azure-fájlmegosztás számára.

## <a name="next-steps"></a>További lépések

Ha a AD DSban létrehozott identitás olyan tartományban vagy szervezeti egységben található, amely a jelszó-elforgatást kényszeríti, folytassa a következő cikkel a jelszó frissítésével kapcsolatos utasításokért:

[A Storage-fiók identitásának jelszavának frissítése AD DS](storage-files-identity-ad-ds-update-password.md)
