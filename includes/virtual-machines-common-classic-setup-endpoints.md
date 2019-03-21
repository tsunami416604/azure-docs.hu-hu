---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ee5faedd4f59aa791424a1f178f0462922f21d28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094689"
---
Mindegyik végpontra egy *nyilvános port* és a egy *magánhálózati port*:

* A nyilvános port szolgál az Azure load balancer által figyelni a bejövő forgalmat a virtuális géphez az internetről.
* A magánhálózati port figyelni a bejövő forgalmat, általában egy alkalmazás vagy szolgáltatás fut a virtuális gép felé irányuló a virtuális gép által használt.

Az IP protokoll és a jól ismert hálózati protokollok találhatók végpontok az Azure Portallal való létrehozásakor a TCP vagy UDP-portok alapértelmezett értékeit. Egyéni végpontok adja meg a megfelelő IP-protokollt (TCP vagy UDP) és a nyilvános és magánhálózati portokra. A bejövő forgalom elosztását véletlenszerűen több virtuális gép között, több végpontot, amely elosztott terhelésű készlet létrehozása.

Miután létrehozott egy végpontot, használhatja a hozzáférés-vezérlési lista (ACL) szabályok meghatározásához, amelyek vagy megtagadhatja a bejövő forgalmat a forrás IP-címe alapján a végpont nyilvános port. Azonban ha a virtuális gép Azure-beli virtuális hálózathoz, inkább a hálózati biztonsági csoportok. További információkért lásd: [hálózati biztonsági csoportokkal kapcsolatos](../articles/virtual-network/security-overview.md).

> [!NOTE]
> A tűzfal konfigurálása az Azure virtual machines a távoli kapcsolati végpontok, amelyek az Azure automatikusan beállít társított portokhoz automatikusan történik. A portok összes végponthoz megadott, nem konfigurálása automatikusan történik a virtuális gép a tűzfalhoz. Amikor létrehoz egy végpontot a virtuális gép, győződjön meg arról, hogy a tűzfal a virtuális gép is lehetővé teszi, hogy a forgalom a protokoll és a végpont-konfiguráció megfelelő magánhálózati port. A tűzfal konfigurálásáról lásd: a dokumentációban vagy a virtuális gépen futó operációs rendszer online súgó.
>
>

## <a name="create-an-endpoint"></a>Végpont létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **virtuális gépek**, majd válassza ki a konfigurálni kívánt virtuális gépet.

3. Válassza ki **végpontok** a a **beállítások** csoport. A **végpontok** lap, amely végpontjai az aktuális virtuális gép. (Az ebben a példában egy Windows virtuális gép számára. Egy Linux rendszerű virtuális gép alapértelmezés szerint jelennek meg a végpont az SSH-hoz.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Végpontok](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. A parancssorban a végpont-bejegyzések fölött, válassza ki a **Hozzáadás**. A **végpont hozzáadása** lap jelenik meg.

5. A **neve**, adja meg a végpont nevét.

6. A **protokoll**, válasszon **TCP** vagy **UDP**.

7. A **nyilvános port**, adja meg a portszámot a bejövő forgalom az internetről. 

8. A **magánhálózati port**, adja meg a portszámot, amelyen a virtuális gép figyel. A nyilvános és privát portszámokat eltérő lehet. Győződjön meg arról, hogy beállítottak-e a virtuális gép tűzfala engedélyezi a forgalmat a protokoll és a magánhálózati port megfelelő.

9. Kattintson az **OK** gombra.

Az új végpont megjelenik a **végpontok** lapot.

![Végpont létrehozása sikeres](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>A végponti ACL kezelése
A végponti ACL definiálásához az számítógépeket, amelyek képesek a forgalmat küldeni, korlátozhatja a forgalmat a forrás IP-cím alapján. Kövesse az alábbi lépéseket hozzáadása, módosítása vagy a végpont ACL eltávolítása.

> [!NOTE]
> A végpont egy elosztott terhelésű készlet része, ha végrehajtott módosítások az ACL-végponton a csoportban lévő összes végpontok lépnek.
>
>

Ha a virtuális gép Azure-beli virtuális hálózathoz, a hálózati biztonsági csoportok ACL-ek helyett. További információkért lásd: [hálózati biztonsági csoportokkal kapcsolatos](../articles/virtual-network/security-overview.md).

1. Jelentkezzen be az Azure portálra.

2. Válassza ki **virtuális gépek**, majd válassza ki a konfigurálni kívánt virtuális gép nevét.

3. Válassza ki **végpontok**. A végpontok listából válassza ki a megfelelő végpontra. Az ACL lista van, az oldal alján.

   ![ACL részleteinek megadása](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Használja a lista sorok hozzáadásához, törléséhez vagy az ACL szabályok szerkesztése és sorrendbe. A **távoli ALHÁLÓZATI** értéke egy IP-címtartományt, amely engedélyezi vagy letiltja a forgalmat a forrás IP-címének alapján használja az Azure load balancer az internetről bejövő forgalmat. Mindenképpen adja meg az IP-címtartományt classless Inter-Domain útválasztási (CIDR formátumban), más néven a cím előtagja formátuma. Például: `10.1.0.0/8`.

   ![Új ACL-bejegyzéssel](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Szabályok is használhatja, vagy meghatározott, ismert címtartományok a forgalom megtagadásához szükséges megfelelő a számítógépek az interneten, a megadott számítógépekről érkező csak adatforgalom engedélyezéséhez.

A szabályok kiértékelése sorrendben az első szabály a kezdő és záró az utolsó szabállyal. Ezért szabályokat kell kell rendelni a legkevésbé korlátozó leginkább korlátozó. További információkért lásd: [Mi az a hálózati hozzáférés-vezérlési lista](../articles/virtual-network/virtual-networks-acl.md).
