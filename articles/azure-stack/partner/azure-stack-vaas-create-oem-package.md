---
title: Az Azure Stack érvényesítési gyakorlati tanácsok |} A Microsoft Docs
description: Ez a cikk ismerteti az ajánlott eljárások a érvényesítési szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: fc2659fb9bbe043a61f1ad49bb4290b7ccf834f8
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422014"
---
# <a name="create-an-oem-package"></a>OEM-csomag létrehozása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack OEM-kiterjesztési csomag olyan mechanizmusok, mely OEM által meghatározott tartalom hozzáadódik Azure Stack-infrastruktúra üzembe helyezés, valamint a működési folyamatok például frissítése, bővítése és a mező helyettesítő használatra.

## <a name="creating-the-package"></a>A csomag létrehozása

Miután létrehozott és érvényesítve, az OEM-kiterjesztés csomagjának VaaS is használható.  A folytatás előtt győződjön meg arról, hogy végrehajtotta a lépéseket a [az OEM-csomag létrehozása](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). A csomag majd együtt VaaS terhelésiteszt-eredményei jelentkezik be a csomag érvényesítése munkafolyamat elküldése a Microsoftnak. A következő lépések bemutatják, hogyan szeretné a létrehozott fájlokat, amelyet felhasználhat VaaS egyetlen zip-fájlban.

1. Határozza meg a csomag a következő tartalommal:
    - Egy végrehajtható fájl neve `<Publisher>-<Model>-<Version>.exe`
    - Egy vagy több fájl nevű bináris fájlok `<Publisher><Model>-<Version>-#.bin`, ahol # egy 1-től induló egymást követő számot. Bináris fájlok száma érték a csomagtartalmat amelyek összméretén függ.
    - A jegyzékfájlt nevű `oemMetadata.xml`, amely meg kell egyezniük a tartalmat a gyökérkönyvtárában található a csomag tartalmát a metadata.xml fájlt.

2. Válassza ki azokat a tartalomfájlokat, és létrehozhat egy zip-fájl tartalma:

    ![Zip-fájl tartalmának](media/vaas-create-oem-package-1.png) ![elem tartalom tömörítése](media/vaas-create-oem-package-2.png)

3. Nevezze át az eredményül kapott fájl, úgy, hogy azonosítható legyen elég beszédes legyen.

## <a name="verifying-the-contents"></a>A tartalom ellenőrzése

A zip-fájl szerkezete ellenőrzéséhez azt vizsgálja, és ellenőrizheti, hogy vannak-e almappák nélkül. A TLD tartalmaz a ZIP tartalmát. Egy érvényes csomag szerkezete alább látható.
> [!IMPORTANT]
> A mappa tartalma helyett a becsomagolás sikertelen csomagaláíró miatt.

![Megfelelően tömörített csomag tartalma](media/vaas-create-oem-package-3.png)

A zip-fájl most VaaS feltöltött és a csomag érvényesítése munkafolyamat a Microsoft aláírásával.

## <a name="next-steps"></a>További lépések

- [Az OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md)
