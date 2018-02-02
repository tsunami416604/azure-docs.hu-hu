---
title: "Hálózati integráció szempontjai integrált Azure verem rendszerek |} Microsoft Docs"
description: "Ismerje meg, mi mindent datacenter hálózati integráció az többcsomópontos Azure veremnek megfelelő tervezését."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: a198ff5fe7135e17301025d6a712236b76be0ede
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="network-connectivity"></a>Hálózati kapcsolat
Ez a cikk információival Azure verem hálózati infrastruktúra segítségével eldöntheti, hogyan Azure verem legjobb integrálhatók a meglévő hálózati környezethez. 

> [!NOTE]
> Az Azure oszlopból (például www.bing.com) külső DNS-nevek feloldására, meg kell adnia a DNS-kiszolgálók DNS-kérések továbbításához. Azure verem DNS követelményeivel kapcsolatos további információkért lásd: [Azure verem datacenter integrációs - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fizikai hálózati terv
Az Azure-verem megoldás rugalmas és magas rendelkezésre állású fizikai infrastruktúra a művelet és a szolgáltatások támogatásához szükséges. Az alábbi ábra a javasolt tervezési jelöli:

![Ajánlott Azure verem hálózati terv](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logikai hálózatok
Logikai hálózatok a mögöttes fizikai hálózati infrastruktúra absztrakciós jelölik. Szervezése és egyszerűsítése érdekében a gazdagépek, virtuális gépek és szolgáltatások hálózati feladatok használhatók. Logikai hálózatok létrehozása részeként a hálózati helyek adható meg a virtuális helyi hálózatokat (VLAN), IP-alhálózatok és a logikai hálózat minden fizikai helyen társított IP alhálózat-VLAN párok jönnek létre.

Az alábbi táblázat a logikai hálózatok és a kapcsolódó IPv4 alhálózati megadott tartományok figyelembe kell vennie:

| Logikai hálózat | Leírás | Méret | 
| -------- | ------------- | ------------ | 
| Nyilvános VIP | Egy kis készletét Azure verem szolgáltatások, a többi bérlő virtuális gépek által használt nyilvános IP-címet. Az Azure-verem infrastruktúrát a hálózati 32 címeit használja. Ha tervezi az App Service és az SQL erőforrás-szolgáltató, 7 több címet használja. | / 26 (62 gazdagépek) – /22 (1022 gazdagép)<br><br>Ajánlott = /24 (254 gazdagép) | 
| Kapcsoló-infrastruktúra | Pont-pont típusú IP-címek útválasztásra, dedikált felügyeleti felületek, és a kapcsoló rendelt visszacsatolási címek váltani. | /26 | 
| Infrastruktúra | Való kommunikációhoz használt Azure verem belső összetevőnél. | /24 |
| Saját | A tárolóhálózat és titkos virtuális IP-címek használata. | /24 | 
| BMC | A bmc-k, a fizikai állomáson folytatott kommunikációhoz használandó. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Hálózati infrastruktúra
A hálózati infrastruktúra Azure verem több logikai hálózatok, a kapcsolók konfigurált áll. Az alábbi ábrán látható, ezeket a logikai hálózatokat, és hogyan integrálható a-tor (TOR) alaplapi felügyeleti vezérlővel (BMC), és szegély (ügyfél hálózati) kapcsolók.

![Logikai hálózati diagram és kapcsoló kapcsolatok](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC hálózati
Ez a hálózat a felügyeleti hálózathoz csatlakozó összes az alaplapi felügyeleti vezérlők (más néven szolgáltatás processzorok, például iDRAC, iLO, iBMC, stb.) van kijelölve. Ha van ilyen, az a (HLH) hardver életciklus állomás a hálózaton található, és rendelkezhetnek OEM adott szoftver hardver karbantartás és/vagy a figyelés. 

### <a name="private-network"></a>Magánhálózat
A /24 (254 gazdagép IP-címekhez) hálózat az Azure-verem régió (nem bővíti ki a szegély kapcsoló eszközökre az Azure-verem régió túl) a saját, és két alhálózat oszlik:

- **Tárolóhálózat**. Egy /25 (126 gazdagép IP-címekhez) hálózati használatával támogatja a közvetlen tárolóhelyek és a Server Message Block (SMB) tárolási forgalom és a virtuális gép élő áttelepítés. 
- **Belső virtuális IP-hálózat**. A/25 hálózati csak belső dedikált virtuális IP-címek esetében az szoftveres terheléselosztóként üzemeljen.

### <a name="azure-stack-infrastructure-network"></a>Verem Azure infrastruktúra-hálózathoz
Ez/24 van számára kijelölt hálózat belső Azure verem összetevők, hogy kommunikál, és exchange-adatok egymás között. Ez az alhálózat elérhető IP-címet igényel, de tartják titokban megoldás hozzáférés-vezérlési listák (ACL) segítségével. Azt a szegély kapcsolók, kivéve egy kis méretű egyenértékű egy /27 tartomány túl irányíthatja át nem várt hálózati külső erőforrások és/vagy az internet eléréséhez szükséges néhány szolgáltatás által használatos. 

### <a name="public-infrastructure-network"></a>Nyilvános infrastruktúra-hálózathoz
Ez/27 hálózati a kis közé az Azure-verem infrastruktúra-alhálózat már említettük, nem igényel nyilvános IP-címek, de azt internetelérés NAT vagy transzparens Proxy keresztül. Ez a hálózat oszt ki a sürgős helyreállítási konzol rendszer (ERCS) a, a ERCS VM internet-hozzáférést igényel az Azure-bA regisztráció során, és a felügyeleti hálózathoz hibaelhárítási célból irányíthatóknak kell lenniük.

### <a name="public-vip-network"></a>Nyilvános virtuális IP-hálózat
A nyilvános virtuális IP-hálózati hozzá van rendelve a hálózati vezérlő Azure-készletben. Egy logikai hálózatot a kapcsoló nincs. A SLB címek készletét használja, és hozzárendeli/32 hálózatokra vonatkozó bérlői munkaterheléseket. A kapcsoló-útválasztási táblázat ezek 32 IP-cím van-e hirdetve BGP keresztül a rendelkezésre álló útvonalként. Ez a hálózat a külső érhető el vagy nyilvános IP-címet tartalmaz. Az Azure-verem infrastruktúra a nyilvános virtuális IP-hálózati legalább 8 címeket használ, amíg a többi bérlői virtuális gépek által használt. Az alhálózaton hálózati mérete között lehet (64 gazdagépek) /26 legalább /22 (1022 gazdagépek) legfeljebb, azt javasoljuk, hogy tervezi-e egy/24 hálózati.

### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózati kapcsoló
Ez/26 hálózati az alhálózatot, amely tartalmazza a point-to-point irányítható IP/30-as (2 gazdagép IP-címekhez) alhálózat és a loopbacks, amelyek dedikált/32-alhálózatok sávon kapcsoló felügyeleti és BGP-útválasztó azonosítója. Lehet, hogy ezt az IP-címek irányítható kívülről, a Azure verem megoldás az adatközponthoz, azokat a magán- vagy nyilvános IP-cím lehet.

### <a name="switch-management-network"></a>Kapcsoló felügyeleti hálózat
A /29 (6 gazdagép IP-címek) hálózati van kijelölve, a csatlakozás a kapcsolók a felügyeleti portokat. Engedélyezi a központi telepítés, a felügyeletet és hibaelhárítást a sávon kívüli hozzáférést. A fent említett kapcsoló infrastruktúra-hálózathoz alapján számítja ki.

## <a name="publish-azure-stack-services"></a>Azure verem szolgáltatásokat
Azure verem szolgáltatások felhasználók számára elérhetővé külső Azure veremből lesz szüksége. Az Azure verem hoz létre az infrastruktúra-szerepkörök különböző végpontok. Ezeket a végpontokat a nyilvános IP-címkészletből hozzárendelt virtuális IP-címmel. A DNS-bejegyzés jön létre a külső DNS-zónában, amelyek a központi telepítéskor megadott végpontok. A felhasználói portál például a DNS-állomás bejegyzés portál van hozzárendelve.  *&lt;régió >.&lt; teljesen minősített tartományneve >*.

### <a name="ports-and-urls"></a>Portok és URL-címek
Azure verem szolgáltatásokat (például a portálok, Azure Resource Manager-, DNS, stb.) külső hálózatok számára elérhető, engedélyeznie kell a fenti végpontokkal való bejövő forgalom az adott URL-címeket, portokat és protokollokat.
 
Központi telepítés, ahol a transzparens proxy kimenő kapcsolatot biztosítani a hagyományos proxykiszolgálót, engedélyeznie kell a adott portok és URL-címeket mindkét [bejövő](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) és [kimenő](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) kommunikációt. Idetartozik a portok és URL-címek identitás, piactér szindikálási, javítási és frissítési, regisztrációs és használati adatok.

## <a name="next-steps"></a>További lépések
[Szegély kapcsolat](azure-stack-border-connectivity.md)