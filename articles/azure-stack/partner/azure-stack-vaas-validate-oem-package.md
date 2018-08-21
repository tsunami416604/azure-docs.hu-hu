---
title: Számítógépgyártó (OEM) csomagok szolgáltatásként az Azure Stack érvényesítési ellenőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan ellenőrizheti a számítógépgyártó (OEM) csomagok-érvényesítéssel szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235369"
---
# <a name="validate-oem-packages"></a>OEM-csomagok érvényesítése

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ha a belső vezérlőprogram vagy illesztőprogramok egy befejezett megoldás érvényesítéshez változás történt tesztelheti egy új OEM-csomagot. Ha a csomag megfelelt a vizsgálatot, azt a Microsoft által aláírt. A tesztet tartalmaznia kell a frissített illesztőprogramokat és belső vezérlőprogramja, amelyek megfeleltek a Windows Server embléma és a számítógépek tesztek OEM bővítmény csomagot.

Az összes teszt befejezése 24 órán belül eredménye **sikeres**. Ha bármelyik teszt eredménye **sikertelen**, a hiba jelentése [Microsoft Collaborate](https://aka.ms/collaborate) és az e-mail értesíti a Microsoftot [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Az aláírt OEM csomag beszerzése

1. Győződjön meg arról, hogy az aktuális havi frissítés telepítve van-e. A legújabb verzióját, lásd: a legújabb verziót [Azure Stack operátori dokumentációja > Áttekintés > kibocsátási megjegyzések](https://docs.microsoft.com/azure/azure-stack/) .

    Szoftverfrissítéseket a Microsoft Azure Stack használatával a elnevezési jelölik, például 1803 jelzi a frissítést a 2018 március. Az Azure Stack-frissítési szabályzat kapcsolatban-váltás gyakoriságáról és a kibocsátási megjegyzések érhető el, lásd: [karbantartási szabályzat az Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Futtatásával ellenőrizze a rendszer állapot **Test-AzureStack** szerint ismertetett [teszt. Futtassa az Azure Stackhez. Javítsa ki a figyelmeztetések és hibák bármilyen vizsgálatok elindítása előtt.

2. Az a [érvényesítési portál](https://azurestackvalidation.com), válasszon ki egy meglévő megoldást. Ha nem adta hozzá a megoldáshoz, [adjon hozzá egy új megoldás](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Válassza ki **Start** a a **csomag ellenőrzések** csempe egy új munkafolyamat elindításához.

    ![Csomag ellenőrzések](media/image3.png)

4.  Adjon meg egy diagnosztikai kapcsolati karakterláncot. Útmutatásért lásd: [állítsa be a tárfiók](azure-stack-vaas-set-up-account.md).

    Az OEM-kiterjesztési csomag futtatása minden csomag-ellenőrzés céljából meg kell adni. Adja meg az OEM-csomagot, amely a megoldás az Azure Stack üzemelő példányához időpontjában volt telepítve. Útmutatásért lásd: [hozzon létre egy Azure storage-blobba naplók tárolására](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    Egy JSON-fájlt a környezeti változók használatával kell használni a bemeneti kötelező mezők adatbevitel a hibák elkerülése érdekében a Futtatás gombra. Útmutatásért lásd: [egy konfigurációs fájlt az Azure Stack központi telepítésének első](azure-stack-vaas-parameters.md).

5. A tesztek futtatásához.

6. Az összes teszt sikeresen befejeződött, a neve, a megoldás és a csomag érvényesítése küldése [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) lekérni a csomag aláírása.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
