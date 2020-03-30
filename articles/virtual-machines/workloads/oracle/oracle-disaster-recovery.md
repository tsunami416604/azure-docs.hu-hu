---
title: Oracle vész-helyreállítási forgatókönyv áttekintése az Azure-környezetben | Microsoft dokumentumok
description: Vész-helyreállítási forgatókönyv egy Oracle Database 12c adatbázishoz az Azure-környezetben
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: f6f678f91e74ea9b0b68127c1786fee745508b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101465"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Vészhelyreállítás egy Oracle Database 12c adatbázishoz Azure-környezetben

## <a name="assumptions"></a>Feltételezések

- Ismeri az Oracle Data Guard tervezését és az Azure-környezeteket.


## <a name="goals"></a>Célok
- Tervezze meg a vész-helyreállítási (DR) követelményeknek megfelelő topológiát és konfigurációt.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>1. forgatókönyv: Elsődleges és DR-helyek az Azure-ban

Az ügyfél oracle-adatbázist állított be az elsődleges helyen. A VÉSZ-hely egy másik régióban található. Az ügyfél az Oracle Data Guard segítségével gyors helyreállítást kér ezen oldalak között. Az elsődleges hely is rendelkezik egy másodlagos adatbázis jelentési és egyéb célokra. 

### <a name="topology"></a>Topológia

Az alábbiakban összefoglaljuk az Azure beállítását:

- Két hely (elsődleges hely és vész-pont)
- Két virtuális hálózat
- Két Oracle adatbázis a Data Guard segítségével (elsődleges és készenléti állapot)
- Két Oracle adatbázis golden gate-tel vagy data guard-mal (csak elsődleges helyen)
- Két alkalmazásszolgáltatás, egy elsődleges és egy a VÉSZ-webhelyen
- Rendelkezésre *állási készlet,* amely az elsődleges helyen lévő adatbázis- és alkalmazásszolgáltatáshoz használatos
- Minden webhelyen egy ugródoboz, amely korlátozza a hozzáférést a magánhálózathoz, és csak rendszergazda általi bejelentkezést tesz lehetővé
- Jumpbox, alkalmazásszolgáltatás, adatbázis és VPN-átjáró külön alhálózatokon
- NSG kényszerítve az alkalmazás- és adatbázis-alhálózatokon

![Képernyőkép a DR topológia lapról](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>2. forgatókönyv: Elsődleges hely a helyszínen és a VÉSZ-hely az Azure-ban

Az ügyfél rendelkezik egy helyszíni Oracle adatbázis-beállítással (elsődleges hely). A DR-webhely az Azure-ban található. Az Oracle Data Guard a helyek közötti gyors helyreállításhoz használható. Az elsődleges hely is rendelkezik egy másodlagos adatbázis jelentési és egyéb célokra. 

Ehhez a beállításhoz két módszer létezik.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>1. megközelítés: Közvetlen kapcsolatok a helyszíni és az Azure között, amely nyitott TCP-portokat igényel a tűzfalon 

Nem javasoljuk a közvetlen kapcsolatokat, mert a TCP-portokat a külvilág számára elérhetővé teszik.

#### <a name="topology"></a>Topológia

Az alábbiakban összefoglaljuk az Azure-beállításokat:

- Egy DR-webhely 
- Egy virtuális hálózat
- Egy Oracle adatbázis data guard (aktív)
- Egy alkalmazásszolgáltatás a VÉSZ-webhelyen
- Egy ugródoboz, amely korlátozza a hozzáférést a magánhálózathoz, és csak rendszergazda általi bejelentkezést tesz lehetővé
- Jumpbox, alkalmazásszolgáltatás, adatbázis és VPN-átjáró külön alhálózatokon
- NSG kényszerítve az alkalmazás- és adatbázis-alhálózatokon
- NSG-házirend/szabály az 1521-es bejövő TCP-port (vagy a felhasználó által definiált port) engedélyezéséhez
- NSG-házirend/szabály, amely csak a virtuális hálózat eléréséhez csak a helyszíni IP-címeket/címeket (DB vagy alkalmazás) korlátozza

![Képernyőkép a DR topológia lapról](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>2. megközelítés: Helyek közötti VPN
A helyek közötti VPN jobb megközelítés. A VPN beállításáról a [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a CLI használatával című](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)témakörben talál további információt.

#### <a name="topology"></a>Topológia

Az alábbiakban összefoglaljuk az Azure-beállításokat:

- Egy DR-webhely 
- Egy virtuális hálózat 
- Egy Oracle adatbázis data guard (aktív)
- Egy alkalmazásszolgáltatás a VÉSZ-webhelyen
- Egy ugródoboz, amely korlátozza a hozzáférést a magánhálózathoz, és csak rendszergazda általi bejelentkezést tesz lehetővé
- A jumpbox, az alkalmazásszolgáltatás, az adatbázis és a VPN-átjáró külön alhálózatokon található
- NSG kényszerítve az alkalmazás- és adatbázis-alhálózatokon
- Helyek közötti VPN-kapcsolat a helyszíni és az Azure között

![Képernyőkép a DR topológia lapról](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>További olvasás

- [Oracle-adatbázis tervezése és megvalósítása az Azure-ban](oracle-design.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Az Oracle Golden Gate konfigurálása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)


## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [Fedezze fel a virtuális gép üzembe helyezését az Azure CLI-mintákban](../../linux/cli-samples.md)
