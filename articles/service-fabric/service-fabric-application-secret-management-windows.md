---
title: Állítsa be a titkosítási tanúsítványt, és titkosítani a titkos kulcsok az Azure Service Fabric Windows-fürtök |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be a titkosítási tanúsítványt, és Windows-fürtök a titkos kulcsok titkosítására.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662312"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Állítsa be a titkosítási tanúsítványt, és a kulcsok a Windows-fürtök titkosítása
Ez a cikk bemutatja, hogyan állítsa be a titkosítási tanúsítványt, és a kulcsok a Windows-fürtök titkosíthatja. Linux-fürtöket, lásd: [beállítása beállítása egy titkosítási tanúsítványt, és titkosítani a titkos kulcsokat a Linux-fürtökön.][secret-management-linux-specific-link]

[Az Azure Key Vault] [ key-vault-get-started] itt történik, a tanúsítványok biztonságos tárolási helyként, valamint arra, hogy a Service Fabric-fürtök az Azure-ban telepített tanúsítványok beolvasása. Ha nem telepíti az Azure-ba, nem kell a titkos kulcsokat a Service Fabric-alkalmazások kezelése a Key Vault használatával. Azonban *használatával* egy alkalmazás titkos kulcsainak van cloud platform-agnosztikus üzemeltetett fürt központilag telepített alkalmazások. 

## <a name="obtain-a-data-encipherment-certificate"></a>Adatok titkosítási tanúsítvány beszerzése
Egy data titkosítási tanúsítvánnyal szigorúan szolgál az titkosítását és visszafejtését [paraméterek] [ parameters-link] a szolgáltatások Settings.xml és [környezeti változók] [ environment-variables-link] a ServiceManifest.xml a szolgáltatás. Akkor sem a hitelesítéshez használt vagy titkosított szöveget az aláírást. A tanúsítvány a következő követelményeknek kell megfelelnie:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható a tanúsítványt kell létrehozni.
* A tanúsítvány kulcshasználati adattitkosítás (10) kell tartalmaznia, és nem tartalmazhat kiszolgálói hitelesítés vagy az ügyfél-hitelesítéssel. 
  
  Például, ha a PowerShell-lel, önaláírt tanúsítvány létrehozása a `KeyUsage` jelzőt kell beállítani `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Telepítse a tanúsítványt a fürtben
Ezt a tanúsítványt a fürt minden csomópontján telepítve kell lennie. Lásd: [hogyan hozhat létre egy fürtöt az Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] telepítési utasítások alapján. 

## <a name="encrypt-application-secrets"></a>Titkos alkalmazáskulcsok titkosítása
A következő PowerShell-parancsot a titkos kulcs titkosítására szolgál. Ez a parancs csak titkosítja a értéket; ugyanúgy **nem** jelentkezzen a titkosított szöveget. A titkos értékek ciphertext előállításához a fürtben telepített ugyanolyan titkosítási tanúsítvánnyal kell használnia:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Az eredményül kapott base-64 kódolású karakterláncot tartalmazza a titkos ciphertext és is a titkosításhoz használt tanúsítvány adatait.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [adja meg a titkosított titkos kulcsok egy alkalmazásban.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
