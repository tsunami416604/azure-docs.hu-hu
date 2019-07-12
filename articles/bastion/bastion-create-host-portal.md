---
title: Hozzon létre egy Azure bástyagazdagép |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure bástyagazdagép
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 65116ebbd6a66241a5b35a39f3dfb8f826a3745f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594274"
---
# <a name="create-an-azure-bastion-host-preview"></a>Hozzon létre egy Azure bástyagazdagép (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre egy Azure bástyagazdagép. Után a virtuális hálózaton üzembe helyezi a megerősített Azure-szolgáltatás, az RDP/SSH zökkenőmentes felhasználói élmény az azonos virtuális hálózatban lévő összes virtuális gép számára érhető el. Az üzembe helyezés virtuális hálózatonként értelmezendő, nem pedig előfizetések/fiókok vagy virtuális gépek alapján.

Kétféleképpen hozhatja létre a megerősített gazdagépi erőforrás:

* Hozzon létre egy megerősített erőforrást az Azure portal használatával.
* Az Azure Portalon megerősített erőforrás létrehozása meglévő virtuális gép beállításainak használatával.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzióban korlátozva az Azure nyilvános elérhetősége:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Hozzon létre egy bástyagazdagép

Ez a szakasz segítséget nyújt a hozzon létre egy új megerősített Azure-erőforrást az Azure Portalról.

1. (Kezdőlap) lapján a [Azure portal – megerősített előzetes](https://aka.ms/BastionHost), kattintson a **+ erőforrás létrehozása**. Ellenőrizze, hogy a jelen előzetes, nem a normál Azure portal-portál eléréséhez megadott hivatkozás használata.

1. Az a **új** lap a *keresés a piactéren* mezőbe írja be a **megerősített**, majd kattintson a **Enter** a keresési eredmények beolvasásához.

1. Az eredmények között kattintson **megerősített (előzetes verzió)** . Győződjön meg arról, hogy a közzétevő *Microsoft* a kategóriát pedig *hálózatkezelés*.

1. Az a **megerősített (előzetes verzió)** kattintson **létrehozás** megnyitásához a **hozzon létre egy megerősített** lap.

1. Az a **hozzon létre egy megerősített** területén konfigurálja egy új megerősített erőforrást. Adja meg a megerősített erőforrás konfigurációs beállításait.

    ![Hozzon létre egy megerősített](./media/bastion-create-host-portal/settings.png)

    * **Előfizetés**: Az Azure-előfizetést egy új megerősített-erőforrás létrehozásához használni kívánt.
    * **Erőforráscsoport**: Az Azure-erőforráscsoportot, amelyben az új megerősített erőforrás létrejön. Ha nem rendelkezik egy meglévő erőforráscsoportot, létrehozhat egy újat.
    * **Név**: Az új megerősített erőforrás neve
    * **Régió**: Az erőforrás jön létre az Azure nyilvános régió.
    * **Virtuális hálózat**: A virtuális hálózatot, amelyben a megerősített erőforrás létrejön. Létrehozhat egy új virtuális hálózatot a portálon Ez a folyamat során abban az esetben nem rendelkezik, vagy nem szeretné használni a meglévő virtuális hálózattal. Ha egy meglévő virtuális hálózatot használ, győződjön meg arról, a meglévő virtuális hálózat rendelkezik elegendő szabad címtér megerősített alhálózati követelményeinek megfelelően.
    * **Alhálózat**: Az alhálózat, amelyhez az új megerősített gazdagép erőforrást szeretné telepíteni a virtuális hálózaton. Létre kell hoznia egy alhálózatot a név-érték használatával **AzureBastionSubnet**. Ez az érték lehetővé teszi, hogy az Azure tudja, hogy melyik alhálózatot a megerősített erőforrások üzembe helyezéséhez. Ez eltér egy átjáró-alhálózatot. Erősen ajánlott legalább/27 vagy nagyobb alhálózat használata (/ 27, / 26 stb). Hozzon létre a **AzureBastionSubnet** minden olyan hálózati biztonsági csoportok nélkül irányíthatja a táblák vagy delegálásokat.
    * **Nyilvános IP-cím**: A nyilvános IP-címét a megerősített erőforrás, amelyen az RDP/SSH (a 443-as porton) keresztül kell elérnie. Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címet a megerősített erőforrást hoz létre ugyanabban a régióban kell lennie.
    * **Nyilvános IP-cím neve**: A nyilvános IP-cím erőforrás neve.
    * **Nyilvános IP-cím Termékváltozat**: Alapértelmezés szerint a előre feltöltve **Standard**. Azure megerősített használ és támogatja a csak a Standard nyilvános IP Termékváltozat.
    * **Hozzárendelés**: Alapértelmezés szerint a előre feltöltve **statikus**.

1. Kattintson a beállítások megadása után, amikor **felülvizsgálat + létrehozás**. Ez igazolja, hogy az értékeket. Ellenőrzés elvégzése után megkezdheti a létrehozását.
1. A megerősített lap létrehozása kattintson **létrehozás**.
1. Megjelenik egy üzenet értesíti, hogy az üzembe helyezés folyamatban van. Az erőforrások létrehozásakor állapotát ezen a lapon fog megjelenni. A megerősített erőforrás létrehozása és üzembe helyezett körülbelül 5 percet vesz igénybe.

## <a name="createvmset"></a>Hozzon létre egy virtuális gép beállításainak használatával bástyagazdagép

Bástyagazdagép a portálon hozzon létre egy meglévő virtuális gép használatával, ha különböző beállítások automatikusan alapértelmezés szerint a virtuális gép és/vagy a virtuális hálózat megfelelő.

1. Az a [Azure portal – megerősített előzetes](https://aka.ms/BastionHost), keresse meg a virtuális géphez, majd kattintson a **Connect**.

    ![Virtuális gép csatlakoztatása](./media/bastion-create-host-portal/vmsettings.png)

1. Kattintson a jobb oldali oldalsávon **megerősített**, majd **használata megerősített**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. A megerősített lapon töltse ki az alábbi beállítások mezőket:

    * **Név**: A létrehozni kívánt bástyagazdagép neve.
    * **Alhálózat**: Az alhálózatot, amely megerősített erőforrást szeretné telepíteni a virtuális hálózaton belül. Az alhálózat létre kell hozni a nevű **AzureBastionSubnet**. Ez lehetővé teszi, hogy az Azure tudja, melyik alhálózat a megerősített erőforrás üzembe helyezéséhez. Ez eltér egy átjáró-alhálózatot. Kattintson a **kezelés alhálózati konfigurációt** az Azure megerősített alhálózat létrehozásához. Erősen ajánlott legalább/27 vagy nagyobb alhálózat használata (/ 27, / 26-os stb.). Hozzon létre a **AzureBastionSubnet** minden olyan hálózati biztonsági csoportok nélkül irányíthatja a táblák vagy delegálásokat. Kattintson a **létrehozás** az alhálózat létrehozásához, majd folytassa a következő beállításokat.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Nyilvános IP-cím**: A nyilvános IP-címét a megerősített erőforrás, amelyen az RDP/SSH (a 443-as porton) keresztül kell elérnie. Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címet a megerősített erőforrást hoz létre ugyanabban a régióban kell lennie.
    * **Nyilvános IP-cím neve**: A nyilvános IP-cím erőforrás neve.
1. Az érvényesítés képernyőn kattintson a **létrehozás**. Várjon, amíg a megerősített erőforrás létrehozása és üzembe helyezett körülbelül 5 perc.

## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített – gyakori kérdések](bastion-faq.md)
