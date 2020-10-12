---
title: Az Oracle vész-helyreállítási forgatókönyvek áttekintése az Azure-környezetben | Microsoft Docs
description: Vész-helyreállítási forgatókönyv egy Oracle Database 12c-adatbázishoz az Azure-környezetben
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 5a162c0683a953399b48aaf627bbff5bc674e9dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274561"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Oracle Database 12c-adatbázis vész-helyreállítása Azure-környezetben

## <a name="assumptions"></a>Feltételezések

- Megértette az Oracle-alapú adatvédelem kialakítását és az Azure-környezeteket.


## <a name="goals"></a>Célok
- Tervezze meg a vész-helyreállítási (DR) követelményeknek megfelelő topológiát és konfigurációt.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>1. forgatókönyv: az Azure-beli elsődleges és DR-helyek

Az ügyfél rendelkezik egy Oracle-adatbázissal, amely az elsődleges helyen van beállítva. Egy DR-hely egy másik régióban található. Az ügyfél az Oracle adatvédelmet használja a helyek közötti gyors helyreállításhoz. Az elsődleges hely egy másodlagos adatbázist is tartalmaz a jelentéskészítéshez és más felhasználásokhoz. 

### <a name="topology"></a>Topológia

Az Azure Setup összefoglalása:

- Két hely (egy elsődleges hely és egy DR-hely)
- Két virtuális hálózat
- Két Oracle-adatbázis az adatvédelemmel (elsődleges és készenléti)
- Két Oracle-adatbázis a Golden Gate vagy az adatvédelem (csak az elsődleges hely) esetén
- Két Application Services, egy elsődleges és egy a DR webhelyén
- Egy *rendelkezésre állási csoport,* amely az elsődleges helyen található adatbázis-és alkalmazás-szolgáltatáshoz használatos
- Egy Jumpbox minden helyen, amely korlátozza a magánhálózaton való hozzáférést, és csak a rendszergazda általi bejelentkezést engedélyezi
- Jumpbox, alkalmazás-szolgáltatás, adatbázis és VPN-átjáró külön alhálózatokon
- NSG kényszerítve az alkalmazás-és adatbázis-alhálózatokon

![A DR-topológia oldalának képernyőképe](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>2. forgatókönyv: a helyszíni és DR. Azure-beli elsődleges hely

Az ügyfél helyszíni Oracle Database-telepítést (elsődleges helyet) tartalmaz. A DR-hely az Azure-ban található. Az Oracle-adatőr a helyek közötti gyors helyreállításhoz használható. Az elsődleges hely egy másodlagos adatbázist is tartalmaz a jelentéskészítéshez és más felhasználásokhoz. 

Ennek a beállításnak két megközelítése van.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>1. módszer: közvetlen kapcsolatok a helyszíni és az Azure között, a tűzfalon nyitott TCP-portok megkövetelése 

A közvetlen kapcsolatok nem ajánlottak, mert a TCP-portokat a külvilág számára teszik elérhetővé.

#### <a name="topology"></a>Topológia

A következőkben az Azure-telepítés összegzése található:

- Egy DR-hely 
- Egy virtuális hálózat
- Egy Oracle-adatbázis és egy adatőr (aktív)
- Egy Application Service a DR webhelyén
- Egy Jumpbox, amely korlátozza a magánhálózat elérését, és csak a rendszergazda általi bejelentkezést engedélyezi
- Jumpbox, alkalmazás-szolgáltatás, adatbázis és VPN-átjáró külön alhálózatokon
- NSG kényszerítve az alkalmazás-és adatbázis-alhálózatokon
- Egy NSG házirend/szabály, amely engedélyezi a 1521 bejövő TCP-portot (vagy egy felhasználó által megadott portot)
- Egy NSG házirend/szabály, amely kizárólag a helyszíni IP-címet vagy-címeket korlátozza a virtuális hálózat eléréséhez

![A DR-topológia oldalának képernyőképe](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>2. módszer: helyek közötti VPN
A helyek közötti VPN jobb megközelítés. A VPN beállításával kapcsolatos további információkért lásd: [virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a CLI használatával](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md).

#### <a name="topology"></a>Topológia

A következőkben az Azure-telepítés összegzése található:

- Egy DR-hely 
- Egy virtuális hálózat 
- Egy Oracle-adatbázis és egy adatőr (aktív)
- Egy Application Service a DR webhelyén
- Egy Jumpbox, amely korlátozza a magánhálózat elérését, és csak a rendszergazda általi bejelentkezést engedélyezi
- A Jumpbox, az alkalmazás-szolgáltatás, az adatbázis és a VPN-átjáró külön alhálózatokon található.
- NSG kényszerítve az alkalmazás-és adatbázis-alhálózatokon
- Helyek közötti VPN-kapcsolat a helyszíni és az Azure között

![A DR-topológia oldalának képernyőképe](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>További információ

- [Oracle-adatbázis tervezése és implementálása az Azure-ban](oracle-design.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Az Oracle Golden Gate konfigurálása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)


## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](../../linux/cli-samples.md)
