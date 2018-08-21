---
title: Ellenőrizze a szoftverfrissítéseket a Microsoft Azure Stack érvényesítési szolgáltatásként |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti a szoftverfrissítéseket a Microsoft ellenőrzési szolgáltatásként.
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
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235404"
---
# <a name="validate-software-updates-from-microsoft"></a>A Microsoft szoftverfrissítések ellenőrzése

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeresen frissítéseket az Azure Stack szoftver. Ezek a frissítések állnak rendelkezésre az Azure Stack közös mérnöki partnerek előtt kerül sor nyilvánosan elérhető, így azok elleni megoldásaikat a frissítések ellenőrzése és visszajelzés küldése a Microsoftnak.

## <a name="test-an-existing-solution"></a>Egy meglévő megoldás tesztelése

1. Jelentkezzen be a [érvényesítési portál](https://azurestackvalidation.com).

2. Válasszon egy meglévő megoldás, ahol a Microsoft a frissített már telepítve van és válassza ki **Start** a a **csomag** csempére.

    ![Csomag érvényességének ellenőrzése](media/image3.png)

3. Adja meg az érvényesítési nevét.

4. Adja meg az URL-címet az OEM-csomagot, amely a megoldás központi telepítéskor lett telepítve. Az URL-címet használja a csomag az Azure blob szolgáltatásban tárolja. További információkért lásd: [hozzon létre egy Azure storage-blobba naplók tárolására](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

5. Válassza ki **feltöltése** a KözpontiTelepítés-konfigurációs fájl hozzáadásához. Tekintse meg a [ellenőrzése egy új Azure Stack megoldás](azure-stack-vaas-validate-solution-new.md) információ a központi telepítési konfigurációs fájl feltöltése.

6. A telepítési konfigurációs fájlt kell szabhatók testre a megfelelő környezetben paramétereket tartalmazó fájlt, tekintse meg [környezet paraméterek](azure-stack-vaas-parameters.md#environment-parameters) további részleteket.

    > [!Note]   
    > A telepítési konfigurációs fájlt általános tesztelési paraméterek hozzáadásával további testre szabható. További információkért lásd: [szolgáltatásként az Azure Stack érvényesítési az általános munkafolyamat-paraméterek](azure-stack-vaas-parameters.md)

7. A felhasználónév és a bérlő felhasználói, szolgáltatás-rendszergazda és felhőalapú rendszergazdai jelszavát manuálisan kell megadni.

8. Adja meg a diagnosztikai naplók tárolásához az Azure Storage blob URL-címe. További információkért lásd: [hozzon létre egy Azure storage-blobba naplók tárolására](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    > [!Note]  
    > Leíró címkék címkével ellátni a munkafolyamat adható meg.

10. Válassza ki **küldés** a munkafolyamat mentéséhez.

A megoldás a munkafolyamat fut körülbelül 24 órán keresztül. Adja hozzá a egy hivatkozást vagy utasítás a tesztek ütemezése. Törölje az eszközben.

Futtassa egy érvényesítési állapotának figyeléséről további információt talál, lásd: [figyelése egy teszt ](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
