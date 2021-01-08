---
title: Cserélje le az Azure Stack Edge Pro-eszközét | Microsoft Docs
description: Ismerteti, hogyan lehet helyettesítő Azure Stack Edge Pro-eszközt beolvasni.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 501136c6f5015dc8c68a1aba004432900675b11c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017969"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro-eszköz cseréje

Ez a cikk azt ismerteti, hogyan kérhető le egy helyettesítő Azure Stack Edge Pro-eszköz. Szükség van egy helyettesítő eszközre, ha a meglévő eszköz hardveres hibával rendelkezik, vagy frissítésre van szüksége. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Támogatási jegy megnyitása a hardveres probléma megoldásához
> * Új erőforrás létrehozása a helyettesítő eszközhöz Azure Portal
> * Telepítse, aktiválja a helyettesítő eszközt
> * Az eredeti eszköz visszaadása

## <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása

Ha a meglévő eszköz hardveres hibával rendelkezik, nyisson meg egy támogatási jegyet. A Microsoft ügyfélszolgálata megállapítja, hogy ehhez a példányhoz nem érhető el helyszíni cseregység, vagy az eszköznek hardveres frissítésre van szüksége. Mindkét esetben a támogatás egy helyettesítő eszközt fog rendelni.

1. Nyisson meg egy támogatási jegyet Microsoft ügyfélszolgálata jelezve, hogy vissza kívánja adni az eszközt. Válassza ki a probléma típusát **Azure stack Edge Pro hardverként**.

    ![Támogatási jegy megnyitása](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Egy Microsoft ügyfélszolgálata mérnök felveszi Önnel A kapcsolatot. Adja meg a szállítási adatokat.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Erőforrás létrehozása a helyettesítő eszközhöz

> [!NOTE]
> Hozzon létre egy új erőforrást a helyettesítő eszköz aktiválásához. A helyettesítő eszköz meglévő erőforrással való aktiválása nem támogatott.

Egy erőforrás létrehozásához kövesse az alábbi lépéseket.

1. Kövesse az [új erőforrás létrehozása](azure-stack-edge-deploy-prep.md#create-a-new-resource) című témakör lépéseit, és hozzon létre egy erőforrást a helyettesítő eszközhöz. 

2. Ügyeljen arra, hogy jelölje be a jelölőnégyzetet a **Azure stack Edge Pro-eszközön**. 

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

- Megtudhatja, hogyan adhat [vissza Azure stack Edge Pro-eszközt](azure-stack-edge-return-device.md).
