---
title: Tanúsítványtartalom konvertálása base-64-re – Azure HDInsight
description: Egyszerű szolgáltatástanúsítvány-tartalom konvertálása base-64 kódolású karakterlánc-formátumra az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894169"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Egyszerű szolgáltatástanúsítvány-tartalom konvertálása base-64 kódolású karakterlánc-formátumra a HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Hibaüzenet jelenik meg arról, hogy a bemenet nem érvényes Base-64 karakterlánc, mivel nem alap64 karaktert, kettőnél több kitöltési karaktert vagy a kitöltési karakterek között nem fehér szóközt tartalmaz.

## <a name="cause"></a>Ok

PowerShell vagy Azure-sablon központi telepítése a Data Lake elsődleges vagy további tárolóként a Data Lake elsődleges vagy további tárolóként létrehozott fürtök létrehozása, a Data Lake storage-fiók eléréséhez biztosított egyszerű szolgáltatásnév-tartalom alap-64 formátumban van. A pfx tanúsítvány tartalmának nem megfelelő átalakítása base-64 kódolású karakterláncra vezethet.

## <a name="resolution"></a>Megoldás:

Miután rendelkezik a szolgáltatás egyszerű tanúsítvány pfx formátumban [(lásd itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) a minta szolgáltatás egyszerű létrehozása lépéseket), használja a következő PowerShell parancs vagy C# kódrészlet konvertálni a tanúsítvány tartalmát base-64 formátumban.

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

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
