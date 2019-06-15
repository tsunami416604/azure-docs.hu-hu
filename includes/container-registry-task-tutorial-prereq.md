---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133828"
---
## <a name="prerequisites"></a>Előfeltételek

### <a name="get-sample-code"></a>Mintakód letöltése

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](../articles/container-registry/container-registry-tutorial-quick-task.md) lépéseit, valamint elágaztatta és klónozta a mintaadattárat. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

### <a name="container-registry"></a>Tárolóregisztrációs adatbázis

Az oktatóanyag elvégzéséhez rendelkeznie kell egy Azure-beli tárolóregisztrációs adatbázissal az Azure-előfizetésében. Ha a beállításjegyzék van szüksége, tekintse meg a [előző oktatóanyagban](../articles/container-registry/container-registry-tutorial-quick-task.md), vagy [a rövid útmutató: Hozzon létre egy tároló-beállításjegyzéket az Azure CLI-vel](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Személyes hozzáférési jogkivonat létrehozása a GitHubban

Egy véglegesítés a Git-tárházat a feladat indításához az ACR feladatok kell egy személyes hozzáférési jogkivonat (PAT) a tárház eléréséhez. Ha Ön még nem rendelkezik a PAT, az alábbi lépésekkel hozhat létre egy, a GitHub:

1. Lépjen a GitHub PAT-létrehozási oldalára a https://github.com/settings/tokens/new címen.
1. Adja meg a jogkivonat rövid **leírását**, például „ACR Tasks bemutatója”.
1. Válassza ki a hatókört az ACR-hez a tárház eléréséhez. Ebben az oktatóanyagban mint nyilvános adattár eléréséhez a **tárház**, engedélyezése **tárház: állapot** és **public_repo**

   ![A GitHub személyes hozzáférési jogkivonatok létrehozására szolgáló oldalának képernyőképe][build-task-01-new-token]

   > [!NOTE]
   > Egy PAT eléréséhez létrehozásához egy *privát* adattár, teljes hatókör kiválasztása **tárház** vezérlő.

1. Kattintson a **Generate token** (Jogkivonat létrehozása) gombra (a rendszer kérheti a jelszó megadását).
1. Másolja és mentse a létrehozott jogkivonatot egy **biztonságos helyre** (ezt a jogkivonatot egy feladat definiálására fogja használni a következő szakaszban).

   ![A létrehozott személyes hozzáférési jogkivonat képernyőképe a GitHubban][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
