---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448634"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy a megfelelő kiszolgálóhoz titkosított adatok küld. Amellett, titkosítás a tanúsítvány lehetővé teszi a hitelesítéshez. Feltöltheti saját megbízható SSL-tanúsítványt az eszköz a PowerShell-felületen keresztül.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Használja a `Set-HcsCertificate` parancsmaggal töltse fel a tanúsítványt. Amikor a rendszer kéri, adja meg a következő paraméterekkel:

   - `CertificateFilePath` -A megosztáshoz, amely tartalmazza a tanúsítványfájlt, a path *.pfx* formátumban.
   - `CertificatePassword` -A tanúsítvány védelméhez használt jelszó.
   - `Credentials` -A tanúsítványt tartalmazó megosztásra hozzáféréséhez használt felhasználónév. Adja meg a hálózati megosztást, amikor a rendszer kéri a jelszót.

     Az alábbi példa bemutatja ennek a parancsmagnak a használatát:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

