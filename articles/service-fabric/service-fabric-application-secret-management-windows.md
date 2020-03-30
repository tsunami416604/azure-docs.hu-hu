---
title: Titkosítási tanúsítvány beállítása Windows-fürtökön
description: Megtudhatja, hogy miként állíthat be titkosítási tanúsítványt, és hogyan titkos kulcsokat titkosíthat Windows-fürtökön.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d9413a37be221adc375836719dc1f467a5571fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610182"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása Windows-fürtökön
Ez a cikk bemutatja, hogyan állíthat be titkosítási tanúsítványt, és hogyan használhatja azt titkos kulcsok titkosítására a Windows-fürtökön. Linux-fürtök esetén [lásd: Titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása Linux-fürtökön.][secret-management-linux-specific-link]

[Az Azure Key Vault][key-vault-get-started] itt a tanúsítványok biztonságos tárolóhelyeként és az Azure-beli Service Fabric-fürtökre telepített tanúsítványok leszállításának egyik módjaként használatos. Ha nem telepíti az Azure-ba, nem kell használnia a Key Vault a Service Fabric-alkalmazások titkos kulcsok kezeléséhez. Azonban egy alkalmazás titkos titkainak *használata* felhőplatform-független, amely lehetővé teszi az alkalmazások üzembe helyezését egy fürtre bárhol üzemeltetett. 

## <a name="obtain-a-data-encipherment-certificate"></a>Adattitkosítási tanúsítvány beszerzése
Az adattitkosítási tanúsítvány szigorúan a szolgáltatás Settings.xml és [a][environment-variables-link] service ServiceManifest.xml fájlban lévő [paraméterek][parameters-link] titkosítására és visszafejtésére szolgál. Nem használható a titkosítási szöveg hitelesítésére vagy aláírására. A bizonyítványnak a következő követelményeknek kell megfelelnie:

* A tanúsítványnak titkos kulcsot kell tartalmaznia.
* A tanúsítványt kulcscseréhez kell létrehozni, amely személyes adatcserefájlba (.pfx) exportálható.
* A tanúsítványkulcs használatának tartalmaznia kell az adattitkosítást (10), és nem tartalmazhat kiszolgálói hitelesítést vagy ügyfélhitelesítést. 
  
  Ha például önaláírt tanúsítványt hoz létre `KeyUsage` a PowerShell `DataEncipherment`használatával, a jelzőt a következőre kell állítani:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>A tanúsítvány telepítése a fürtben
Ezt a tanúsítványt a fürt minden csomópontjára telepíteni kell. [Tekintse meg, hogyan hozhat létre fürtöt][service-fabric-cluster-creation-via-arm] az Azure Resource Manager használatával a beállítási utasításokhoz. 

## <a name="encrypt-application-secrets"></a>Alkalmazástitkok titkosítása
A következő PowerShell-parancs egy titkos titok titkosítására szolgál. Ez a parancs csak az értéket titkosítja; **nem** írja alá a rejtjel szöveget. A titkos értékek titkosítási szövegének létrehozásához ugyanazt a titkosítási tanúsítványt kell használnia, amely a fürtben van telepítve:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Az eredményül kapott base-64 kódolású karakterlánc tartalmazza a titkos titkosítást, valamint a titkosításhoz használt tanúsítvány adatait.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [adhat meg titkosított titkos kulcsokat egy alkalmazásban.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
