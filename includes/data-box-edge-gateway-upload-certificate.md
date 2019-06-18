---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161214"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy a megfelelő kiszolgálóhoz titkosított adatok küld. Amellett, titkosítás a tanúsítvány lehetővé teszi a hitelesítéshez. Feltöltheti saját megbízható SSL-tanúsítványt az eszköz a PowerShell-felületen keresztül.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Használja a `Set-HcsCertificate` parancsmaggal töltse fel a tanúsítványt. Amikor a rendszer kéri, adja meg a következő paraméterekkel:

   - `CertificateFilePath` -A megosztáshoz, amely tartalmazza a tanúsítványfájlt, a path *.pfx* formátumban.
   - `CertificatePassword` -A tanúsítvány védelméhez használt jelszó.
   - `Credentials` -Felhasználónevet és jelszót, amely tartalmazza a tanúsítványt a megosztás eléréséhez.

     Az alábbi példa bemutatja ennek a parancsmagnak a használatát:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

