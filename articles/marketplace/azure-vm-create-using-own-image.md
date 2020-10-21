---
title: Azure-beli virtuális gépek ajánlatának létrehozása az Azure Marketplace-en saját rendszerkép használatával
description: Ismerje meg, hogyan tehet közzé egy virtuálisgép-ajánlatot az Azure Marketplace-en saját rendszerkép használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284305"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Virtuális gép létrehozása saját rendszerkép használatával

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy felhasználó által biztosított virtuális gépet (VM) tartalmazó lemezképet.

> [!NOTE]
> Az eljárás megkezdése előtt tekintse át az Azure-beli virtuális gépekre vonatkozó [technikai követelményeket](marketplace-virtual-machines.md#technical-requirements) , beleértve a virtuális merevlemez (VHD) követelményeit.

Ha ehelyett egy jóváhagyott alaprendszerképet szeretne használni, kövesse a virtuálisgép- [rendszerkép létrehozása jóváhagyott alapból](azure-vm-create-using-approved-base.md)című témakör utasításait.

## <a name="configure-the-vm"></a>A virtuális gép konfigurálása

Ez a szakasz azt ismerteti, hogyan lehet méretezni, frissíteni és általánosítani egy Azure-beli virtuális gépet. Ezek a lépések szükségesek ahhoz, hogy előkészítse a virtuális gépet az Azure Marketplace-en való üzembe helyezéshez.

### <a name="size-the-vhds"></a>A virtuális merevlemezek mérete

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>A virtuális merevlemez feltöltése az Azure-ba

Konfigurálja és készítse elő a virtuális gépet, amelyet a [Windows VHD vagy VHDX előkészítése az Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) -ba való feltöltéshez, illetve [linuxos virtuális merevlemez létrehozásához és feltöltéséhez](../virtual-machines/linux/create-upload-generic.md)című cikkben ismertetett módon kell feltölteni.

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>A virtuális merevlemez kibontása rendszerképből (rendszerkép-építési szolgáltatások használata esetén)

Ha rendszerkép-építési szolgáltatást (például [csomagolót](https://www.packer.io/)) használ, előfordulhat, hogy ki kell bontania a virtuális merevlemezt a rendszerképből. Erre nincs közvetlen mód. Létre kell hoznia egy virtuális gépet, és ki kell bontania a virtuális merevlemezt a virtuálisgép-lemezről.

### <a name="create-the-vm-on-the-azure-portal"></a>A virtuális gép létrehozása a Azure Portal

Az alábbi lépéseket követve hozza létre az alapszintű VM-rendszerképet a [Azure Portal](https://ms.portal.azure.com/).

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).
2. Válassza a **Virtuális gépek** lehetőséget.
3. Válassza a **+ Hozzáadás** lehetőséget a **virtuális gép létrehozása** képernyő megnyitásához.
4. Válassza ki a lemezképet a legördülő listából, vagy válassza az **összes nyilvános és privát rendszerkép tallózása** lehetőséget a rendelkezésre álló virtuálisgép-lemezképek kereséséhez vagy tallózásához.
5. A 2. **generációs** virtuális gépek létrehozásához nyissa meg a **speciális** lapot, és válassza a **2. generációs** lehetőséget.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Válassza az 1. gen vagy a 2. lehetőséget.":::

6. Válassza ki a telepítendő virtuális gép méretét.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Válassza az 1. gen vagy a 2. lehetőséget.":::

7. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
8. Válassza a **felülvizsgálat + létrehozás** lehetőséget a választási lehetőségek áttekintéséhez. Amikor megjelenik az **érvényesítési** üzenet, válassza a **Létrehozás**lehetőséget.

Az Azure megkezdi a megadott virtuális gép üzembe helyezését. Az előrehaladás nyomon követéséhez válassza a bal oldali menü **Virtual Machines** lapját. Miután létrehozta a virtuális gép változásait a **futtatásra**.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

A Windows vagy [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) [rendszerű](../virtual-machines/windows/connect-logon.md) virtuális géphez való kapcsolódáshoz tekintse meg az alábbi dokumentációt.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Következő lépések

- Javasolt következő lépés: [a virtuálisgép-rendszerkép tesztelése](azure-vm-image-test.md) , hogy az megfeleljen az Azure Marketplace közzétételi követelményeinek. Ez nem kötelező.
- Ha nem teszteli a virtuálisgép-rendszerképet, folytassa [a sas URI előállításával](azure-vm-get-sas-uri.md).
- Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg [Az Azure Marketplace-hez készült virtuális gépekkel kapcsolatos gyakori kérdéseket](azure-vm-create-faq.md).
