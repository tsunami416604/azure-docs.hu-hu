---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125002"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy a megfelelő kiszolgálóhoz titkosított adatok küld. Amellett, titkosítás a tanúsítvány lehetővé teszi a hitelesítéshez. Feltöltheti saját megbízható SSL-tanúsítványt az eszköz a PowerShell-felületen keresztül.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Használja a `Set-HcsCertificate` parancsmaggal töltse fel a tanúsítványt. Amikor a rendszer kéri, adja meg a következő paraméterekkel:

   - `CertificateFilePath` -A megosztáshoz, amely tartalmazza a tanúsítványfájlt, a path *.pfx* formátumban.
   - `CertificatePassword` -A tanúsítvány védelméhez használt jelszó.
   - `Credentials` -Felhasználónevet és jelszót, amely tartalmazza a tanúsítványt a megosztás eléréséhez.

     Az alábbi példa bemutatja ennek a parancsmagnak a használatát:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

