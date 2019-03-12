---
title: Másolása vagy klónozása egy data factoryt az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan másolása vagy klónozása egy data factoryt az Azure Data Factoryban
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 96ea8142e2f7794d3c15c6efb436eafa585bc8fd
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575613"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Másolása vagy klónozása egy data factoryt az Azure Data Factoryban

Ez a cikk ismerteti, hogyan másolása vagy klónozása egy data factoryt az Azure Data Factoryban.

## <a name="use-cases-for-cloning-a-data-factory"></a>Adat-előállító Klónozás alkalmazási helyzetei

Íme néhány a körülmények, ahol, előfordulhat, hogy hasznosnak másolása vagy klónozása az adat-előállító:

-   **Erőforrások átnevezése**. Az Azure nem támogatja az átnevezési erőforrásokat. Ha azt szeretné, adat-előállító átnevezése, klónozza az adat-előállító egy másik névvel, és törölje a meglévő.

-   **Hibakeresés a módosítások** Ha a hibakeresési funkciók nem elegendő. Néha tesztelheti a módosításokat, érdemes egy másik factoryben, mielőtt alkalmazná őket a fő egy tesztelheti a módosításokat. A legtöbb esetben a hibakeresési is használhatja. Eseményindítók, a módosítások azonban, mint például amikor egy eseményindítót a módosítások működése automatikusan hív, vagy adott időtartamra nem lehet testable egyszerűen ellenőrzése nélkül. Ezekben az esetekben a Klónozás a gyári, és ott a módosítások alkalmazása párosítása révén. Mivel az Azure Data Factory elsősorban a futtatások száma alapján számláz, a második gyári nem vezet a további díjakat.

## <a name="how-to-clone-a-data-factory"></a>Hogyan klónozhatók adat-előállítók

1. A Data Factory felhasználói felülete az Azure Portalon lehetővé teszi a Resource Manager-sablonnal, és a egy alkalmazásparaméter-fájlt, amely lehetővé teszi, hogy akkor sem tudják módosítani az előállító klónozásakor módosítani szeretné azokat a data factory teljes hasznos exportálása.

1. Előfeltételként szükséges a célként megadott adat-előállító létrehozása az Azure Portalról.

1. Ha a forrás-előállító egy SelfHosted IntegrationRuntime, meg kell előre létre hozni objektumosztállyal, a cél-előállító ugyanazzal a névvel. Ha meg szeretné osztani a különböző előállítók között SelfHosted IRS-nek, használhatja a közzétett minta [Itt](author-visually.md#best-practices-for-git-integration).

1. Ha a GIT módban minden alkalommal, amikor közzéteszi a portálról, az előállító Resource Manager-sablon mentett GIT-adattár az adf_publish ágban található.

1. Más esetekben a Resource Manager-sablon kattintva letölthető a **Resource Manager sablon exportálása** gomb a portálon.

1. Miután letöltötte a Resource Manager-sablon, a standard Resource Manager-sablon üzembe helyezési módszer használatával telepítheti.

1. Biztonsági okokból a létrehozott Resource Manager-sablon nem tartalmaz minden olyan titkos információk, például jelszavak, a társított szolgáltatások. Ennek eredményeképpen kell adnia ezeket üzembe helyezéshez megadott paraméterek jelszavakat. Ha a paraméterek megadása nem kívánatos, rendelkezik a kapcsolati karakterláncok és a jelszavakat, a társított szolgáltatások beszerzése az Azure Key Vaultban.

## <a name="next-steps"></a>További lépések

Olvassa el az adat-előállító létrehozása az Azure Portalon található útmutatást [adat-előállító létrehozása az Azure Data Factory felhasználói felületének használatával](quickstart-create-data-factory-portal.md).
