---
title: Hozzon létre egy Azure Kubernetes Service-erőforrás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre az Azure Kubernetes Service (AKS)-erőforrás.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877446"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Hozzon létre egy Azure Kubernetes Service-erőforrás

1. Lépjen a [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) válassza **létrehozás**.

1. Az a **alapjai** lapra, adja meg a következőket:

    |Beállítás|Érték|
    |--|--|
    |Subscription|Válassza ki a megfelelő előfizetést|
    |Resource group|Válasszon ki egy rendelkezésre álló|
    |Kubernetes-fürt neve|Adjon meg egy nevet (kisbetűk)|
    |Régió|Válasszon ki egy közeli helyet|
    |Kubernetes-verzió|1.12.8 (alapértelmezett)|
    |DNS name prefix|Automatikusan létrehozott, de ez felülírható|
    |A csomópont mérete|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontok száma|A csúszka hagyja az alapértelmezett értéket|

1. Az a **méretezési** lapra, és távozzon **virtuális csomópontok** és **virtuálisgép-méretezési csoportok (előzetes verzió)** állítsa be az alapértelmezett értékeket.
1. Az a **hitelesítési** lapra, és távozzon **egyszerű szolgáltatás** és **engedélyezése az RBAC** állítsa be az alapértelmezett értékeket.
1. Az a **hálózatkezelés** lapra, adja meg a következő lehetőségek közül:

    |Beállítás|Érték|
    |--|--|
    |HTTP-alkalmazások útválasztása|Nem|
    |Hálózati konfiguráció|Alapszintű|

1. Az a **figyelés** lapra, győződjön meg arról, hogy **figyelési szint** értékre van állítva **Igen**, és hagyja **Log Analytics-munkaterület** , a alapértelmezett érték.
1. Az a **címkék** fülre, hagyja üresen a név-érték párok most.
1. Válassza ki **ellenőrzés és létrehozás**.
1. Ellenőrzés elvégzése után válassza **létrehozás**.

> [!NOTE]
> Ha az érvényesítés meghiúsul, lehet egy "Szolgáltatásnév" hiba miatt. Lépjen vissza a **hitelesítési** lapon érdekében pedig később újból **felülvizsgálat + létrehozása**, ahol érvényesítési kell futni, és akkor továbbítja.
