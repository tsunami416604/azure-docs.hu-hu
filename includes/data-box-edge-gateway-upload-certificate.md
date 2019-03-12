---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c689dc4f7e0957218d6504532c4b481d7673def8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555146"
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

