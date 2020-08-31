---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67448634"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy titkosított adatokat küldjön a megfelelő kiszolgálóra. A titkosítás mellett a tanúsítvány is lehetővé teszi a hitelesítést. A saját megbízható SSL-tanúsítványát az eszköz PowerShell-felületén keresztül töltheti fel.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A `Set-HcsCertificate` tanúsítvány feltöltéséhez használja a parancsmagot. Ha a rendszer kéri, adja meg a következő paramétereket:

   - `CertificateFilePath` – A tanúsítványfájl *. pfx* formátumú fájlját tartalmazó megosztás elérési útja.
   - `CertificatePassword` – A tanúsítvány védeleméhez használt jelszó.
   - `Credentials` -Username a tanúsítványt tartalmazó megosztás eléréséhez. Ha a rendszer kéri, adja meg a hálózati megosztás jelszavát.

     A következő példa a parancsmag használatát mutatja be:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

