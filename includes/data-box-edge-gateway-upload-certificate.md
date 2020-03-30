---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448634"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy titkosított adatokat küld a megfelelő kiszolgálóra. A titkosítás mellett a tanúsítvány lehetővé teszi a hitelesítést is. Feltöltheti saját megbízható SSL-tanúsítványát az eszköz PowerShell-felületén keresztül.

1. [Csatlakozzon a PowerShell-kapcsolathoz.](#connect-to-the-powershell-interface)
2. A `Set-HcsCertificate` parancsmag segítségével töltse fel a tanúsítványt. Amikor a rendszer kéri, adja meg a következő paramétereket:

   - `CertificateFilePath`- A tanúsítványfájlt tartalmazó megosztás elérési útja *.pfx* formátumban.
   - `CertificatePassword`- A tanúsítvány védelmére használt jelszó.
   - `Credentials`- Felhasználónév a tanúsítványt tartalmazó megosztás eléréséhez. Amikor a rendszer kéri, adja meg a jelszót a hálózati megosztáshoz.

     A következő példa a parancsmag használatát mutatja be:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

