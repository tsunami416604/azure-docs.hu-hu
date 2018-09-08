---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: 6055b70c7df2704a334b7f14c9365863ddafbd5a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164546"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Az Azure Cloud Shellben fájlok megtartása
A cloud Shell az Azure File storage segítségével a fájlok munkamenetek közötti megtartása használja. Az első indítás a Cloud Shell kéri, hogy társíthatja egy új vagy meglévő fájlmegosztást a fájlok munkamenetek közötti megtartása.

> [!NOTE]
> A bash és a PowerShell ossza meg ugyanazt a fájlmegosztást. Csak egy fájlmegosztás társítható automatikus csatlakoztatását a Cloud shellben.

## <a name="create-new-storage"></a>Új tároló létrehozása

Ha alapvető beállításokkal, és válasszon ki egy előfizetést csak, a Cloud Shell három erőforrást hoz létre az Ön nevében a legközelebbi támogatott régióban:
* Erőforráscsoport: `cloud-shell-storage-<region>`
* Tárfiók: `cs<uniqueGuid>`
* Fájlmegosztás: `cs-<user>-<domain>-com-<uniqueGuid>`

![Az előfizetés beállítása](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

A fájlmegosztás csatlakoztatja `clouddrive` a a `$Home` könyvtár. Ez egy egyszeri művelet, és minden későbbi munkamenet során az automatikusan csatlakoztatja a fájlmegosztást. 

> [!NOTE]
> A biztonság érdekében minden felhasználó a saját tárfiók kell kiépítenie.  A szerepköralapú hozzáférés-vezérlés (RBAC) a felhasználók kell közreműködői hozzáférés vagy újabb, a storage-fiók szint.

A fájlmegosztást is tartalmaz egy 5 GB-os rendszerképet, amely automatikusan jön létre, amely továbbra is fennáll az adatok a `$Home` könyvtár. Ez vonatkozik a Bash és a PowerShell.

## <a name="use-existing-resources"></a>Meglévő erőforrások használata

A speciális lehetőség használatával társíthatja a meglévő erőforrások. A storage-telepítő használatával megjelenésekor válassza **speciális beállítások megjelenítése** további beállítások megtekintéséhez. A feltöltött tárolási lehetőségek szűrő helyileg redundáns tárolás (LRS), a georedundáns tárolás (GRS) és a zónaredundáns tárfiókok (ZRS). Nyissa meg [Itt további](https://docs.microsoft.com/azure/storage/common/storage-redundancy#choosing-a-replication-option) kapcsolatos Azure-tárfiókok replikációjának lehetőségei.

![Az erőforrás-csoport beállításai](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

A Cloud Shell-régió kiválasztásakor ki kell választania az adott régióban, valamint a biztonsági storage-fiók csatlakoztatásához.

### <a name="supported-storage-regions"></a>Támogatott storage-régiók
Kapcsolódó Azure storage-fiókok és a Cloud Shell-gép, hogy van-e csatlakoztatni őket ugyanabban a régióban kell lennie. Keresse meg a jelenlegi régióban is futtathatja a `env` a Bash, és keresse meg a változó `ACC_LOCATION`. Fájlmegosztások kap egy 5 GB-os rendszerképet, megőrizheti a létrehozott a `$Home` könyvtár.

Cloud Shell gépek létezik a következő régióban:
|Terület|Régió|
|---|---|
|Amerika|USA keleti RÉGIÓJA, USA déli középső RÉGIÓJA, USA nyugati RÉGIÓJA|
|Európa|Észak-Európa, Nyugat-Európa|
|Ázsia és a Csendes-óceáni térség|India középső, Délkelet-Ázsia|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Erőforrás-létrehozás az Azure-erőforrás szabályzatával korlátozása
Storage-fiókokat, akkor a Cloud Shellben hozzon létre megfelelő címkékkel `ms-resource-usage:azure-cloud-shell`. Ha azt szeretné, letilthatja a Cloud shellben hozzon létre a storage-fiókok, hozzon létre egy [Azure erőforrás-szabályzat a címkék](../articles/azure-policy/json-samples.md) , amely az adott címkével által aktivált.
