---
title: Mi az alhálózati delegálás az Azure virtuális hálózatában?
description: Információ az azure-beli virtuális hálózat alhálózati delegálásáról
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281336"
---
# <a name="what-is-subnet-delegation"></a>Mi az alhálózati delegálás?

Alhálózati delegálás lehetővé teszi, hogy egy adott alhálózat egy ön által kiválasztott Azure PaaS-szolgáltatás, amely et be kell adni a virtuális hálózatba. Az alhálózati delegálás teljes körű ellenőrzést biztosít az ügyfél számára az Azure-szolgáltatások virtuális hálózatokba való integrálásának kezeléséhez.

Amikor egy Azure-szolgáltatásnak delegál egy alhálózatot, lehetővé teszi, hogy a szolgáltatás hozzon létre néhány alapvető hálózati konfigurációs szabályt az adott alhálózathoz, amelyek segítségével az Azure-szolgáltatás stabil módon működtetheti a példányait. Ennek eredményeképpen az Azure-szolgáltatás létrehozhat néhány üzembe helyezés előtti vagy utáni feltételt, például:
- a szolgáltatást egy megosztott és dedikált alhálózatban telepítheti.
- adjon hozzá a szolgáltatáshoz a telepítés utáni hálózati szándékházirendek egy készletét, amely a szolgáltatás megfelelő működéséhez szükséges.

##  <a name="advantages-of-subnet-delegation"></a>Az alhálózati delegálás előnyei

Az alhálózat adott szolgáltatásokra történő delegálása a következő előnyökkel jár:

- segít egy vagy több Azure-szolgáltatás alhálózatának kijelölésében, és az alhálózat példányainak a követelmények szerint kezelésében. A virtuális hálózat tulajdonosa például a következőket határozhatja meg egy delegált alhálózathoz az erőforrások és a hozzáférés jobb kezelése érdekében az alábbiak szerint:
    - hálózati szűrési forgalomházirendek hálózati biztonsági csoportokkal.
    - útválasztási házirendek a felhasználó által definiált útvonalakkal.
    - szolgáltatások integrálása a szolgáltatásvégpont-konfigurációkkal.
- segít az injektált szolgáltatások nak a virtuális hálózatba való jobb integrálásában azáltal, hogy hálózati szándékházirendek formájában határozza meg a központi telepítés előfeltételeit. Ez biztosítja, hogy az injektált szolgáltatás működését befolyásoló műveletek blokkolhatók a PUT-nál.


## <a name="who-can-delegate"></a>Ki delegálhat?
Az alhálózati delegálás egy olyan gyakorlat, amelyet a virtuális hálózat tulajdonosainak végre kell hajtaniuk egy adott Azure-szolgáltatás egyik alhálózatának kijelöléséhez. Az Azure Service viszont telepíti a példányokat ebbe az alhálózatba az ügyfél számítási feladatok általi felhasználás érdekében.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Az alhálózati delegálás hatása az alhálózatra
Minden Azure-szolgáltatás saját telepítési modellt határoz meg, ahol meghatározhatja, hogy milyen tulajdonságokat végeznek vagy nem támogatnak egy delegált alhálózatban injektálási célokra, például a következők:
- megosztott alhálózat más Azure-szolgáltatásokkal vagy virtuális gép / virtuálisgép-méretezési készlet ugyanabban az alhálózatban, vagy csak egy dedikált alhálózat csak a szolgáltatás példányai vannak benne.
- támogatja az NSG-társítást a delegált alhálózattal.
- támogatja a delegált alhálózathoz társított NSG-t bármely más alhálózathoz is társítható.
- lehetővé teszi az útvonaltábla-társítást a delegált alhálózattal.
- Lehetővé teszi, hogy a delegált alhálózathoz társított útvonaltábla bármely más alhálózathoz legyen társítva.
- a delegált alhálózatBAN lévő IP-címek minimális számát határozza meg.
- a delegált alhálózat IP-címterét a privát IP-címtérből (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12) határozza meg.
- azt diktálja, hogy az egyéni DNS-konfiguráció rendelkezik Egy Azure DNS-bejegyzéssel.

Az injektált szolgáltatások saját házirendeket is hozzáadhatnak az alábbiak szerint:
- **Biztonsági házirendek**: Egy adott szolgáltatás működéséhez szükséges biztonsági szabályok gyűjteménye.
- **Útvonalházirendek**: Egy adott szolgáltatás működéséhez szükséges útvonalak gyűjteménye.

## <a name="what-subnet-delegation-does-not-do"></a>Mit nem végez az alhálózati delegálás?

A delegált alhálózatba injektált Azure-szolgáltatások továbbra is rendelkeznek a nem delegált alhálózatok hoz rendelkezésre álló alapvető tulajdonságokkal, például:
-  Az Azure-szolgáltatások bevihetik a példányokat az ügyfél alhálózataiba, de nem befolyásolhatják a meglévő számítási feladatokat.
-  Az ezek a szolgáltatások által alkalmazott házirendek vagy útvonalak rugalmasak, és az ügyfél felülbírálhatja őket.

## <a name="next-steps"></a>További lépések

- [Alhálózat delegálása](manage-subnet-delegation.md)
