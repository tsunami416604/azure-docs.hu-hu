---
title: Az Azure Security Center és az Azure Container Registry
description: További információ az Azure Security Center és az Azure Container Registry integrációjáról
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205993"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Az Azure Container Registry integrációja a Security Centerrel

Az Azure Container Registry (ACR) egy felügyelt, privát Docker-beállításjegyzék-szolgáltatás, amely tárolja és kezeli a tárolórendszerképeket az Azure-központi telepítések egy központi beállításjegyzékben. Ez alapján a nyílt forráskódú Docker Registry 2.0.

Ha az Azure Security Center standard szintű szintjén, hozzáadhatja a tárolóregisztercsomag. Ez az opcionális funkció mélyebb betekintést nyújt az ARM-alapú kibocsátásijegyzékekben lévő képek biztonsági réseibe. Engedélyezze vagy tiltsa le a csomagot az előfizetés szintjén az előfizetés összes beállításjegyzékének lefedéséhez. Ez a funkció képenként kerül felszámolásra, ahogy az az [árképzési oldalon látható.](security-center-pricing.md) A tárolójegyzék-csomag engedélyezése biztosítja, hogy a Security Center készen áll a beállításjegyzékbe leadott lemezképek bekéselésére. 

Amikor egy képet a rendszerleíró adatbázisba tol, a Security Center automatikusan beolvassa a képet. A kép vizsgálatának indításához nyomja le a tárházba.

Amikor a vizsgálat befejeződik (általában körülbelül 10 perc után), az eredmények a Biztonsági központ ajánlásaiban érhetők el, mint ez:

[![Minta Az Azure Security Center javaslata az Azure Container Registry (ACR) üzemeltetett rendszerképében felfedezett biztonsági résekről](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Az integráció előnyei

A Security Center azonosítja az ARM-alapú ACR-nyilvántartásokat az előfizetésében, és zökkenőmentesen biztosítja a következőket:

* **Az Azure-natív biztonsági rés az** összes lenyomott Linux-lemezképek. A Security Center a képet az iparágvezető biztonsági rés-ellenőrző gyártó, a Qualys képolvasójával vizsgálja. Ez a natív megoldás alapértelmezés szerint zökkenőmentesen integrálva van.

* **Biztonsági javaslatok** ismert biztonsági résű Linux-lemezképekhez. A Security Center részletesen ismerteti az egyes jelentett biztonsági réseket és a súlyossági besorolást. Emellett útmutatást ad a rendszerleíró adatbázisba tolt egyes lemezképeken található biztonsági rések elhárításához.

![Az Azure Security Center és az Azure Container Registry (ACR) magas szintű áttekintése](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a Security Center tárolóbiztonsági szolgáltatásairól, olvassa el az:

* [Az Azure Security Center és a tárolóbiztonság](container-security.md)

* [Integráció az Azure Kubernetes Service-szel](azure-kubernetes-service-integration.md)

* [Virtuálisgép-védelem](security-center-virtual-machine-protection.md) – Ismerteti a Security Center ajánlásait
