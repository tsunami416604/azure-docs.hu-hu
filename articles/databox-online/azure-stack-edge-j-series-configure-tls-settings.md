---
title: A TLS 1,2 konfigurálása Azure Stack Edge Pro GPU-eszközt elérő Windows-ügyfeleken
description: Ismerteti, hogyan kell konfigurálni a TLS 1,2-t a Azure Stack Edge Pro GPU-eszközt elérő Windows-ügyfeleken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 15e2d7a144b54f443b8298b20dbfacf78a50f9e1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447553"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>A TLS 1,2 konfigurálása Azure Stack Edge Pro-eszközt elérő Windows-ügyfeleken

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ha Windows-ügyfelet használ a Azure Stack Edge Pro-eszköz eléréséhez, konfigurálnia kell a TLS 1,2-t az ügyfélen. Ez a cikk a TLS 1,2 konfigurálásához nyújt forrásokat és irányelveket a Windows-ügyfélen. 

Az itt megadott irányelvek a Windows Server 2016 rendszerű ügyfeleken végrehajtott tesztelésen alapulnak.

## <a name="configure-tls-12-for-current-powershell-session"></a>A TLS 1,2 konfigurálása az aktuális PowerShell-munkamenethez

Az alábbi lépéseket követve konfigurálja a TLS 1,2-t az ügyfélen.

1. Futtassa a PowerShellt rendszergazdaként.
2. A TLS 1,2 beállítása az aktuális PowerShell-munkamenethez írja be a következőt:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>A TLS 1,2 konfigurálása az ügyfélen

Ha a környezethez az egész rendszerre kiterjedő TLS 1,2-et kívánja beállítani, kövesse a következő dokumentumokban található útmutatást:

- [Általános – a TLS 1,2 engedélyezése](/windows-server/security/tls/tls-registry-settings#tls-12)
- [A TLS 1,2 engedélyezése az ügyfeleken](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [A TLS 1,2 engedélyezése a hely kiszolgálóin és a távoli hely rendszerein](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protokollok a TLS/SSL-ben (Schannel SSP)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Titkosító csomagok](/windows-server/security/tls/tls-registry-settings#tls-12): kifejezetten a [TLS titkosítási csomag megrendelésének konfigurálása](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) , győződjön meg arról, hogy az aktuális titkosítási csomagokat és az előtagot a következő listából kilistázza:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Ezeket a titkosítási csomagokat a beállításjegyzék beállításainak közvetlen szerkesztésével is hozzáadhatja.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Elliptikus görbék beállítása

    Győződjön meg arról, hogy az aktuális elliptikus görbéket és az előtagot a következő listából kilistázza:

    - P-256 
    - P-384

    Ezeket az elliptikus görbéket közvetlenül a beállításjegyzék beállításainak szerkesztésével is hozzáadhatja.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Állítsa be a minimális RSA-kulcs Exchange-méretét 2048-](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)ra.



## <a name="next-steps"></a>További lépések

[Kapcsolódás Azure Resource Managerhoz](azure-stack-edge-j-series-connect-resource-manager.md)