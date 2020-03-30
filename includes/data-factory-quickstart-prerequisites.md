---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4d77cb8128105a40143a40e48ebe450115f7cf1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164167"
---
## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

### <a name="azure-roles"></a>Azure-szerepkörök
Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. Az előfizetésben található engedélyek megtekintéséhez nyissa meg az [Azure Portalt,](https://portal.azure.com)válassza ki a felhasználónevét a jobb felső sarokban, válassza a "**...**" ikont a további beállításokért, majd válassza a **Saját engedélyek**lehetőséget. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést.

A Data Factory gyermekerőforrásai – beleértve az adatkészletek, társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – létrehozására és kezelésére az alábbi követelmények vonatkoznak:

- A gyermekerőforrások Microsoft Azure Portalon való létrehozásához és kezeléséhez a **Data Factory közreműködője** szerepkörhöz kell tartoznia az erőforráscsoport szintjén vagy felette.
- A gyermekerőforrások PowerShell-lel vagy az SDK-val való létrehozásához és kezeléséhez a **közreműködő** szerepkör is elegendő az erőforráscsoport szintjén vagy felette.

Ha szeretne példautasításokat látni arra, hogyan kell egy felhasználót a szerepkörhöz adni, olvassa el a [Szerepkörök hozzáadása](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) című cikket.

További információkért tekintse meg a következő cikkeket:

- [Data Factory közreműködője szerepkör](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Szerepkörök és engedélyek az Azure Data Factoryhoz](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben a rövid útmutatóban egy általános célú Azure Storage-fiókot (különösen a Blob storage-ot) használ *forrás-* és céladattárolóként. *destination* Ha nem rendelkezik általános célú Azure Storage-fiókkal, olvassa [el a Tárfiók létrehozása](../articles/storage/common/storage-account-create.md) létrehozása című témakört. 

#### <a name="get-the-storage-account-name"></a>A tárfiók nevének beszereznie
Ehhez a rövid útmutatóhoz szüksége lesz az Azure Storage-fiók nevére. Az alábbi eljárás a tárfiók nevének bekésezésére szolgáló lépéseket tartalmazza: 

1. Egy webböngészőben nyissa meg az [Azure Portalon,](https://portal.azure.com) és jelentkezzen be az Azure-felhasználónév vel és jelszóval.
2. Az Azure Portal menüben válassza a **Minden szolgáltatás**lehetőséget, majd válassza **a Storage** > **Storage-fiókok lehetőséget.** A *Storage-fiókokat* bármelyik oldalról is megkeresheti és kiválaszthatja.
3. A **Storage-fiókok** lapon szűrje a tárfiókot (ha szükséges), majd válassza ki a tárfiókot. 

A *Storage-fiókokat* bármelyik oldalról is megkeresheti és kiválaszthatja.

#### <a name="create-a-blob-container"></a>Blobtároló létrehozása
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban.

1. A tárfiók lapon válassza az **Áttekintő** > **tárolók lehetőséget.**
2. A * \<Fióknév>*  -  **Tárolók** lap eszköztárán válassza a **Tároló**lehetőséget.
3. Az **Új tároló** párbeszédablakban adja meg az **adftutorial** nevet, és kattintson az **OK** gombra. A * \<fiók név>*  -  **tárolók** lap frissül, hogy tartalmazza **adftutorial** a tárolók listájában.

   ![Konténerek listája](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Bemeneti mappa és fájl hozzáadása a blobtárolóhoz
Ebben a szakaszban egy **bemenetnevű** mappát hoz létre az imént létrehozott tárolóban, majd feltölt egy mintafájlt a bemeneti mappába. Mielőtt elkezdené, nyisson meg egy szövegszerkesztőt, például **a Jegyzettömböt,** és hozzon létre egy **emp.txt** nevű fájlt a következő tartalommal:

```emp.txt
John, Doe
Jane, Doe
```

Mentse a fájlt a **C:\ADFv2QuickStartPSH** mappába. (Ha a mappa még nem létezik, hozza létre.) Ezután térjen vissza az Azure Portalra, és kövesse az alábbi lépéseket:

1. A * \<Fiók név>*  -  **tárolók** lapon, ahol abbahagyta, válassza a **adftutorial** a frissített tárolók listájából.

   1. Ha bezárta az ablakot, vagy egy másik oldalra ment, jelentkezzen be újra az [Azure Portalra.](https://portal.azure.com)
   1. Az Azure Portal menüben válassza a **Minden szolgáltatás**lehetőséget, majd válassza **a Storage** > **Storage-fiókok lehetőséget.** A *Storage-fiókokat* bármelyik oldalról is megkeresheti és kiválaszthatja.
   1. Válassza ki a tárfiókot, majd válassza **a Tárolók** > **adftutorial**lehetőséget.

2. Az **adftutorial** tárolólap eszköztárán válassza a **Feltöltés**lehetőséget.
3. A **Blob feltöltése** lapon jelölje be a **Fájlok jelölőnégyzetet,** majd keresse meg és jelölje ki az **emp.txt** fájlt.
4. Bontsa ki a **Speciális** címsort. Az oldal mostantól a következőképpen jelenik meg:

   ![Kattintás a Speciális hivatkozásra](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. A **Feltöltés mappába** mezőbe írja be a **bevitelt.**
6. Kattintson a **Feltöltés** gombra. A listában meg kell jelennie az **emp.txt** fájlnak és a feltöltés állapotának.
7. A **Blob feltöltési** oldal bezárásához válassza a **Bezárás** ikont **(X).**

Tartsa nyitva az **adftutorial** tárolólapot. A segítségével ellenőrizheti ennek a rövid útmutatónak az eredményét.