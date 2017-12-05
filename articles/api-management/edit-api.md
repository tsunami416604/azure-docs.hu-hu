---
title: "Az API-k és az Azure portál szerkesztése |} Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan API Management (APIM) segítségével az API-k szerkesztése."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 362c36181da706e3fe0a27cc5ab262271c2a1e57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="edit-an-api"></a>Az API-k szerkesztése

Ez az oktatóanyag lépéseit mutatja be API Management (APIM) segítségével az API-k szerkesztése. 

+ Hozzáadásával, törlésével, APIM példány műveletek átnevezése akkor is elvégezhető. 
+ Az API-t swagger szerkesztheti.

## <a name="prerequisites"></a>Előfeltételek

+ [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md)
+ [Importálja, és tegye közzé az első API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Az API-nak APIM szerkesztése

![Az API-k szerkesztése](./media/edit-api/edit-api001.png)

1. Kattintson a **API-k** fülre.
2. Válasszon egyet a korábban importált API-k.
3. Válassza ki a **tervezési** fülre.
4. Válasszon ki egy műveletet, amelynek módosítani szeretné.
5. Nevezze át a művelet, válassza ki a **ceruza** a a **előtér** ablak.

## <a name="update-the-swagger"></a>A swagger frissítése

A backbend API Azure-portálról frissítheti az alábbi lépéseket követve:

1. Válassza ki **összes művelet**
2. Kattintson a Ceruza a **előtér** ablak.

    ![Az API-k szerkesztése](./media/edit-api/edit-api002.png)

    Az API-t swagger jelenik meg.

    ![Az API-k szerkesztése](./media/edit-api/edit-api003.png)

3. A swagger frissítése.
4. Nyomja le az **mentése**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [APIM házirend minták](policy-samples.md)
> [átalakítása és egy közzétett API védelme](transform-api.md)