---
title: Adatfeldolgozó másolása vagy klónozása Azure Data Factory
description: Megtudhatja, hogyan másolhat vagy klónozott egy adatgyárat Azure Data Factory
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
ms.openlocfilehash: bafe70655f05b5dda32f51393591f82b4b5625f1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678132"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Adatfeldolgozó másolása vagy klónozása Azure Data Factory

Ez a cikk bemutatja, hogyan másolhat vagy klónozott egy adatgyárat a Azure Data Factoryban.

## <a name="use-cases-for-cloning-a-data-factory"></a>Az adatfeldolgozó klónozására szolgáló esetek használata

Íme néhány olyan körülmény, amelyben hasznos lehet egy adatgyár másolása vagy klónozása:

-   **Erőforrások átnevezése**. Az Azure nem támogatja az erőforrások átnevezését. Ha átnevezni szeretne egy adatelőállítót, akkor az adatgyárat más néven is megoszthatja, majd törölheti a meglévőt.

-   A **hibakeresési** funkciók nem elégségesek. Időnként előfordulhat, hogy tesztelni kívánja a módosításokat egy másik gyárban, mielőtt alkalmazná azokat a fő számára. A legtöbb esetben használhatja a hibakeresést. Előfordulhat azonban, hogy az eseményindítók változásai, például hogy a módosítások hogyan viselkednek automatikusan, vagy egy időablakban, nem tesztelhető könnyen a bejelentkezés nélkül. Ezekben az esetekben a gyár klónozása és a módosítások alkalmazása sok értelmet tesz lehetővé. Mivel a Azure Data Factory díjat elsősorban a futtatások száma alapján számítjuk fel, a második gyár nem jár további költségekkel.

## <a name="how-to-clone-a-data-factory"></a>Az adatfeldolgozó klónozása

1. A Azure Portal Data Factory felhasználói felülete lehetővé teszi az adat-előállító teljes hasznos adatainak exportálását egy Resource Manager-sablonba, valamint egy olyan paramétert, amely lehetővé teszi, hogy a gyár klónozásakor módosítani kívánt értékeket módosítsa.

1. Előfeltételként létre kell hoznia a cél adatait előállítót a Azure Portal.

1. Ha rendelkezik SelfHosted-IntegrationRuntime a forrás-előállítóban, előbb létre kell hoznia azt ugyanazzal a névvel a cél gyárban. Ha meg szeretné osztani a SelfHosted IRs-t a különböző gyárak között, használhatja az [itt](source-control.md#best-practices-for-git-integration)közzétett mintát.

1. Ha GIT módban van, minden alkalommal, amikor közzéteszi a portálon, a gyár Resource Manager-sablonját a rendszer az adattár adf_publish-ágában menti a GIT-ben.

1. Más esetekben a Resource Manager-sablon letölthető a portál **Resource Manager-sablon exportálása** gombjára kattintva.

1. A Resource Manager-sablon letöltése után üzembe helyezheti a Resource Manager-sablonok szabványos telepítési módszereivel.

1. Biztonsági okokból a generált Resource Manager-sablon nem tartalmaz titkos adatokat, például a társított szolgáltatások jelszavait. Ennek eredményeképpen ezeket a jelszavakat telepítési paraméterekként kell megadnia. Ha a paraméterek megadása nem kívánatos, akkor a társított szolgáltatások kapcsolati karakterláncait és jelszavát be kell szereznie Azure Key Vault.

## <a name="next-steps"></a>További lépések

Tekintse át az adatfeldolgozó létrehozásával kapcsolatos útmutatót a Azure Portal az [adatfeldolgozó létrehozása a Azure Data Factory felhasználói felület használatával](quickstart-create-data-factory-portal.md)című témakörben.
