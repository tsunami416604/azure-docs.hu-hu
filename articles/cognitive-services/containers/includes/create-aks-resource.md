---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozzon létre egy azure kubernetes-(AKS)-erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: eb2d609c3a7dfd769c6a19854366ac5e45fddb7d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712520"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Hozzon létre egy Azure Kubernetes Service (AKS)-erőforrás

1. Nyissa meg a [létrehozása](https://ms.portal.azure.com/#create/microsoft.aks) a Kubernetes-szolgáltatás.

1. Az a **alapjai** lapra, adja meg a következő adatokat:

    |Beállítás|Value|
    |--|--|
    |Subscription|Válassza ki a megfelelő előfizetést|
    |Resource group|Válasszon ki egy rendelkezésre álló|
    |Kubernetes-fürt neve|Kívánt nevét (, kisbetű)|
    |Régió|Válasszon ki egy közeli helyet|
    |Kubernetes-verzió|1.12.8 (alapértelmezett)|
    |DNS name prefix|Automatikusan létrehozott, de igény szerint felülbírálható|
    |A csomópont mérete|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontok száma|A csúszka hagyja az alapértelmezett értéket|

1. Az a **méretezési** fülre, hagyja a *virtuális csomópontok* és *a virtual machine scale sets* alapértelmezett értékeket.
1. Az a **hitelesítési** lapra, és távozzon *szolgáltatásnév* és *engedélyezése az RBAC* alapértelmezett értékeket.
1. Az a **hálózatkezelés** lapra, adja meg a következő lehetőségek közül:

    |Beállítás|Value|
    |--|--|
    |HTTP-alkalmazások útválasztása|Nem|
    |Hálózati konfiguráció|Alapszintű|

1. Az a **figyelés** lapra, győződjön meg arról, hogy *figyelési szint* értékre van állítva **Igen** és a továbbiakban hagyja a *Log Analytics-munkaterület* , a alapértelmezett érték
1. Az a **címkék** fülre, hagyja üresen a név-érték párok most.
1. Kattintson a **ellenőrzés és létrehozás**.
1. Kattintson az ellenőrzés elvégzése után **létrehozás**.

> [!NOTE]
> Ha az érvényesítés meghiúsul, onnan lehet egy *szolgáltatásnév* hiba. Lépjen vissza a *hitelesítési* lapra, majd vissza a *felülvizsgálat + létrehozása* érvényesítési kell hajtható végre, akkor továbbítja.
