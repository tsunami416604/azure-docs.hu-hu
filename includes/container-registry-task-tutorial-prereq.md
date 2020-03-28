---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67133828"
---
## <a name="prerequisites"></a>Előfeltételek

### <a name="get-sample-code"></a>Mintakód letöltése

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](../articles/container-registry/container-registry-tutorial-quick-task.md) lépéseit, valamint elágaztatta és klónozta a mintaadattárat. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

### <a name="container-registry"></a>Tárolóregisztrációs adatbázis

Az oktatóanyag elvégzéséhez rendelkeznie kell egy Azure-beli tárolóregisztrációs adatbázissal az Azure-előfizetésében. Amennyiben létre kell hoznia a regisztrációs adatbázist, tekintse meg az [előző oktatóanyagot](../articles/container-registry/container-registry-tutorial-quick-task.md) vagy a [Rövid útmutató: Tárolóregisztrációs adatbázis létrehozása az Azure CLI-vel](../articles/container-registry/container-registry-get-started-azure-cli.md) című cikket.

## <a name="create-a-github-personal-access-token"></a>Személyes hozzáférési jogkivonat létrehozása a GitHubban

A Git-tárház véglegesítésének elindításához az ACR-feladatoknak személyes hozzáférési jogkivonatra (PAT) van szükségük a tárház eléréséhez. Ha még nem rendelkezik PAT-tal, kövesse az alábbi lépéseket a GitHubon való létrehozáshoz:

1. Lépjen a GitHub PAT-létrehozási oldalára a https://github.com/settings/tokens/new címen.
1. Adja meg a jogkivonat rövid **leírását**, például „ACR Tasks bemutatója”.
1. Válassza ki az ACR hatóköreit a tártár eléréséhez. Nyilvános tártár eléréséhez, mint ebben az oktatóanyagban, **a tárházban**engedélyezze **a repo:status** és **a public_repo**

   ![A GitHub személyes hozzáférési jogkivonatok létrehozására szolgáló oldalának képernyőképe][build-task-01-new-token]

   > [!NOTE]
   > Ha egy *privát* tártár eléréséhez pat-ot szeretne létrehozni, válassza ki a teljes **tártárvezérlés** hatókörét.

1. Kattintson a **Generate token** (Jogkivonat létrehozása) gombra (a rendszer kérheti a jelszó megadását).
1. Másolja és mentse a létrehozott jogkivonatot egy **biztonságos helyre** (ezt a jogkivonatot egy feladat definiálására fogja használni a következő szakaszban).

   ![A létrehozott személyes hozzáférési jogkivonat képernyőképe a GitHubban][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
