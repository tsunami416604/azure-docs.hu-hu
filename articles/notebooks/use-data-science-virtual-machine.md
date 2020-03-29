---
title: Azure-adatelemzési virtuális gépek használata
description: Megtudhatja, hogyan csatlakozhat egy Azure Data Science virtuális géphez (DSVM) az Azure Notebookelőzetes verzió ban elérhető számítási teljesítmény bővítéséhez.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898403"
---
# <a name="use-azure-data-science-virtual-machines"></a>Azure-adatelemzési virtuális gépek használata

Alapértelmezés szerint a projektek a **szabad számítási** szinten futnak, amely 4 GB memóriára és 1 GB adatra korlátozódik a visszaélések megelőzése érdekében. Ezeket a korlátozásokat megkerülheti egy azure-előfizetésben kiépített másik virtuális gép használatával. Erre a célra a legjobb választás egy Azure Data Science virtuális gép (DSVM) a **Data Science Virtual Machine for Linux (Ubuntu)** rendszerkép használatával. Egy ilyen DSVM előre konfigurálva van mindennel, amire szüksége van az Azure Notebooks, és automatikusan megjelenik a **Futtatás** legördülő lista az Azure Notebooks.

> [!Note]
> Az Azure Notebooks csak az Linux Linux-rendszerképpel létrehozott DSVM-ek támogatott. Windows 2012, Windows 2016 vagy Linux CentOS rendszerképeken a notebookok nem támogatottak.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>DSVM-példány létrehozása

Új DSVM-példány létrehozásához kövesse az [Ubuntu Data Science VM létrehozása](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)című útmutatóutasításait. További információ, beleértve az árképzésrészleteit, lásd: [Data Science virtuális gépek.](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

## <a name="connect-to-the-dsvm"></a>Csatlakozás a DSVM-hez

Miután létrehozta a DSVM-et, válassza ki a **Futtatás** legördülő listát az Azure Notebooks projekt irányítópultján, és válassza ki a megfelelő DSVM-példányt. A legördülő lista dsvm-példányokat jelenít meg, ha a következő feltételek teljesülnek:

- Az Azure-jegyzetfüzetekbe az Azure Active Directoryt (AAD) használó fiókkal, például egy vállalati fiókkal jelentkezett be.
- A fiók egy Azure-előfizetéshez kapcsolódik.
- Van egy vagy több virtuális gép az adott előfizetésben, legalább Reader hozzáféréssel, amely a Data Science Virtual Machine for Linux (Ubuntu) lemezképet használja.)

![Adatelemzési virtuálisgép-példányok a projekt irányítópultjának legördülő listájában](media/project-compute-tier-dsvm.png)

DSVM-példány kiválasztásakor az Azure Notebooks kérheti a virtuális gép létrehozásakor használt adott számítógép-hitelesítő adatokat.

> [!Important]
> A jupyterhubhasználatához a felhasználónévnek kisbetűsnek kell lennie.

Ha a feltételek bármelyike nem teljesül, továbbra is csatlakozhat a DSVM-hez. A legördülő listában válassza ki a **Direct Compute opciót,** amely egy nevet (a listában való megjelenítését), a virtuális gép IP-címét és portját (általában 8000-et, az alapértelmezett portot, amelyre a JupyterHub figyel) és a virtuális gép hitelesítő adatait kéri:

![A Direct Compute beállítás kiszolgálóadatainak összegyűjtésének kérése](media/project-compute-tier-direct.png)

Ezeket az értékeket az Azure Portalon a DSVM-lapon szerezheti be.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Az Azure Notebook-fájlok elérése a DSVM-ből

A fájlrendszer-hozzáférés a 19.06.15-ös vagy újabb dsvm-verziók esetén támogatott. A verzió ellenőrzéséhez először csatlakozzon a DSVM-hez SSH-n keresztül, majd futtassa a következő parancsot: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (az itt látható pontos IP-címet kell használnia). A verziószám a "verzió" kimenetében jelenik meg.

A fájlelérési utak paritásának megőrzése a **szabad számítási** szinttel, egyszerre csak egy projektet nyithat meg egy DSVM-en. Új projekt megnyitásához először le kell állítania a megnyitott projektet.

Amikor egy projekt fut egy virtuális gépen, a fájlok a Jupyter-kiszolgáló gyökérkönyvtárába (a JupyterHub-ban megjelenő könyvtárba) vannak csatlakoztatva, és az alapértelmezett Azure Notebooks-fájlokat cserélik le. Amikor leállítja a virtuális gépet a **leállítás** gomb bal -on jegyzetfüzet UI, Azure Jegyzetfüzetök visszaad a hiba fájlokat.

![Leállítás gomb az Azure-jegyzetfüzetekben](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Új DSVM-felhasználók létrehozása

Ha több felhasználó osztozik egy DSVM-en, elkerülheti egymás letiltását, ha minden egyes jegyzetfüzet-felhasználóhoz létrehoz és használ egy DSVM-felhasználót:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a virtuális gépet.
1. A bal margó **Támogatás + hibaelhárítás** csoportjában válassza a **Jelszó alaphelyzetbe állítása lehetőséget.**
1. Adjon meg egy új **felhasználónevet**. A jupyterhubhasználatához a felhasználónévnek kisbetűsnek kell lennie. Adja meg a jelszót. Ezután válassza **a Frissítés**lehetőséget. (A meglévő felhasználóneveket ez nem érinti.)
1. Ismételje meg az előző lépést minden további felhasználó számára.

## <a name="next-steps"></a>További lépések

További információ a DSVM-ekről az [Azure Data Science virtuális gépek bemutatása című témában.](/azure/machine-learning/data-science-virtual-machine/overview)
