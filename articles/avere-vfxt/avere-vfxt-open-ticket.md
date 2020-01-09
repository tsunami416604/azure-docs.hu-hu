---
title: Hogyan kérhet támogatást az Azure-hoz készült avere-vFXT
description: Az Azure-hoz készült avere-vFXT kapcsolatos támogatási jegyek megnyitásának magyarázata
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8a371f902703287ed3105ed53a57d6341b9528d4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415107"
---
# <a name="get-help-with-your-system"></a>Segítség kérése a rendszeren

Ha segítségre van szüksége az Azure-hoz készült avere-vFXT, az alábbi módokon kérhet támogatást:

* **Avere vFXT-probléma** – a Azure Portal segítségével nyisson meg egy támogatási jegyet az avere-vFXT az [alább](#open-a-support-ticket-for-your-avere-vfxt)leírtak szerint.
* **Kvóta** – ha kvótával kapcsolatos probléma merül fel, [a kvóta növelését kéri](#request-a-quota-increase)
* **Dokumentáció és példák** – ha problémák merülnek fel ezzel a dokumentációval vagy példákkal kapcsolatban, görgessen a lap aljára, és használja a **visszajelzés** szakaszt a meglévő problémák megkereséséhez, és szükség esetén új fájl létrehozásához.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Támogatási jegy megnyitása a avere-vFXT

Ha a avere vFXT telepítése vagy használata során problémák merülnek fel, kérjen segítséget a Azure Portal.

Kövesse az alábbi lépéseket annak biztosításához, hogy a támogatási jegye a fürt egy erőforrásával legyen címkézve. A jegy címkézése segít a megfelelő támogatási erőforráshoz való átirányításban.

1. [https://portal.azure.com](https://portal.azure.com)válassza az **erőforráscsoportok**lehetőséget.

   ![képernyőkép Azure Portal bal oldali menü "erőforráscsoportok" körben](media/avere-vfxt-ticket-rg.png)

1. Tallózással keresse meg azt a vFXT-fürtöt tartalmazó erőforráscsoportot, ahol a probléma felmerült, és kattintson az egyik avere virtuális gépre.

    ![képernyőfelvétel: Azure Portal erőforráscsoport – áttekintés panel egy adott virtuális géppel](media/avere-vfxt-ticket-vm.png)

1. A virtuális gép lapon görgessen le a bal oldali panel aljára, és kattintson az **új támogatási kérelem**elemre.

    ![Képernyőkép a virtuális gép Azure Portal virtuális gép oldaláról az előző képernyőképen. A bal oldali menü az alsó és az "új támogatási kérelem" körben görgethető.](media/avere-vfxt-ticket-request.png)

1. A támogatási kérelem egyikén kattintson a **minden szolgáltatás** elemre, és keresse meg a **avere VFXT**elemet a **tárterület** területen.

    ![képernyőkép a Azure Portal új támogatási kérés képernyőjéről, az "alapismeretek" fejléctel és a "szolgáltatás" elem köré. A "minden szolgáltatás" gomb van kiválasztva, és a legördülő menü mező értéke "avere vFXT".](media/avere-vfxt-ticket-service.png)

1. A második oldalon válassza ki a probléma típusát és kategóriáját, amely leginkább megfelel a problémának. Adjon hozzá egy rövid címet és egy leírást, amely tartalmazza a probléma előfordulásának időpontját.

   ![képernyőkép az új támogatási kérés képernyőről a "probléma" fejléctel, amely sok olyan mezőt tartalmaz, amelyeket el kell végezni](media/avere-vfxt-ticket-problem.png)

1. A harmadik oldalon adja meg a kapcsolattartási adatait, majd kattintson a **Létrehozás**gombra. A rendszer visszaigazolja az e-mail-címre, és felveszi Önnel a kapcsolatot a támogatási személyzet tagjaival.

## <a name="request-a-quota-increase"></a>Kvóta növelésének kérése

Olvassa el [a kvótát a vFXT-fürthöz](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) , hogy megtudja, milyen összetevőkre van szükség a avere-vFXT Azure-beli telepítéséhez. [Kvótát is igényelhet](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a Azure Portal.
