---
title: "Gyakori kérdések az Azure Tárolószolgáltatásban"
description: "Azure Tárolószolgáltatás kapcsolatos gyakori kérdésekre adott válaszok biztosít."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 73c49510512c9148f4fee98423b14770fa8602b9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Gyakori kérdések kapcsolatos Azure tároló szolgáltatás (AKS)

Ez a cikk címek gyakran használják az Azure tároló szolgáltatás (AKS) kapcsolatos kérdésekre.

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>Mely Azure-régiók lesz Azure tároló szolgáltatás (AKS)? 

- Közép-Kanada 
- Kelet-Kanada 
- USA középső régiója 
- USA keleti régiója 
- Délkelet-Ázsia 
- Nyugat-Európa 
- USA nyugati régiója, 2. 

## <a name="when-will-additional-regions-be-added"></a>Ha további régiókban megkapja? 

További régiókban támasztott igény növekedésével hozzá szeretné adni.

## <a name="are-security-updates-applied-to-aks-nodes"></a>Biztonsági frissítések AKS csomópontok vonatkoznak? 

Az operációs rendszer biztonsági javításokat is vonatkozik egy éjszakánként ütemezés szerint, a fürt csomópontjaihoz, azonban az újraindítás nem történik. Ha szükséges, csomópontok előfordulhat, hogy újra kell indítani a portál vagy az Azure parancssori felület használatával. Fürt frissítésekor a legújabb Ubuntu lemezképet használja, és az összes biztonsági javítások alkalmazása (újraindítás).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>Tegye javasoljuk az ügyfelek használata ACS vagy AKSs? 

Figyelembe véve, hogy az Azure-tároló szolgáltatás (AKS) GA egy későbbi időpontban lesz, azt javasoljuk, hogy koncepció tartozó készít, fejlesztési és tesztelési AKS a fürtök, de az ACS-Kubernetes fürtök éles.  

## <a name="when-will-acs-be-deprecated"></a>Ha ACS elavulttá válik? 

ACS AKS válik GA környékén elavulttá válik Fürtök áttelepítéséhez AKS 12 hónapon lesz. 12 hónap során az összes ACS-műveleteket is futtathatja.

## <a name="does-aks-support-node-autoscaling"></a>Támogatja a AKS csomópont automatikus skálázás? 

Csomópont automatikus skálázás nem támogatott, de a programba. Vessen egy pillantást a nyitott forrása kívánt [automatikus skálázás megvalósítási][auto-scaler].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Két erőforráscsoport miért jönnek létre az AKS? 

Minden Azure tároló szolgáltatás (AKS) fürt két erőforráscsoport tartalmazza. Az első hozta létre, és csak a AKS erőforrást tartalmaz. A második erőforráscsoport automatikus telepítése során létrehozott, és tartalmazza az összes fürt infrastrukturális erőforrások virtuális gépeket, például hálózati és tárolási erőforrásokat. Ez az erőforráscsoport könnyen erőforrás karbantartása jön létre. 

Az automatikusan létrehozott erőforráscsoport hasonló névvel rendelkezik:

```
MC_myResourceGRoup_myAKSCluster_eastus
```

A storage-fiókok vagy fenntartott nyilvános IP-cím például Kubernetes fürt használni kívánt Azure-erőforrások hozzáadásakor ezeket az erőforrásokat kell létrehozni az automatikus létrehozott erőforráscsoport.   

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS szolgáltatással integrált Azure Key Vault? 

Nem, nem, de ez az integráció tervezett. Addig is, a következő megoldást kipróbálása [Hexadite][hexadite]. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  