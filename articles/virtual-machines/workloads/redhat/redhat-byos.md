---
title: Red Hat Enterprise Linux saját előfizetés-lemezképek | Microsoft Docs
description: Ismerkedjen meg a saját előfizetésekkel kapcsolatos rendszerképekkel az Azure-beli Red Hat Enterprise Linux
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486507"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux saját előfizetéses lemezképek az Azure-ban
A Red Hat Enterprise Linux-(RHEL-) lemezképek az Azure-ban az utólagos elszámolású (TB) vagy a saját előfizetéses (BYOS) modellen keresztül érhetők el az Azure-ban. Ez a dokumentum áttekintést nyújt a BYOS-lemezképekről az Azure-ban.

## <a name="important-points-to-consider"></a>Megfontolandó fontos szempontok

- A programban megadott RHEL BYOS-lemezképek az Azure Gallery/Marketplace RHEL TB-rendszerképeihez hasonló, éles használatra kész RHEL-lemezképek. A rendszerképek beszerzéséhez szükséges regisztrációs folyamat előzetes verzióban érhető el.

- A rendszerképek az Azure-ban [Red Hat Enterprise Linux rendszerképekben](./redhat-images.md) ismertetett aktuális szabályzatokat követik

- A rendszerképekből létrehozott virtuális gépekre a szabványos támogatási szabályzatok vonatkoznak

- A RHEL BYOS-lemezképből kiépített virtuális gépek nem teljesítik a RHEL TB-lemezképekhez kapcsolódó RHEL-díjakat

- A lemezképek nem jogosultak, ezért az előfizetés-kezelővel regisztrálnia kell a virtuális gépeket, és előfizethet a Red Hat közvetlen frissítéseinek letöltésére

- A Linux-lemezképek esetében jelenleg nem lehet dinamikusan váltani a BYOS és a TB számlázási modelljei között. A számlázási modell átváltásához a virtuális gépet a megfelelő rendszerképből kell újból üzembe helyezni

