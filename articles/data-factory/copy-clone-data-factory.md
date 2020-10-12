---
title: Adatfeldolgozó másolása vagy klónozása Azure Data Factory
description: Megtudhatja, hogyan másolhat vagy klónozott egy adatgyárat Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 304c39f4b6f7852068d4e72adfad2d41eeefc26c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85552970"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Adatfeldolgozó másolása vagy klónozása Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan másolhat vagy klónozott egy adatgyárat a Azure Data Factoryban.

## <a name="use-cases-for-cloning-a-data-factory"></a>Az adatfeldolgozó klónozására szolgáló esetek használata

Íme néhány olyan körülmény, amelyben hasznos lehet egy adatgyár másolása vagy klónozása:

- **Data Factory áthelyezése** egy új régióba. Ha át szeretné helyezni a Data Factory egy másik régióba, a legjobb módszer egy másolat létrehozása a célként megadott régióban, és a meglévő törlése.

- **Data Factory átnevezése**. Az Azure nem támogatja az erőforrások átnevezését. Ha átnevezni szeretne egy adatelőállítót, akkor az adatgyárat más néven is megteheti, és törölheti a meglévőt.

- A **hibakeresési** funkciók nem elégségesek. A legtöbb esetben használhatja a [hibakeresést](iterative-development-debugging.md). Másokban a klónozott homokozó-környezet változásainak kipróbálása nagyobb hangérzékelést tesz lehetővé. Például azt, hogy a paraméteres ETL-folyamatok hogyan fognak működni, ha egy eseményindító a fájl megérkezése és a felskálázás időintervalluma között következik be, előfordulhat, hogy nem könnyen tesztelhető a hibakeresést. Ezekben az esetekben érdemes lehet egy sandbox-környezetet klónozással kísérletezni. Mivel a Azure Data Factory díjat elsősorban a futtatások száma alapján számítjuk fel, a második gyár nem jár további költségekkel.

## <a name="how-to-clone-a-data-factory"></a>Az adatfeldolgozó klónozása

1. Előfeltételként először létre kell hoznia a cél adatait előállítót a Azure Portal.

1. Ha GIT módban van:
    1. Minden alkalommal, amikor közzéteszi a portálon, a gyári Resource Manager-sablont a GIT-be menti az ADF \_ közzétételi ág
    1. Kapcsolja össze az új gyárat _ugyanahhoz_ a tárházhoz, és hozzon létre egy ADF \_ közzétételi ágat. Az erőforrások, például a folyamatok, az adathalmazok és az eseményindítók

1. Ha élő módban van:
    1. Data Factory felhasználói felület lehetővé teszi, hogy az adat-előállító teljes hasznos adatait egy Resource Manager-sablonfájl és egy paraméter-fájlba exportálja. A portálon az **ARM-sablon \ Resource Manager-sablon exportálása** gombra kattintva érhetők el.
    1. A paraméter fájljának módosítása és az új gyár új értékeinek cseréje is lehetséges.
    1. Ezután üzembe helyezheti a standard Resource Manager-sablonok üzembe helyezési módszereivel.

1. Ha rendelkezik SelfHosted-IntegrationRuntime a forrás-előállítóban, előbb létre kell hoznia azt ugyanazzal a névvel a cél gyárban. Ha meg szeretné osztani a SelfHosted Integration Runtime a különböző gyárak között, az [itt](create-shared-self-hosted-integration-runtime-powershell.md) közzétett mintát használhatja a SelfHosted IR megosztásához.

1. Biztonsági okokból a generált Resource Manager-sablon nem tartalmaz titkos adatokat, például a társított szolgáltatások jelszavait. Ezért a hitelesítő adatokat központi telepítési paraméterekként kell megadnia. Ha a beállítások manuális bevitele nem kívánatos, fontolja meg a kapcsolatok karakterláncának és jelszavának beolvasását Azure Key Vault helyett. [Részletek](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>További lépések

Tekintse át az adatfeldolgozó létrehozásával kapcsolatos útmutatót a Azure Portal az [adatfeldolgozó létrehozása a Azure Data Factory felhasználói felület használatával](quickstart-create-data-factory-portal.md)című témakörben.
