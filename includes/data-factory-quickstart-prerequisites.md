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
ms.openlocfilehash: 0a2bf306ccfa3a4a4fc80d0d55493f284185bba9
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648460"
---
## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

### <a name="azure-roles"></a>Azure-szerepkörök
Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. Az előfizetésben található engedélyek megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com), válassza ki a felhasználónevét a jobb felső sarokban, válassza a **további lehetőségek** (...) lehetőséget, majd válassza **a saját engedélyek**lehetőséget. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést.

A Data Factory gyermekerőforrásai – beleértve az adatkészletek, társított szolgáltatások, folyamatok, eseményindítók és integrációs modulok – létrehozására és kezelésére az alábbi követelmények vonatkoznak:

- A gyermekerőforrások Microsoft Azure Portalon való létrehozásához és kezeléséhez a **Data Factory közreműködője** szerepkörhöz kell tartoznia az erőforráscsoport szintjén vagy felette.
- A gyermekerőforrások PowerShell-lel vagy az SDK-val való létrehozásához és kezeléséhez a **közreműködő** szerepkör is elegendő az erőforráscsoport szintjén vagy felette.

Ha szeretne példautasításokat látni arra, hogyan kell egy felhasználót a szerepkörhöz adni, olvassa el a [Szerepkörök hozzáadása](../articles/billing/billing-add-change-azure-subscription-administrator.md) című cikket.

További információkért tekintse meg a következő cikkeket:

- [Data Factory közreműködője szerepkör](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Szerepkörök és engedélyek az Azure Data Factoryhoz](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben a rövid útmutatóban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk *forrás-* és *céladattárként*. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, a létrehozáshoz tekintse meg a [tárfiók létrehozását](../articles/storage/common/storage-quickstart-create-account.md) ismertető cikket. 

#### <a name="get-the-storage-account-name"></a>A Storage-fiók nevének beolvasása
Ehhez a rövid útmutatóhoz szüksége lesz az Azure Storage-fiók nevére. A következő eljárás a Storage-fiók nevének beszerzéséhez nyújt lépéseket: 

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
2. Válassza **a minden szolgáltatás** > **tároló** > **Storage-fiókok**lehetőséget.
3. A **Storage-fiókok** lapon szűrje a Storage-fiókot (ha szükséges), majd válassza ki a Storage-fiókját. 

#### <a name="create-a-blob-container"></a>Blobtároló létrehozása
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban.

1. A Storage-fiók lapon válassza az > áttekintő**Blobok**lehetőséget.
2. A  *\<fiók neve >*  - **Blobok** lap eszköztárán válassza a **tároló**elemet.
3. Az **Új tároló** párbeszédablakban adja meg az **adftutorial** nevet, és kattintson az **OK** gombra.  -   *A\<fiók neve >* **Blobok** lap frissül, hogy tartalmazza a **adftutorial** a tárolók listájában.

   ![Tárolók listája](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Bemeneti mappa és fájl hozzáadása a blob-tárolóhoz
Ebben a szakaszban létrehoz egy **input** nevű mappát az imént létrehozott tárolóban, majd feltölt egy mintát a bemeneti mappába. Mielőtt elkezdené, nyisson meg egy szövegszerkesztőt,például a jegyzettömböt, és hozzon létre egy **EMP. txt** nevű fájlt az alábbi tartalommal:

```emp.txt
John, Doe
Jane, Doe
```

Mentse a fájlt a **C:\ADFv2QuickStartPSH** mappába. (Ha a mappa még nem létezik, hozza létre.) Ezután térjen vissza a Azure Portal, és kövesse az alábbi lépéseket:

1.  -   *A\<fiók neve >* **Blobok** oldalon, ahol abbahagyta, válassza a **adftutorial** elemet a tárolók frissített listájából. (Ha bezárta az ablakot, vagy egy másik lapra lépett, jelentkezzen be újra a [Azure Portalba](https://portal.azure.com), válassza a **minden szolgáltatás** > **Storage** > **Storage-fiókok**lehetőséget, válassza ki a Storage-fiókot, majd válassza a **Blobok** elemet. **adftutorial.** )  > 
2. A **adftutorial** -tároló lapja eszköztárán válassza a **feltöltés**lehetőséget.
3. A **blob feltöltése** lapon kattintson a **fájlok** mezőre, majd keresse meg és válassza ki az **EMP. txt** fájlt.
4. Bontsa ki a **speciális** fejlécet. A lap ekkor megjelenik:

   ![Kattintás a Speciális hivatkozásra](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. A **feltöltés mappába** mezőbe írja be a **bemenet**értéket.
6. Kattintson a **Feltöltés** gombra. A listában meg kell jelennie az **emp.txt** fájlnak és a feltöltés állapotának.
7. Válassza a **Bezárás** ikont ( **X**) a **blob feltöltése** oldal bezárásához.

Tartsa megnyitva a **adftutorial** -tároló lapot. A segítségével ellenőrizheti ennek a rövid útmutatónak az eredményét.