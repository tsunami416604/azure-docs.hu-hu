---
title: MSI-fájl használata Azure-felhőbe rendszerhéj |} Microsoft Docs
description: Azure Cloud rendszerhéj MSI kódot hitelesítéséhez
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
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517442"
---
# <a name="use-msi-in-azure-cloud-shell"></a>MSI-fájl használata Azure-felhőbe rendszerhéj

Azure Cloud a rendszerhéj támogatja a felügyelt szolgáltatás-identitások (MSI-fájl) az engedélyezés. Használja a hozzáférési jogkivonatok történő biztonságos kommunikációhoz az Azure-szolgáltatásokkal beolvasása.

## <a name="about-managed-service-identity-msi"></a>Felügyelt Szolgáltatásidentitás (MSI) kapcsolatban
Egy közös kérdés esetén felhőalapú alkalmazások biztonságosan kezelése a hitelesítő adatokat, amelyek kell lenniük a kódban való hitelesítéséhez. Felhő rendszerhéj szükség lehet egy parancsfájlt kell esetleg hitelesítő adatokat a Key Vault lekérését hitelesítéséhez.

Felügyelt szolgáltatás identitásának (MSI) teszi egyszerűbbé válik a probléma megoldásához adjon az Azure-szolgáltatások automatikusan felügyelt identitást az Azure Active Directory (Azure AD). Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, többek között a Key Vault, anélkül, hogy a hitelesítő adatok a kódban a szolgáltatással való hitelesítésre szolgáló használhatja.

## <a name="acquire-access-token-in-cloud-shell"></a>A felhő rendszerhéj hozzáférési jogkivonat

A következő parancsok futtatásával egy környezeti változó állítja be az MSI-hozzáférési jogkivonat `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Kezelési jogkivonat lejáratáról

A helyi MSI alrendszer gyorsítótárazza a jogkivonatokat. Ezért hívása, amilyen gyakran csak van lehetősége, és az Azure AD egy a tömörített hívás eredménye csak akkor, ha:
- a gyorsítótár-tévesztései miatt nem jogkivonat a gyorsítótárban történik
- a jogkivonat érvényessége lejárt

Ha a jogkivonat gyorsítótárba helyezi a kódban, helyzetek kezelésére, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt felkészültnek kell lennie.

Token hibák kezelésének, látogasson el a [MSI nézőpontra MSI hozzáférési jogkivonatok sütővasak](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>További lépések
[További tudnivalók az MSI-fájl](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[MSI virtuális gépek hozzáférési tokenek beszerzése](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
