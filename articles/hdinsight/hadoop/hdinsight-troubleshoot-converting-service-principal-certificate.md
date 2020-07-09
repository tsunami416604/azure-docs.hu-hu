---
title: A tanúsítvány tartalmának konvertálása Base-64-Azure HDInsight
description: Egyszerű tanúsítvány tartalmának konvertálása Base-64 kódolású karakterlánc-formátumra az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894169"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Egyszerű tanúsítvány tartalmának konvertálása Base-64 kódolású karakterlánc-formátumra a HDInsight-ben

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Hibaüzenet jelenik meg, amely szerint a bemenet nem érvényes Base-64 karakterlánc, mert nem Base 64 karaktert, több mint két kitöltési karaktert vagy egy nem fehér szóköz karaktert tartalmaz a kitöltési karakterek között.

## <a name="cause"></a>Ok

Ha a PowerShell vagy az Azure template üzembe helyezésével fürtöket hoz létre Data Lake elsődlegesként vagy további tárolóként, akkor az Data Lake Storage-fiók eléréséhez megadott egyszerű szolgáltatás tanúsítványa a Base-64 formátumban van. A pfx-tanúsítvány tartalmának a Base-64 kódolású sztringre való helytelen átalakítása ehhez a hibához vezethet.

## <a name="resolution"></a>Megoldás:

Ha az egyszerű szolgáltatás tanúsítványa pfx formátumban van (lásd [itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) az egyszerű minta-létrehozási lépéseknél), használja a következő PowerShell-parancsot vagy C#-kódrészletet a tanúsítvány tartalmának base-64 formátumra való átalakításához.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
