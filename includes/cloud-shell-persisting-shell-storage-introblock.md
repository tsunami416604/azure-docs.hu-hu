---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58051810"
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

A speciális lehetőség használatával társíthatja a meglévő erőforrások. A Cloud Shell-régió kiválasztásakor jelöljön ki egy biztonsági tárfiók ugyanabban a régióban közös elhelyezésű. Például ha a hozzárendelt régiója az USA nyugati Régiójában, mint társítania kell a fájlmegosztást, amelyet az USA nyugati Régiójában, valamint található is.

A storage-telepítő használatával megjelenésekor válassza **speciális beállítások megjelenítése** további beállítások megtekintéséhez. A feltöltött tárolási lehetőségek szűrő helyileg redundáns tárolás (LRS), a georedundáns tárolás (GRS) és a zónaredundáns tárfiókok (ZRS). 

> [!NOTE]
> A GRS vagy ZRS tárfiókok használata akkor javasolt további rugalmasság a biztonsági fájlmegosztás. Milyen típusú redundancia a céljait és a díj szabályozó függ. [További információ az Azure-tárfiókok replikációjának lehetőségei](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Az erőforrás-csoport beállításai](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

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
