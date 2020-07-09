---
title: Az Azure adatelemzési Virtual Machines
description: Megtudhatja, hogyan csatlakozhat egy Azure Data Science Virtual Machine (DSVM) szolgáltatáshoz, hogy kiterjessze a számítási teljesítményt Azure Notebooks előzetes verzióra.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 291f1ac093568f50ad6146b70fa1ef69263b7b5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830306"
---
# <a name="use-azure-data-science-virtual-machines"></a>Az Azure adatelemzési Virtual Machines

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Alapértelmezés szerint a projektek az **ingyenes számítási** szinten futnak, amely legfeljebb 4 GB memóriával és 1 GB-nyi adattal használható a visszaélések megelőzése érdekében. Ezeket a korlátozásokat egy másik, Azure-előfizetésben üzembe helyezett virtuális géppel lehet kihagyni. Erre a célra a legjobb választás egy Azure Data Science Virtual Machine (DSVM) az **Data Science Virtual Machine for Linux (Ubuntu)** rendszerkép használatával. Egy ilyen DSVM előre konfigurálva van minden, amire szüksége van a Azure Notebookshoz, és automatikusan megjelenik a Azure Notebooks **Futtatás** legördülő listájában.

> [!Note]
> A Azure Notebooks csak a Linux Ubuntu-lemezképpel létrehozott Dsvm támogatott. A jegyzetfüzetek Windows 2012, Windows 2016 vagy Linux CentOS rendszerű lemezképeken nem támogatottak.

## <a name="create-a-dsvm-instance"></a>DSVM-példány létrehozása

Új DSVM-példány létrehozásához kövesse az [Ubuntu-Data Science VM létrehozása](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)című témakör utasításait. A díjszabással kapcsolatos további információkért lásd: [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Kapcsolódás a DSVM

Miután létrehozta a DSVM, válassza a **Futtatás** legördülő listát a Azure Notebooks projekt irányítópulton, és válassza ki a megfelelő DSVM-példányt. A legördülő lista DSVM-példányokat jelenít meg, ha a következő feltételek teljesülnek:

- Bejelentkezett Azure Notebooksba egy olyan fiókkal, amely Azure Active Directory (HRE), például egy vállalati fiókot használ.
- A fiókja egy Azure-előfizetéshez csatlakozik.
- Az előfizetésben legalább egy olyan virtuális gép található, amely legalább olvasói hozzáféréssel rendelkezik, amely a Linux (Ubuntu) Data Science Virtual Machine használja.)

![Data Science Virtual Machine példányok a projekt irányítópultjának legördülő listájában](media/project-compute-tier-dsvm.png)

Ha kiválaszt egy DSVM-példányt, Azure Notebooks kérheti a virtuális gép létrehozásakor használt adott számítógép hitelesítő adatait.

> [!Important]
> A felhasználónévnek kisbetűsnek kell lennie a JupyterHub-vel való használathoz.

Ha valamelyik feltétel nem teljesül, továbbra is csatlakozhat a DSVM. A legördülő listában válassza a **közvetlen számítás** lehetőséget, amely egy név (a listában megjelenítendő), a virtuális gép IP-címe és portja (jellemzően 8000, az alapértelmezett port, amelyre a JupyterHub figyeli), valamint a virtuális gép hitelesítő adatainak megadását kéri:

![Rákérdezés a közvetlen számítási lehetőség kiszolgálói adatainak gyűjtésére](media/project-compute-tier-direct.png)

Ezeket az értékeket a Azure Portal DSVM lapján szerezheti be.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Azure Notebooks fájlok elérése a DSVM

A fájlrendszer-hozzáférés támogatott a DSVM-verziók 19.06.15 vagy újabb verzióiban. A verzió ellenõrzéséhez először csatlakozzon a DSVM SSH-n keresztül, majd futtassa a következő parancsot: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (az itt látható pontos IP-címet kell használnia). A verziószám a "version" kimenetben jelenik meg.

Ha meg szeretné őrizni a fájlelérési utak paritását az **ingyenes számítási** réteggel, egyszerre csak egy projektet lehet megnyitni egy DSVM. Új projekt megnyitásához először le kell állítania az Open projektet.

Amikor egy projekt egy virtuális gépen fut, a fájlok a Jupyter-kiszolgáló gyökérkönyvtárában vannak csatlakoztatva (a JupyterHub könyvtárban látható), és lecserélik az alapértelmezett Azure Notebooks fájlokat. Ha a notebook felhasználói felületén a **Leállítás** gombbal állítja le a virtuális gépet, Azure Notebooks visszaállítja az alapértelmezett fájlokat.

![Leállítás gomb a Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Új DSVM-felhasználók létrehozása

Ha több felhasználó osztozik egy DSVM, elkerülheti egymás blokkolását úgy, hogy DSVM-felhasználót hoz létre és használ az egyes notebook-felhasználók számára:

1. A [Azure Portal](https://portal.azure.com)navigáljon a virtuális géphez.
1. A bal oldali margó **támogatás + hibaelhárítás** területén válassza a **jelszó alaphelyzetbe állítása**lehetőséget.
1. Adjon meg egy új **felhasználónevet**. A felhasználónévnek kisbetűsnek kell lennie a JupyterHub-vel való használathoz. Adjon meg egy jelszót. Ezután válassza a **frissítés**lehetőséget. (A meglévő felhasználónevek nincsenek hatással.)
1. Ismételje meg az előző lépést a további felhasználók számára.

## <a name="next-steps"></a>További lépések

További információ az [Azure Adatelemzési Virtual Machines bevezetésének](/azure/machine-learning/data-science-virtual-machine/overview)dsvm.
