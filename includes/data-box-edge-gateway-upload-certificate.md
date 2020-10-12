---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67448634"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy a titkosított adatokat a megfelelő kiszolgálóra küldi. A titkosítás mellett a tanúsítvány a hitelesítésre is lehetőséget biztosít. A saját megbízható SSL-tanúsítványát az eszköz PowerShell-felületén keresztül töltheti fel.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A `Set-HcsCertificate` tanúsítvány feltöltéséhez használja a parancsmagot. Ha a rendszer kéri, adja meg a következő paramétereket:

   - `CertificateFilePath` – A tanúsítványfájl *. pfx* formátumú fájlját tartalmazó megosztás elérési útja.
   - `CertificatePassword` – A tanúsítvány védeleméhez használt jelszó.
   - `Credentials` -Username a tanúsítványt tartalmazó megosztás eléréséhez. Kérés esetén adja meg a hálózati megosztás jelszavát.

     A következő példa a parancsmag használatát mutatja be:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

