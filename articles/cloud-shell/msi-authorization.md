---
title: Felügyelt identitások használata az Azure-erőforrásokhoz az Azure Cloud Shellben |} A Microsoft Docs
description: Hitelesítés a kódot az Azure Cloud Shellben MSI-vel
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: fe77deeedc34bf769065e34ac2f81d631b0004d6
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352948"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Az Azure-erőforrásokhoz az Azure Cloud Shellben használja a felügyelt identitásokból

Az Azure Cloud Shell engedélyezés és a felügyelt identitásokból támogatja az Azure-erőforrásokhoz. Ez a hozzáférési jogkivonatok biztonságosan kommunikáljon az Azure-szolgáltatások használatára.

## <a name="about-managed-identities-for-azure-resources"></a>Tudnivalók az Azure-erőforrások felügyelt identitásairól
Ha a felhőbeli alkalmazások készítése a hitelesítő adatok biztonságos kezelése közös kihívást, amely kell lenniük a kódban, a cloud serviceshez hitelesítéséhez. A Cloud Shellben szükség lehet a hitelesítő adatait, amely egy szkript szükség lehet a Key Vaultból lekérés hitelesítéséhez.

Felügyelt identitások az Azure-erőforrások így egyszerűbb a probléma megoldására azzal, hogy az Azure-szolgáltatások automatikusan felügyelt identitás az Azure Active Directoryban (Azure AD). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

## <a name="acquire-access-token-in-cloud-shell"></a>A Cloud Shellben a hozzáférési jogkivonat beszerzése

Hajtsa végre az alábbi parancsokat az MSI-hozzáférési jogkivonat környezeti változóban, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Jogkivonat lejáratának kezelése

A helyi MSI-alrendszer gyorsítótárazza a jogkivonatokat. Így meg lehet hívni többször van lehetősége, és a egy Azure ad-hez az átvitel közbeni hívás eredménye csak akkor, ha:
- gyorsítótár-tévesztés miatt nem jogkivonat a gyorsítótárban történik
- a jogkivonat lejárt

Ha a kódban a jogkivonat gyorsítótárba helyezi, forgatókönyvek kezeléséhez, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt felkészültnek kell lennie.

Token hibáinak kezelése, látogasson el a [MSI lap kapcsolatos MSI hozzáférési jogkivonatok sütővasak](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>További lépések
[További információ az MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[MSI-beli virtuális gépekről származó hozzáférési tokenek beszerzése](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
