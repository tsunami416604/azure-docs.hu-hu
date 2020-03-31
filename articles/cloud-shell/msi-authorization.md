---
title: Felügyelt identitások használata erőforrásokhoz az Azure Cloud Shellben
description: Kód hitelesítése az MSI-vel az Azure Cloud Shellben
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72328714"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Felügyelt identitások használata az Azure-erőforrásokhoz az Azure Cloud Shellben

Az Azure Cloud Shell támogatja az Azure-erőforrások felügyelt identitásokkal való engedélyezését. Ezzel lekérheti a hozzáférési jogkivonatokat az Azure-szolgáltatásokkal való biztonságos kommunikációhoz.

## <a name="about-managed-identities-for-azure-resources"></a>Tudnivalók az Azure-erőforrások felügyelt identitásairól
A felhőalapú alkalmazások létrehozásakor gyakori kihívás, hogy miként kezelheti biztonságosan azokat a hitelesítő adatokat, amelyeknek a kódban kell lenniük a felhőszolgáltatások hitelesítéséhez. A Cloud Shell szükség lehet hitelesíteni a lekérés a Key Vault egy hitelesítő adatokat, hogy egy parancsfájlt lehet szüksége.

Az Azure-erőforrások felügyelt identitásai egyszerűbbé teszik a probléma megoldását azáltal, hogy az Azure-szolgáltatások nak automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

## <a name="acquire-access-token-in-cloud-shell"></a>Hozzáférési jogkivonat beszerzése a Cloud Shellben

Hajtsa végre a következő parancsokat az MSI-hozzáférési jogkivonat környezeti változóként való `access_token`beállításához.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Jogkivonat lejáratának kezelése

A helyi MSI alrendszer gyorsítótárazza a jogkivonatokat. Ezért meghívhat, amilyen gyakran csak tetszik, és egy on-the-wire hívás az Azure AD eredmények csak akkor, ha:
- gyorsítótár-tévesztés a gyorsítótárban lévő token hiánya miatt következik be
- a token lejárt

Ha gyorsítótárazza a jogkivonatot a kódban, fel kell készülnie olyan forgatókönyvek kezelésére, amelyekben az erőforrás azt jelzi, hogy a jogkivonat lejárt.

A tokenhibák kezeléséhez látogasson el az [MSI oldalára az MSI hozzáférési jogkivonatok curlingről.](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)

## <a name="next-steps"></a>További lépések
[További információ az MSI-ről](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Hozzáférési jogkivonatok beszerzése MSI virtuális gépekről](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
