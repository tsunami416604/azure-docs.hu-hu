---
title: Állítsa be a titkosítási tanúsítványt, és titkosítani a titkos kulcsok Azure Service Fabric Linux-fürtökön |} A Microsoft Docs
description: Megtudhatja, hogyan állíthatja be egy titkosítási tanúsítványt, és titkosítani a titkos kulcsokat a Linux-fürtökön.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54068976"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Állítsa be a titkosítási tanúsítványt, és titkosítani a titkos kulcsokat a Linux-fürtökön
Ez a cikk bemutatja, hogyan állítsa be a titkosítási tanúsítványt és titkos kulcsokat a Linux-fürtökön titkosíthatja. A Windows-fürtök esetén lásd: [beállítása beállítása egy titkosítási tanúsítványt, és a Windows-fürtök a titkos kulcsok titkosítására][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Adatok titkosítási tanúsítvány beszerzése
Egy data titkosítási tanúsítvánnyal szigorúan szolgál az titkosítását és visszafejtését [paraméterek] [ parameters-link] a szolgáltatások Settings.xml és [környezeti változók] [ environment-variables-link] a ServiceManifest.xml a szolgáltatás. Akkor sem a hitelesítéshez használt vagy titkosított szöveget az aláírást. A tanúsítvány a következő követelményeknek kell megfelelnie:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A tanúsítvány kulcshasználati adattitkosítás (10) kell tartalmaznia, és nem tartalmazhat kiszolgálói hitelesítés vagy az ügyfél-hitelesítéssel.

  Például a következő parancsokat a szükséges tanúsítvány OpenSSL használatával létrehozásához használható:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Telepítse a tanúsítványt a fürtben
A tanúsítvány alatt a fürt minden csomópontjára telepíteni kell `/var/lib/sfcerts`. A felhasználói fiók, amely alatt a szolgáltatás fut (alapértelmezés szerint sfuser) **olvasási hozzáféréssel kell rendelkeznie** a telepített tanúsítványt (azaz `/var/lib/sfcerts/TestCert.pem` ebben a példában a).

## <a name="encrypt-secrets"></a>Titkosítani a titkos kulcsok
A következő kódrészlet is használható titkos kulcs titkosításához. Ez a kódrészlet csak titkosítja a értéket; ugyanúgy **nem** jelentkezzen a titkosított szöveget. **Használjon** a titkos értékek ciphertext előállításához a fürtben telepített ugyanolyan titkosítási tanúsítvánnyal.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Encrypted.txt a base-64 kódolású sztring a kimenet tartalmazza a titkos ciphertext és is a titkosításhoz használt tanúsítvány adatait. Visszafejti a openssl ellenőrizheti annak érvényességét.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [adja meg a titkosított titkos kulcsok egy alkalmazásban.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
