---
title: Oktatóanyag – Egy Azure DNS-aliasrekord létrehozása a zónában lévő erőforrásrekordra való hivatkozáshoz.
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy Azure DNS-aliasrekordot a zónán belüli erőforrásrekordra való hivatkozáshoz.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990841"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Oktatóanyag: Hozzon létre egy aliasrekordot a zónában lévő erőforrásrekordra való hivatkozáshoz

Az aliasrekordok hivatkozhatnak más azonos típusú rekordhalmazokra. Például rendelkezhet egy DNS CNAME-rekordhalmazzal, amely egy ugyanolyan típusú másik CNAME-rekordhalmaz aliasa. Ez akkor hasznos, ha szeretne néhány rekordhalmazt, amelyek aliasok és néhányat, amelyek nem aliasok a viselkedés tekintetében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Aliasrekord létrehozása egy erőforrásrekordhoz a zónában
> * Az aliasrekord tesztelése


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes egészében rendelkeznie kell ezzel a tartománnyal, többek között be kell tudnia állítani a tartomány névkiszolgálói (NS) rekordjait.

A tartomány Azure DNS-ben való üzemeltetésére vonatkozó utasításokért lásd az [Oktatóanyag: A tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Aliasrekord létrehozása

Aliasrekord létrehozása, amely egy erőforrásrekordra mutat a zónában.

### <a name="create-the-target-resource-record"></a>A célként megadott erőforrásrekord létrehozása
1. Kattintson az Azure DNS-zónára a zóna megnyitásához.
2. Kattintson a **Rekordhalmaz** gombra.
3. A **Név** szövegmezőbe írja be, hogy **kiszolgáló**.
4. A **Típus** számára válassza az **A** értéket.
5. Írja be az **IP-CÍM** szövegmezőbe, hogy **10.10.10.10**.
6. Kattintson az **OK** gombra.

### <a name="create-the-alias-record"></a>Az aliasrekord létrehozása
1. Kattintson az Azure DNS-zónára a zóna megnyitásához.
2. Kattintson a **Rekordhalmaz** gombra.
3. A **Név** szövegmezőbe írja be, hogy **teszt**.
4. A **Típus** számára válassza az **A** értéket.
5. Kattintson az **Igen** elemre az **Alias-rekordhalmaz** jelölőnégyzetben, majd válassza a **Zónarekordhalmaz** lehetőséget.
6. A **Zóna-rekordhalmaz** beállításaként, jelölje be a **kiszolgáló** rekordot.
7. Kattintson az **OK** gombra.

## <a name="test-the-alias-record"></a>Az aliasrekord tesztelése

1. Indítsa el a kedvenc nslookup eszközét. Az egyik lehetőség, hogy látogasson el a [https://network-tools.com/nslook](https://network-tools.com/nslook) lapra.
2. A lekérdezés típusaként adja meg, hogy A rekordok, és keresse meg a **teszt.\<saját tartományneve\>** elemet. Válaszként a **10.10.10.10** értéket kell kapnia.
3. Az Azure Portalon módosítsa a **kiszolgáló** A rekordjának értékét a következőre: **10.11.11.11**.
4. Várjon néhány percig, és használja újra az nslookup eszközt a **teszt** rekordhoz.
5. Válaszként a **10.11.11.11** értéket kell kapnia.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti a **kiszolgáló** és a **teszt** erőforrásrekordokat a zónában.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy aliasrekordot a zónába tartozó erőforrásrekordra való hivatkozáshoz. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
