---
title: Parancsfájl a Kdump engedélyezéséhez SAP HANAban (nagyméretű példányok) | Microsoft Docs
description: Parancsfájl a Kdump engedélyezéséhez SAP HANA (nagyméretű példányok) HLI típusú I, HLI Type II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204935"
---
# <a name="enable-kdump-service"></a>Kdump szolgáltatás engedélyezése

Ez a dokumentum ismerteti a Kdump szolgáltatás Azure HANA nagyméretű példányon való engedélyezésének részleteit (**i és II**típus)

## <a name="supported-skus"></a>Támogatott SKU-i

|  Hana nagyméretű példány típusa   |  Operációs rendszer szállítója   |  Operációs rendszer csomagjának verziója   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Típus                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Típus                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Típus                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   II. típus                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   II. típus                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   II. típus                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   II. típus                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Előfeltételek

- A Kdump szolgáltatás `/var/crash` címtárat használ a memóriaképek írásához, győződjön meg arról, hogy a partíciónak megfelelő hely van a memóriaképek elhelyezéséhez.

## <a name="setup-details"></a>Telepítés részletei

- A Kdump engedélyezéséhez szükséges parancsfájl [itt](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh) található.

- Futtassa ezt a parancsfájlt a HANA Large-példányon az alábbi parancs használatával

    > [!NOTE]
    > a parancs futtatásához sudo jogosultság szükséges.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Ha a parancs kimenete Kdump sikeresen engedélyezve van, indítsa újra a rendszerét a módosítás alkalmazásához, majd a Kdump engedélyezése sikeresen megtörtént. A módosítások alkalmazásához indítsa újra a rendszerét.

- Ha a parancs kimenete nem tudott végrehajtani bizonyos műveletet, zárja be!!!!,, majd a Kdump szolgáltatás nincs engedélyezve. Tekintse át a [támogatási problémát](#support-issue)ismertető szakaszt.

## <a name="test-kdump"></a>Kdump tesztelése

> [!NOTE]
>  Az alábbi művelet elindítja a kernel összeomlását és a rendszer újraindítását.

- Kernel összeomlásának elindítása

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- A rendszer sikeres újraindítása után keresse meg a `/var/crash` kernel-összeomlási naplók mappáját.

- Ha a `/var/crash` címtára aktuális dátummal rendelkezik, akkor a Kdump engedélyezése sikeresen megtörtént.

## <a name="support-issue"></a>Támogatási probléma

Ha a parancsfájl hibát jelez, vagy a Kdump nincs engedélyezve, a következő részletekkel emelje fel a szolgáltatást a Microsoft támogatási csapatával.

* HLI előfizetés azonosítója

* Kiszolgálónév

* Operációs rendszer szállítója

* Operációs rendszer verziója

* Kernel verziója
