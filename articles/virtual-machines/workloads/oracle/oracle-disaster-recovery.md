---
title: "Az Oracle vész-helyreállítási forgatókönyv az Azure környezetben áttekintése |} Microsoft Docs"
description: "Oracle Database 12c adatbázis az Azure környezetben vészhelyreállítás"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.openlocfilehash: f17ebb2b74cd7ad872f88483ed7cdb4f239ee069
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Oracle Database 12c adatbázis Azure környezetben vészhelyreállítás

## <a name="assumptions"></a>Előfeltételek

- Oracle Data Guard tervezési és az Azure-környezetek rendelkezik.


## <a name="goals"></a>Célok
- Tervezési a topológia és a konfigurációt, amely a vész-helyreállítási követelményeinek megfelelően.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>1. forgatókönyv: Elsődleges és a vész-Helyreállítási helyeken az Azure-on

Az ügyfél rendelkezik egy Oracle adatbázis-állítsa be az elsődleges helyen. A vész-Helyreállítási hely van egy másik régióban. Az ügyfél Oracle Data Guard használja a helyek közötti gyors helyreállítás. Az elsődleges hely is rendelkezik, egy másodlagos adatbázis jelentéskészítéshez és egyéb felhasználásra. 

### <a name="topology"></a>topológia

Ez az Azure beállítása összefoglalása:

- Két hely (elsődleges hely és a vész-Helyreállítási hely)
- Két virtuális hálózatok
- Két adatbázisokkal Oracle Data Guard (elsődleges és készenléti állapot)
- Két Oracle adatbázis Golden kapu vagy Data Guard (csak az elsődleges hely)
- Két alkalmazás szolgáltatást, egy elsődleges és egy, a vész-Helyreállítási helyen
- Egy *rendelkezésre állási csoport* amellyel adatbázis és az alkalmazás szolgáltatás az elsődleges helyen
- Minden helyen, amely korlátozza a hozzáférést a magánhálózaton, és csak a bejelentkezés egy rendszergazda lehetővé teszi egy jumpbox
- A jumpbox, a alkalmazás service, az adatbázis és a VPN-átjáró külön alhálózatokon
- NSG kényszeríti az alkalmazás és az adatbázis alhálózatok

![A vész-Helyreállítási topológia oldalát bemutató képernyőkép](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>2. forgatókönyv: Helyszíni elsődleges hely és az Azure-on vész-Helyreállítási hely

Az ügyfél rendelkezik egy helyszíni Oracle adatbázis-beállítások (elsődleges helyen). A vész-Helyreállítási hely van, az Azure-on. Oracle Data Guard szolgál a helyek közötti gyors helyreállítás. Az elsődleges hely is rendelkezik, egy másodlagos adatbázis jelentéskészítéshez és egyéb felhasználásra. 

Kétféleképpen a telepítés.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>1. módszer: A helyszíni és az Azure, nyitott TCP-portok a tűzfalon igénylő közti közvetlen kapcsolatokon keresztül 

Közvetlen kapcsolatok nem ajánlott, mert a TCP-portok a külvilág szolgáltatnak.

#### <a name="topology"></a>topológia

Az alábbiakban olvashat az Azure beállítása összefoglalása:

- Egy vész-Helyreállítási hely 
- Egy virtuális hálózat
- A Data Guard (aktív) egy Oracle-adatbázishoz
- A vész-Helyreállítási helyen egy alkalmazásszolgáltatás
- Egy jumpbox, amely korlátozza a hozzáférést a magánhálózaton, és csak lehetővé teszi a bejelentkezés rendszergazda
- A jumpbox, a alkalmazás service, az adatbázis és a VPN-átjáró külön alhálózatokon
- NSG kényszeríti az alkalmazás és az adatbázis alhálózatok
- Egy NSG házirend/szabály, amely engedélyezi a bejövő TCP-portot 1521 (vagy egy felhasználó által definiált port)
- Egy NSG/házirendszabály korlátozása csak az IP-cím/címek helyszíni (DB vagy alkalmazás) a virtuális hálózat eléréséhez

![A vész-Helyreállítási topológia oldalát bemutató képernyőkép](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>2. módszer: Telephelyek közötti VPN
Telephelyek közötti VPN jobb megközelítés. VPN-en beállításával kapcsolatos további információkért lásd: [virtuális hálózat létrehozása a parancssori felület használatával telephelyek közötti VPN-kapcsolattal rendelkező](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>topológia

Az alábbiakban olvashat az Azure beállítása összefoglalása:

- Egy vész-Helyreállítási hely 
- Egy virtuális hálózat 
- A Data Guard (aktív) egy Oracle-adatbázishoz
- A vész-Helyreállítási helyen egy alkalmazásszolgáltatás
- Egy jumpbox, amely korlátozza a hozzáférést a magánhálózaton, és csak lehetővé teszi a bejelentkezés rendszergazda
- Külön alhálózatokon vannak a jumpbox, a szolgáltatás, az adatbázis és a VPN-átjáró
- NSG kényszeríti az alkalmazás és az adatbázis alhálózatok
- A helyszíni és az Azure közötti pont-pont VPN-kapcsolat

![A vész-Helyreállítási topológia oldalát bemutató képernyőkép](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>További olvasnivaló

- [Tervezése és megvalósítása Oracle-adatbázishoz az Azure](oracle-design.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Oracle Golden kapu beállítása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)


## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: Hozzon létre magas rendelkezésre állású virtuális gépek](../../linux/create-cli-complete.md)
- [Virtuális gép telepítése az Azure parancssori felület minták felfedezése](../../linux/cli-samples.md)
