---
title: Az Azure Stack Edge-eszköz cseréje | Microsoft Docs
description: Ismerteti, hogyan lehet helyettesítő Azure Stack Edge-eszközt beolvasni.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098752"
---
# <a name="replace-your-azure-stack-edge-device"></a>Az Azure Stack Edge-eszköz cseréje

Ez a cikk egy helyettesítő Azure Stack Edge-eszköz beszerzését ismerteti. Szükség van egy helyettesítő eszközre, ha a meglévő eszköz hardveres hibával rendelkezik, vagy frissítésre van szüksége. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Támogatási jegy megnyitása a hardveres probléma megoldásához
> * Új erőforrás létrehozása a helyettesítő eszközhöz Azure Portal
> * Telepítse, aktiválja a helyettesítő eszközt
> * Az eredeti eszköz visszaadása

## <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása

Ha a meglévő eszköz hardveres hibával rendelkezik, nyisson meg egy támogatási jegyet. Microsoft ügyfélszolgálata megállapítja, hogy ehhez a példányhoz nem érhető el egy mező típusú helyettesítő egység, vagy az eszköznek hardveres frissítésre van szüksége. Mindkét esetben a támogatás egy helyettesítő eszközt fog rendelni.

1. Nyisson meg egy támogatási jegyet Microsoft ügyfélszolgálata jelezve, hogy vissza kívánja adni az eszközt. Válassza ki a probléma típusát **Azure stack Edge hardverként**.

    ![Támogatási jegy megnyitása](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Egy Microsoft ügyfélszolgálata mérnök felveszi Önnel A kapcsolatot. Adja meg a szállítási adatokat.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Erőforrás létrehozása a helyettesítő eszközhöz

Egy erőforrás létrehozásához kövesse az alábbi lépéseket.

1. Kövesse az [új erőforrás létrehozása](azure-stack-edge-deploy-prep.md#create-a-new-resource) című témakör lépéseit, és hozzon létre egy erőforrást a helyettesítő eszközhöz. 

2. Ügyeljen arra, hogy jelölje be a jelölőnégyzetet a **Azure stack Edge-eszközön**. 

    ![Erőforrás a helyettesítő eszközhöz](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>A helyettesítő eszköz telepítése és aktiválása

A következő lépésekkel telepítheti és aktiválhatja a helyettesítő eszközt:

1. [Telepítse az eszközt](azure-stack-edge-deploy-install.md).

2. [Aktiválja az eszközt](azure-stack-edge-deploy-connect-setup-activate.md) a korábban létrehozott új erőforrással.

## <a name="return-your-existing-device"></a>Meglévő eszköz visszaküldése

Az eredeti eszköz visszaküldéséhez kövesse az összes lépést:

1. [Az eszközön lévő adat törlése](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. Az [eszköz visszaküldésének kezdeményezése](azure-stack-edge-return-device.md#initiate-device-return) az eredeti eszközhöz.
3. [Pickup beosztása](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Törölje a](azure-stack-edge-return-device.md#delete-the-resource) visszaadott eszközhöz hozzárendelt erőforrást.


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan adhat [vissza Azure stack Edge-eszközt](azure-stack-edge-return-device.md).
