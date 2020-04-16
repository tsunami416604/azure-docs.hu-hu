---
title: Adatgyár másolása vagy klónozása az Azure Data Factoryban
description: Ismerje meg, hogyan másolhat vagy klónozhat adatelmiszt az Azure Data Factoryban
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414113"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Adatgyár másolása vagy klónozása az Azure Data Factoryban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk ismerteti, hogyan másolhatja vagy klónozhatja az Azure Data Factory adat-előállító.

## <a name="use-cases-for-cloning-a-data-factory"></a>Esetek használata adat-előállító klónozásához

Íme néhány olyan körülmény, amelyben hasznosnak találhatja az adatgyár másolását vagy klónozását:

-   **Erőforrások átnevezése**. Az Azure nem támogatja az erőforrások átnevezését. Ha át szeretne nevezni egy adat-előállítót, klónozhatja az adat-előállítót egy másik névvel, majd törölheti a meglévőt.

-   **A hibakeresés akkor változik,** ha a hibakeresési szolgáltatások nem elegendőek. Néha, hogy tesztelje a módosításokat, érdemes kipróbálni a módosításokat egy másik gyárban, mielőtt azokat a fő. A legtöbb esetben használhatja a Debug. Az eseményindítók változásai azonban, például a módosítások viselkedése, amikor egy eseményindító automatikusan vagy egy időablakban, nem lehet könnyen tesztelhető bejelentkezés nélkül. Ezekben az esetekben, klónozás a gyár és alkalmazása a változások ott van sok értelme. Mivel az Azure Data Factory elsősorban a futtatások száma alapján számít fel díjat, a második gyár nem vezet további díjakhoz.

## <a name="how-to-clone-a-data-factory"></a>Adatgyár klónozása

1. Az Azure Portalon található Data Factory felhasználói felületlehetővé teszi az adatgyár teljes hasznos adatának exportálását egy Resource Manager-sablonba, valamint egy paraméterfájlt, amely lehetővé teszi a gyár klónozása során módosítani kívánt értékek módosítását.

1. Előfeltételként létre kell hoznia a céladat-előállító az Azure Portalon létre kell hoznia.

1. Ha van egy SelfHosted IntegrationRuntime a forrásgyárban, elő kell hoznia azt az azonos nevű a célgyárban. Ha meg szeretné osztani a SelfHosted IRs különböző gyárak között, használhatja a mintát [itt](source-control.md#best-practices-for-git-integration)közzétett .

1. Ha GIT módban van, minden alkalommal, amikor közzéteszi a portálról, a gyár Erőforrás-kezelő sablonja a Tárház adf_publish ágában a GIT-be kerül.

1. Más esetekben az Erőforrás-kezelő sablon a portál **Erőforrás-kezelő fájljának exportálása** gombra kattintva tölthető le.

1. Az Erőforrás-kezelő sablon letöltése után üzembe helyezheti azt a szabványos Resource Manager-sablon telepítési módszerekkel.

1. Biztonsági okokból a létrehozott Erőforrás-kezelő sablon nem tartalmaz titkos információkat, például csatolt szolgáltatások jelszavait. Ennek eredményeképpen meg kell adnia ezeket a jelszavakat központi telepítési paraméterekként. Ha a paraméterek megadása nem kívánatos, be kell szereznie a kapcsolati karakterláncok és jelszavak a csatolt szolgáltatások az Azure Key Vault.

## <a name="next-steps"></a>További lépések

Tekintse át az adatgyár létrehozásához az Azure Portalon az [Adatgyár létrehozása az Azure Data Factory felhasználói felületén.](quickstart-create-data-factory-portal.md)
