---
title: "Az Azure DNS használatának első lépései az Azure Portal használatával | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure Portal használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 656f2c7d00161d15ebb6f72db0dff33c709973c2
ms.lasthandoff: 03/21/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Bevezetés az Azure DNS Azure Portallal való használatába

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Ez a cikk végigvezeti az első DNS-zóna és -rekord létrehozásának lépésein az Azure Portalon. Ezek a lépések az Azure PowerShell-lel vagy a platformfüggetlen Azure CLI-vel is elvégezhetőek.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A központi menüben kattintson az **Új > Hálózatkezelés >** elemre, majd kattintson a **DNS-zóna** elemre a DNS-zóna létrehozása panel megnyitásához.

    ![DNS-zóna](./media/dns-getstarted-portal/openzone650.png)

4. A **DNS-zóna létrehozása** panelen nevezze el a DNS-zónát. Például adja meg a *contoso.com* nevet.
 
    ![Zóna létrehozása](./media/dns-getstarted-portal/newzone250.png)

5. Ezután adja meg a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt. Ha új erőforráscsoport létrehozását választja, a **Hely** legördülő listában adja meg az erőforráscsoport helyét. Vegye figyelembe, hogy ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

6. A **Rögzítés az irányítópulton** jelölőnégyzetet bejelölve hagyhatja, ha könnyedén meg szeretné találni az új zónát az irányítópulton. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Rögzítés az irányítópulton](./media/dns-getstarted-portal/pindashboard150.png)

7. Miután a Létrehozás gombra kattint, láthatja az új zóna konfigurálását az irányítópulton.

    ![Létrehozás](./media/dns-getstarted-portal/creating150.png)

8. Amikor létrejött az új zóna, a hozzá tartozó panel megnyílik az irányítópulton.


## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

A következő példa végigvezeti egy új „A” rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört. 


1. A **DNS-zóna** panel tetején válassza a **+ Rekordhalmaz** elemet a **Rekordhalmaz hozzáadása** panel megnyitásához.

    ![Új rekordhalmaz](./media/dns-getstarted-portal/newrecordset500.png)

4. A **Rekordhalmaz hozzáadása** panelen nevezze el a rekordhalmazt. Például adja neki a „**www**” nevet.

    ![Rekordhalmaz hozzáadása](./media/dns-getstarted-portal/addrecordset500.png)

5. Válassza ki a létrehozni kívánt rekord típusát. Ehhez a példához válassza az **A** típust.
6. Állítsa be az **élettartamot**. Az alapértelmezett élettartam egy óra.
7. Adja hozzá a rekord IP-címét.
8. Válassza a panel alján található **OK** gombot a DNS-rekord létrehozásához.


## <a name="view-records"></a>A rekordok megtekintése

A DNS-zóna panel alsó részén láthatja a DNS-zóna rekordjait. Meg kell jelennie az alapértelmezett NS és SOA típusú rekordoknak, amelyek minden zónában létrejönnek, valamint az összes új létrehozott rekordnak.

![zóna](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>A névkiszolgálók frissítése

Ha a DNS-zóna és -rekordok megfelelően be lettek állítva, konfigurálnia kell a tartománynevet az Azure DNS-névkiszolgálók használatára. Így más internetes felhasználók megkereshetik a DNS-rekordjait.

A zóna névkiszolgálói az Azure Portalon vannak megadva:

![zóna](./media/dns-getstarted-portal/viewzonens500.png)

Ezeket a névkiszolgálókat a tartományregisztrálóhoz kell konfigurálni (ahol a tartománynevet vásárolta). A regisztráló felajánlja, hogy beállítja a névkiszolgálókat a tartományhoz. További információért lásd: [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md).


## <a name="next-steps"></a>Következő lépések

Az Azure DNS-sel kapcsolatos további információért lásd [az Azure DNS áttekintését biztosító](dns-overview.md) cikket.

Az Azure DNS-ben a DNS-rekordok kezelésével kapcsolatos további információért lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.


