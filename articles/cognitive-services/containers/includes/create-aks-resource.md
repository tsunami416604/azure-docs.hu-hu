---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozzon létre egy azure kubernetes-(AKS)-erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455118"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Hozzon létre egy Azure Kubernetes Service (AKS)-erőforrás

1. Nyissa meg a [létrehozása](https://ms.portal.azure.com/#create/microsoft.aks) a Kubernetes-szolgáltatás.

1. Az a **alapjai** lapra, adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló|
    |Kubernetes-fürt neve|Kívánt nevét (, kisbetű)|
    |Régió|Válasszon ki egy közeli helyet|
    |Kubernetes-verzió|1.12.8 (alapértelmezett)|
    |DNS name prefix|Automatikusan létrehozott, de igény szerint felülbírálható|
    |A csomópont mérete|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontok száma|A csúszka hagyja az alapértelmezett értéket|

1. Az a **méretezési** fülre, hagyja a *virtuális csomópontok* és *a virtual machine scale sets* alapértelmezett értékeket.
1. Az a **hitelesítési** lapra, és távozzon *szolgáltatásnév* és *engedélyezése az RBAC* alapértelmezett értékeket.
1. Az a **hálózatkezelés** lapra, adja meg a következő lehetőségek közül:

    |Beállítás|Érték|
    |--|--|
    |HTTP-alkalmazások útválasztása|Nem|
    |Hálózati konfiguráció|Alapszintű|

1. Az a **figyelés** lapra, győződjön meg arról, hogy *figyelési szint* értékre van állítva **Igen** és a továbbiakban hagyja a *Log Analytics-munkaterület* , a alapértelmezett érték
1. Az a **címkék** fülre, hagyja üresen a név-érték párok most.
1. Kattintson a **ellenőrzés és létrehozás**.
1. Kattintson az ellenőrzés elvégzése után **létrehozás**.

> [!NOTE]
> Ha az érvényesítés meghiúsul, onnan lehet egy *szolgáltatásnév* hiba. Lépjen vissza a *hitelesítési* lapra, majd vissza a *felülvizsgálat + létrehozása* érvényesítési kell hajtható végre, akkor továbbítja.
