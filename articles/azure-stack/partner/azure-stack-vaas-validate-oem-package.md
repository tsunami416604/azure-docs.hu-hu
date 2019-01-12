---
title: Az Azure Stack érvényesítési szolgáltatásként számítógépgyártó (OEM)-csomagok érvényesítése |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti az eredeti hardvergyártó (OEM) csomagok-érvényesítéssel szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5ba290f442f4c27b510538d7c1f7b5e27467efc5
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246653"
---
# <a name="validate-oem-packages"></a>OEM-csomagok érvényesítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ha a belső vezérlőprogram vagy illesztőprogramok egy befejezett megoldás érvényesítéshez változás történt tesztelheti egy új OEM-csomagot. Ha a csomag megfelelt a vizsgálatot, azt a Microsoft által aláírt. A tesztet tartalmaznia kell a frissített illesztőprogramokat és belső vezérlőprogramja, amelyek megfeleltek a Windows Server embléma és a számítógépek tesztek OEM bővítmény csomagot.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Feltöltése vagy a csomagok elküldése előtt tekintse át a [OEM csomag létrehozása](azure-stack-vaas-create-oem-package.md) a várt csomag formátum és a tartalom.

## <a name="managing-packages-for-validation"></a>Ellenőrzés-csomagok kezelése

Használatakor a **csomag** munkafolyamat érvényesítése a csomag, meg kell adnia az URL-címe egy **Azure Storage-blobot**. A blob az OEM-csomagot, amely a megoldás központi telepítéskor lett telepítve. A telepítés során létrehozott Azure Storage-fiók használata a blob létrehozásához (lásd: [állítsa be a szolgáltatási erőforrások, az érvényesítési](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Előfeltétel: A storage-tároló üzembe helyezése

Hozzon létre egy tárolót a storage-fiókban csomag blobok számára. Ez a tároló használható összes futtatja a csomag érvényességének ellenőrzése.

1. Az a [az Azure Portal](https://portal.azure.com), lépjen a létrehozott tárfiókban [állítsa be a szolgáltatási erőforrások, az érvényesítési](azure-stack-vaas-set-up-resources.md).
2. A bal oldali panel alatt **Blob Service**, jelölje be a **tárolók**.
3. Válassza ki **+ tároló** menüjében sávot, és adja meg például a tároló nevét `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Csomag feltöltése a storage-fiókba

1. Készítse elő az érvényesíteni kívánt csomagot. Ha a csomag több fájlt, a tömörítés be egy `.zip` fájlt.
2. Az a [az Azure Portal](https://portal.azure.com), válassza ki a csomag tárolót, és töltse fel a csomagot, válassza a **feltöltése** a menüsávon.
3. Válassza ki a csomagot `.zip` fájlt a feltöltéshez. Hagyja változatlanul az alapértelmezett beállításokat **Blob típusát** (azaz a **Blokkblob**) és **blokkméret**.

> [!NOTE]
> Győződjön meg arról, hogy a `.zip` tartalma gyökérmappájában kerülnek a `.zip` fájlt. A csomag nincs almappák kell lennie.

### <a name="generate-package-blob-url-for-vaas"></a>Csomag blob URL-címet generálni VaaS

Amikor létrehozza a **csomag** munkafolyamat a VaaS portálon, meg kell adnia a csomagot tartalmazó Azure Storage blob URL-címe.

#### <a name="option-1-generating-an-account-sas-url"></a>1. lehetőség: Egy fiók SAS URL-cím generálása

1. Az a [az Azure portal](https://portal.azure.com/)nyissa meg a tárfiók, és keresse meg a .zip a csomagot tartalmazó

2. Válassza ki **SAS létrehozása** a helyi menüből

3. Válassza ki **olvasási** a **engedélyek**

4. Állítsa be **kezdési idő** az aktuális idő és **befejezési idő** legalább 48 órát a **kezdési idő**. Ha a rendszer más teszteket ugyanaz a csomag kell futtatja, érdemes növelni **befejezési idő** hosszán át a tesztelést. Bármely keresztül után VaaS ütemezett vizsgálatok **befejezési idő** sikertelen és a egy új SAS fog kell létrehozni.

5. Válassza ki **készítése a blob SAS-jogkivonat és URL-címe**

Használja **a Blob SAS URL-cím** mikor indítása egy új **csomag** munkafolyamat a VaaS portálon.

#### <a name="option-2-using-public-read-container"></a>2. lehetőség: Nyilvános olvasási tároló használatával

> [!CAUTION]
> Ezzel a beállítással megnyílik a névtelen olvasási hozzáférés a tároló.

1. Engedélyezés **nyilvános olvasási hozzáférés csak blobok** a csomag tárolóhoz az részben található útmutatást követve [tárolókhoz és blobokhoz való névtelen felhasználók engedélyek](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. A csomag tárolóban válassza ki a csomag BLOB tároló megnyitása a Tulajdonságok panelen.

3. Másolás a **URL-cím**. Használja ezt az értéket, egy új indításakor **csomag** munkafolyamat a VaaS portálon.

## <a name="apply-monthly-update"></a>Havi frissítés alkalmazása

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Csomag érvényesítése munkafolyamat létrehozása

1. Jelentkezzen be a [VaaS portál](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Válassza ki **Start** a a **csomag** csempére.

    ![Csomag ellenőrzések munkafolyamat csempe](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Adja meg az Azure Storage blob URL-címet az OEM-csomagot, amely a megoldás központi telepítéskor lett telepítve. Útmutatásért lásd: [csomag blob URL-címet generálni VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Környezeti paraméter nem lehet módosítani egy munkafolyamat létrehozása után.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A tesztek összefoglaló oldalra irányítja.

## <a name="run-package-validation-tests"></a>Csomag Érvényesítési tesztek futtatásához

1. Az a **csomag Érvényesítési tesztek összefoglalása** lapon látni fogja a szükséges érvényesítési teszteket listáját. A munkafolyamat-futtatási körülbelül 24 órán keresztül tesztjét.

    Az érvényesítés munkafolyamatokban **ütemezés** egy tesztet a munkafolyamat-szintű gyakori paramétereket, a munkafolyamat létrehozása során megadott használja (lásd: [az Azure Stack érvényesítésiszolgáltatásáltalánosmunkafolyamat-paraméterek](azure-stack-vaas-parameters.md)). Ha bármelyik teszt paraméterértékek érvénytelenné válik, meg kell resupply őket a utasításai [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Teszt ütemezés egy meglévő példányát keresztül fog létrehozni a régi példány helyett egy új példányt a portálon. A régi példány naplók megőrzi a rendszer, de nem érhetők el a portálon.  
    Egy teszt sikeres végrehajtása után a **ütemezés** művelet le lesz tiltva.

2. Válassza ki az ügynök, amely a teszt fog futni. Információ hozzáadása helyi végrehajtási tesztügynökök, lásd: [üzembe helyezése a helyi ügynök](azure-stack-vaas-local-agent.md).

3. A következő ellenőrzés mindegyike esetében lépés négy és az öt:
    - OEM-bővítmény csomag ellenőrzése
    - Felhőbeli szimuláció motor

4. Válassza ki **ütemezés** nyissa meg a tevékenységütemezést a test-példány ehhez a helyi menüből.

5. Tekintse át a teszt paramétereket, majd **küldés** ütemezni a teszt végrehajtásához.

Az összes teszt sikeresen befejeződött, küldése nevét a VaaS megoldás és a csomag érvényességi [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) lekérni a csomag aláírása.

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)