---
title: Az Azure Data Science virtuális gépek használata
description: Csatlakozás, az Azure adatelemzési virtuális gép (DSVM) terjeszthető ki a rendelkezésre álló számítási teljesítmény az Azure-jegyzetfüzetek.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: getroyer
ms.openlocfilehash: 9b762f1b3f1c17e15b051e72f5d2cf98bef446bf
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137758"
---
# <a name="use-azure-data-science-virtual-machines"></a>Az Azure Data Science virtuális gépek használata

Alapértelmezés szerint a projektek futnak a **ingyenes számítási** szint, amely a 4 GB memóriát és 1 GB adatot visszaélések megelőzése érdekében. Elkerülheti, hogy ezek a korlátozások használatával már kiépített egy Azure-előfizetésében lévő másik virtuális gépet. Erre a célra a legjobb választás az Azure adatelemzési virtuális gép (DSVM) használatával a **adatelemző virtuális gép Linux (Ubuntu)** kép. Az ilyen egy adatelemző virtuális GÉPET tartalmaz minden Azure-notebookokhoz kell, és automatikusan megjelenik az előre konfigurált a **futtatása** legördülő listából válassza ki az Azure-jegyzetfüzeteket.

> [!Note]
> Az Azure notebookok csak a on Linux Ubuntu-képre hoztak létre Dsvm támogatott. Notebookok nem támogatottak a Windows 2012, Windows 2016 vagy Linux CentOS-rendszerképeket.

## <a name="create-a-dsvm-instance"></a>A DSVM-példány létrehozása

Új DSVM-példány létrehozásához kövesse az utasításokat [hozzon létre egy Ubuntu adatelemző virtuális gép](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Beleértve a szolgáltatás díjszabását további információkért lásd: [adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>A dsvm-hez való csatlakozáshoz

A DSVM létrehozása után válassza ki a **futtatása** legördülő listából válassza ki az Azure notebookokban irányítópult projektre, és válassza ki a megfelelő DSVM-példányt. A legördülő listából válassza ki a DSVM-példányok látható, ha az alábbi feltételek teljesülése esetén:

- Egy olyan fiókkal, amely használja az Azure Active Directory (AAD), például egy vállalati fiók jelentkezett be az Azure-jegyzetfüzeteket.
- A fiók Azure-előfizetés csatlakoztatva van.
- Egy vagy több virtuális gépekkel rendelkezik, az adott előfizetésben való legalább olvasó access, az adatelemző virtuális gép által a Linux (Ubuntu) rendszerkép.)

![A legördülő listából válassza ki a projekt irányítópultján található adatok adatelemzési virtuálisgép-példányok](media/project-compute-tier-dsvm.png)

Amikor kiválaszt egy DSVM-példányt, Azure notebookok kérheti az adott gép hitelesítő adatainak a virtuális gép létrehozásakor használt.

Ha a feltételek nem teljesülnek, továbbra is csatlakozhat a dsvm-hez. A legördülő listából válassza ki a **közvetlen számítási** beállítással szabályozhatja, hogy megjelenjen a listában) (név, IP-címet a virtuális gép és portot (általában 8000, mely JupyterHub figyeli az alapértelmezett portot) és a virtuális gép hitelesítő adatainak kéri:

![Rákérdezés a közvetlen Compute option server adatainak összegyűjtése](media/project-compute-tier-direct.png)

Ezen értékek lekérését az DSVM oldal az Azure Portalon.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>A dsvm-hez az Azure-jegyzetfüzetek fájlokhoz fér hozzá

A fájl elérési utak a paritásos megőrzéséhez a **ingyenes számítási** szinten is tudja csak nyissa meg egy projektet egy adatelemző virtuális GÉPET egy időben. Nyisson meg egy új projektet, hogy le kell állítani a megnyitott projektben először.

![Leállítás gombját, az Azure-jegyzetfüzetekben](media/shutdown.png)

Ha egy projektet egy virtuális gépen fut, a fájlok csatlakoztatva vannak-e a gyökérkönyvtárban a Jupyter-kiszolgáló (könyvtárral JupyterHub), és cserélje le az alapértelmezett Azure notebookok fájlokat. Amikor leállítja a virtuális gépet a **leállítási** gomb a notebook UI, az Azure-jegyzetfüzetek visszaállítja az alapértelmezett fájlokat.

![Leállítás gombját, az Azure-jegyzetfüzetekben](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Hozzon létre új DSVM-felhasználók

Ha több felhasználó osztozik egy adatelemző virtuális GÉPET, elkerülheti a blokkolás egymással létrehozásával és használatával a DSVM-felhasználó minden olyan jegyzetfüzet felhasználóhoz:

1. Az a [az Azure Portal](https://portal.azure.com), keresse meg a virtuális géphez.
1. A **támogatás + hibaelhárítás** válassza a bal margón **jelszó alaphelyzetbe állítása**.
1. Adjon meg egy új felhasználónevet és jelszót, és válassza ki **frissítés**. (Meglévő felhasználónevek nem változnak.)
1. Az előző lépés megismétlésével további felhasználók számára.

## <a name="next-steps"></a>További lépések

A Dsvm többet [Bevezetés az Azure Data Science Virtual Machines](/azure/machine-learning/data-science-virtual-machine/overview).