- A Azure Disk Encryption (ADE) a RHEL BYOS-lemezképek esetében támogatott. Az ADE-támogatás jelenleg előzetes verzióban érhető el. Az ADE konfigurálása előtt regisztrálnia kell a Red Hat használatával az előfizetés-kezelővel. A regisztrálást követően az ADE konfigurálásához lásd: [Azure Disk Encryption engedélyezése linuxos IaaS virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- Amíg a lemezképek nem változnak (a normál frissítések és javítások után), a regisztrációs folyamat előzetes verzióban érhető el, és a folyamat tovább fog javulni a folyamat egyszerűsítése érdekében.

- A lemezképből vagy a pillanatképekről már kiépített virtuális gépek teljes hozzáférése a végső megvalósítástól függetlenül

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>A RHEL BYOS-lemezképek elérésére vonatkozó követelmények és feltételek

1. Ismerje meg a [Red Hat Cloud Access program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) használati feltételeit, és regisztráljon a [Red Hat Cloud Access regisztrációs oldalán](https://access.redhat.com/cloude/manager/image_imports/new).

1. Fejezze be a [RHEL BYOS-hozzáférési kérelem űrlapját](https://aka.ms/rhel-byos). A RHEL BYOS-lemezképek és a használatával való eléréséhez be kell állítania a Red Hat-fiók számát, valamint az Azure-előfizetését.

1. A Red Hat és a Microsoft általi ellenőrzés és jóváhagyás után az Azure-előfizetés (ok) engedélyezve lesz a képhozzáféréshez.

### <a name="expected-time-for-image-access"></a>A rendszerkép-hozzáférés várt ideje

Ha elvégezte a RHEL BYOS űrlapot, és elfogadja a használati feltételeket, a Red Hat három munkanapon belül érvényesíti a BYOS-lemezképekre való jogosultságát, és ha érvényes, akkor a BYOS-lemezképeket egy adott napon belül megkapja.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>A RHEL BYOS-lemezképek használata az Azure Portalon

1. Miután az előfizetés engedélyezve lett a RHEL BYOS-lemezképek esetében, megkeresheti azt a [Azure Portal](https://portal.azure.com) **egy erőforrás létrehozásához** , majd az **összes**megjelenítése lehetőségre kattintva.

1. A lap tetején láthatja, hogy saját ajánlatokat tartalmaz.

    ![Piactér – privát ajánlatok](./media/rhel-byos-privateoffers.png)

1. A saját ajánlatok megjelenítéséhez kattintson a lila hivatkozásra, vagy görgessen le a lap aljára.

1. A felhasználói felületen a többi kiépítés nem lesz más, mint a többi meglévő Red Hat-rendszerkép. Válassza ki a RHEL verzióját, és kövesse az utasításokat a virtuális gép üzembe helyezéséhez. Ez a folyamat azt is lehetővé teszi, hogy elfogadja a rendszerkép feltételeit az utolsó lépésben.

>[!NOTE]
>Ezek a lépések nem teszik lehetővé a RHEL BYOS-rendszerképét a programozott üzembe helyezéshez – további lépésre lesz szükség az alábbi "További információ" című szakaszban leírtak szerint.

A dokumentum többi része a CLI metódusra összpontosít a feltételek kiépítéséhez és elfogadásához a képen. A felhasználói felület és a CLI teljes mértékben felcserélhető, ha a végeredmény (egy kiépített RHEL BYOS virtuális gép).

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>A RHEL BYOS-lemezképek használata az Azure CLI-vel
Az alábbi utasítások végigvezetik a RHEL virtuális gép kezdeti üzembe helyezési folyamatán az Azure CLI használatával. Ezek az utasítások feltételezik, hogy az [Azure CLI telepítve](https://docs.microsoft.com/cli/azure/install-azure-cli)van.

>[!IMPORTANT]
>Győződjön meg arról, hogy a közzétevő, az ajánlat, a terv és a Képhivatkozások összes kisbetűjét használja a következő parancsokhoz

1. Győződjön meg arról, hogy a kívánt előfizetése van:
    ```azurecli
    az account show -o=json
    ```

1. Hozzon létre egy erőforráscsoportot a RHEL BYOS virtuális géphez:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. A rendszerkép elfogadásának feltételei:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Ezeket a feltételeket csak egyszer kell elfogadni az *Azure-előfizetések, a RENDSZERKÉP SKU-száma*alapján.

1. Választható Ellenőrizze a virtuális gép telepítését a következő paranccsal:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Helyezze üzembe a virtuális gépet úgy, hogy ugyanazt a parancsot futtatja, mint a `--validate` argumentum nélkül:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH-t a virtuális gépre, és ellenőrizze, hogy van-e egy nem jogosult rendszerképe. Ehhez futtassa `sudo yum repolist`. A kimenet azt kéri, hogy az előfizetés-kezelővel regisztrálja a virtuális gépet a Red Hat használatával.

## <a name="additional-information"></a>További információk
- Ha olyan előfizetésre próbál virtuális gépet kiépíteni, amely nincs engedélyezve ehhez az ajánlathoz, a következő hibaüzenet jelenik meg, és a Microsoft vagy a Red Hat használatával kapcsolatba kell lépnie az előfizetés engedélyezésével.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Ha a RHEL BYOS rendszerképből hoz létre pillanatképet, és közzéteszi a rendszerképet a [megosztott rendszerkép](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)-katalógusban, meg kell adnia a pillanatkép eredeti forrásának megfelelő díjcsomag-információt. A parancs például a következőhöz hasonló lehet: (jegyezze fel a terv paramétereit az utolsó sorban):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Ha automatizálást használ a virtuális gépek RHEL BYOS-lemezképből való kiépítéséhez, meg kell adnia a fentebb láthatóhoz hasonló csomag-paramétereket. Ha például a Terraform-t használja, megadhatja a terv információit egy [csomag blokkban](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Következő lépések
* További információ az [Azure Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* Ha többet szeretne megtudni az Azure-beli Red Hat-lemezképekről, lépjen a [dokumentáció lapra](./redhat-images.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.