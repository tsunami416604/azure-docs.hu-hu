---
title: Titkosítási tanúsítvány beállítása Linux-fürtökön
description: Ismerje meg, hogyan állíthat be egy titkosítási tanúsítványt, és titkos kulcsoktitkosításlinuxos fürtökön.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969032"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása Linux-fürtökön
Ez a cikk bemutatja, hogyan állíthat be egy titkosítási tanúsítványt, és használja a linuxos fürtök titkos kulcsainak titkosítására. Windows-fürtök esetén olvassa el A [titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása Windows-fürtökön című témakört.][secret-management-windows-specific-link]

## <a name="obtain-a-data-encipherment-certificate"></a>Adattitkosítási tanúsítvány beszerzése
Az adattitkosítási tanúsítvány szigorúan a szolgáltatás Settings.xml és [a][environment-variables-link] service ServiceManifest.xml fájlban lévő [paraméterek][parameters-link] titkosítására és visszafejtésére szolgál. Nem használható a titkosítási szöveg hitelesítésére vagy aláírására. A bizonyítványnak a következő követelményeknek kell megfelelnie:

* A tanúsítványnak titkos kulcsot kell tartalmaznia.
* A tanúsítványkulcs használatának tartalmaznia kell az adattitkosítást (10), és nem tartalmazhat kiszolgálói hitelesítést vagy ügyfélhitelesítést.

  A következő parancsok például a szükséges tanúsítvány openSSL használatával történő létrehozásához használhatók:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>A tanúsítvány telepítése a fürtben
A tanúsítványt a fürt minden csomópontjára `/var/lib/sfcerts`telepíteni kell a területen. Annak a felhasználói fióknak, `/var/lib/sfcerts/TestCert.pem` amely alatt a szolgáltatás fut (alapértelmezés szerint sfuser) **olvasási hozzáféréssel kell rendelkeznie** a telepített tanúsítványhoz (azaz az aktuális példában).

## <a name="encrypt-secrets"></a>Titkos kulcsok titkosítása
A következő kódrészlet egy titkos titok titkosítására használható. Ez a kódrészlet csak az értéket titkosítja; **nem** írja alá a rejtjel szöveget. **A** titkos értékek titkosítási szövegének létrehozásához ugyanazt a titkosítási tanúsítványt kell használnia, amely a fürtben van telepítve.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Az eredményül kapott base-64 kódolású karakterlánc-kimenet a titkosított.txt fájlba tartalmazza a titkos titkosítást, valamint a titkosításhoz használt tanúsítványadatait. Érvényességét az OpenSSL segítségével visszafejtheti.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [adhat meg titkosított titkos kulcsokat egy alkalmazásban.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
