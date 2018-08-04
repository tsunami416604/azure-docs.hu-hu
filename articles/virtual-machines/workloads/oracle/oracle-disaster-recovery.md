---
title: Az Oracle vész-helyreállítási helyzetekre az Azure-környezet áttekintése |} A Microsoft Docs
description: Az Oracle Database 12c adatbázis az Azure-környezetben a vész-helyreállítási helyzetekre
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 9f525e68502e32a3f9c7e7cebe6d45627f9077c3
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495027"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Az Azure-környezet egy Oracle Database 12c adatbázis vészhelyreállítása

## <a name="assumptions"></a>Előfeltételek

- Hogy Oracle Data Guard tervezési és az Azure-környezetek megismerése.


## <a name="goals"></a>Célok
- Tervezze meg a topológia és a vész-helyreállítási követelményeknek megfelelő konfiguráció.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>1. forgatókönyv: Elsődleges és a DR webhelyek az Azure-ban

Egy ügyfél rendelkezik egy Oracle adatbázis-készlet mentése az elsődleges helyen. A DR-hely van egy másik régióban. Az ügyfél Oracle Data Guard használja a helyek közötti gyors helyreállítás. Az elsődleges hely is rendelkezik egy másodlagos adatbázist, jelentéskészítési és egyéb felhasználásra. 

### <a name="topology"></a>Topológia

Itt található egy összefoglaló az Azure beállítása:

- Két hely (elsődleges hely és a DR-webhelyként)
- Két virtuális hálózat
- Két Oracle Data Guard (elsődleges és készenléti) adatbázisok
- Két Oracle-adatbázisok Golden kapu vagy Data Guard (csak az elsődleges hely)
- Két alkalmazásszolgáltatások, egy elsődleges és a DR webhelyen egy
- Egy *rendelkezésre állási csoport,* használt adatbázis- és szolgáltatás, az elsődleges helyen
- Minden egyes helyen, amelynél korlátozza a hozzáférést a magánhálózaton, így csak jelentkezzen be egy rendszergazdának egy jumpbox
- A jumpbox, alkalmazás szolgáltatása, adatbázis és VPN-átjáró külön alhálózatokon
- NSG-t kényszeríti az alkalmazás- és adatbázis-alhálózatok

![A DR-topológia oldalát bemutató képernyőkép](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>2. forgatókönyv: Helyszíni elsődleges hely és a DR hely az Azure-ban

Egy ügyfél rendelkezik egy helyszíni Oracle-adatbázis beállítása (elsődleges hely). A DR-hely van az Azure-ban. Ezek a helyek közötti gyors helyreállítás Oracle Data Guard használható. Az elsődleges hely is rendelkezik egy másodlagos adatbázist, jelentéskészítési és egyéb felhasználásra. 

Kétféleképpen a telepítő.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>1. módszer: A helyszíni és az Azure nyitott TCP-portok a tűzfalon igénylő közti közvetlen kapcsolatokon keresztül 

Közvetlen kapcsolat nem ajánlott, mert a külvilág számára a TCP-portok szolgáltatnak.

#### <a name="topology"></a>Topológia

Következő található egy összefoglaló az Azure beállítása:

- Egy DR-hely 
- Egy virtuális hálózat
- Egy Oracle-adatbázist a Data Guard (aktív)
- A DR-helyen több alkalmazásszolgáltatás
- Egy jumpboxba, amely korlátozza a hozzáférést a magánhálózaton, így csak jelentkezzen be egy rendszergazda
- A jumpbox, alkalmazás szolgáltatása, adatbázis és VPN-átjáró külön alhálózatokon
- NSG-t kényszeríti az alkalmazás- és adatbázis-alhálózatok
- Egy NSG/házirendszabály, hogy a bejövő TCP-portot 1521-es (vagy egy felhasználó által definiált port)
- Egy NSG/házirendszabály korlátozása csak az IP-cím/címek helyszíni (DB vagy alkalmazás) a virtuális hálózat elérését

![A DR-topológia oldalát bemutató képernyőkép](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>2. módszer: Site-to-site VPN
Site-to-site VPN emellett akkor esetleg jobb megközelítés. VPN beállításával kapcsolatos további információkért lásd: [virtuális hálózat létrehozása a CLI-vel Site-to-Site VPN-kapcsolattal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topológia

Következő található egy összefoglaló az Azure beállítása:

- Egy DR-hely 
- Egy virtuális hálózat 
- Egy Oracle-adatbázist a Data Guard (aktív)
- A DR-helyen több alkalmazásszolgáltatás
- Egy jumpboxba, amely korlátozza a hozzáférést a magánhálózaton, így csak jelentkezzen be egy rendszergazda
- Külön alhálózatokon vannak egy jumpboxba, alkalmazás szolgáltatása, adatbázis és VPN-átjáró
- NSG-t kényszeríti az alkalmazás- és adatbázis-alhálózatok
- A helyszíni és az Azure között helyek közötti VPN-kapcsolat

![A DR-topológia oldalát bemutató képernyőkép](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>További olvasnivaló

- [Tervezzenek és valósítsanak meg az Oracle-adatbázishoz az Azure-ban](oracle-design.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Oracle Golden kapu beállítása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](oracle-backup-recovery.md)


## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [Ismerje meg a virtuális gép üzembe helyezés az Azure CLI-minták](../../linux/cli-samples.md)
