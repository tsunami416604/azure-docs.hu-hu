---
title: Azure VMware-megoldás – DNS-továbbítás a privát felhőből a helyi környezetbe
description: Ismerteti, hogyan engedélyezheti a CloudSimple saját felhőalapú DNS-kiszolgálójának a helyszíni erőforrások keresésének továbbítását
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76961126"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>A CloudSimple-alapú privát Felhőbeli DNS-kiszolgálók engedélyezése a helyszíni erőforrások DNS-keresésének továbbítására a DNS-kiszolgálókra

A saját felhőalapú DNS-kiszolgálók továbbítják a DNS-keresést bármely helyszíni erőforráshoz a DNS-kiszolgálókra.  A keresés engedélyezése lehetővé teszi, hogy a saját felhőalapú vSphere összetevői bármilyen, a helyszíni környezetben futó szolgáltatást keressenek, és teljes tartománynevek (FQDN) használatával kommunikáljanak velük.

## <a name="scenarios"></a>Forgatókönyvek 

A helyi DNS-kiszolgáló DNS-keresésének továbbítása lehetővé teszi a saját felhő használatát a következő esetekben:

* A saját felhő használata vész-helyreállítási beállításként a helyszíni VMware-megoldáshoz
* Helyszíni Active Directory használata identitás forrásaként a saját felhőalapú vSphere
* A HCX használata a virtuális gépek helyszíniről saját felhőbe való áttelepítéséhez

## <a name="before-you-begin"></a>Előkészületek

A DNS-továbbítás működéséhez egy hálózati kapcsolatban kell lennie a saját felhőalapú hálózatáról a helyszíni hálózatra.  A hálózati kapcsolatok a használatával állíthatók be:

* [Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával](on-premises-connection.md)
* [Helyek közötti VPN-átjáró beállítása](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

A DNS-továbbítás működéséhez a tűzfal portjait meg kell nyitni a kapcsolódáskor.  A használt portok a 53-as TCP-port vagy az 53-es UDP-port.

> [!NOTE]
> Ha helyek közötti VPN-t használ, a helyszíni DNS-kiszolgáló alhálózatát a helyszíni előtagok részeként kell hozzáadni.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>DNS-továbbítás kérése a privát felhőből a helyszíni rendszerbe

Ha engedélyezni szeretné a DNS-továbbítást a privát felhőből a helyszíni környezetbe, küldjön egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), amely a következő információkat biztosítja.

* Probléma típusa: **technikai**
* Előfizetés: **előfizetés, ahol a CloudSimple szolgáltatás telepítve van**
* Szolgáltatás: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **tanácsadó vagy hogyan...**
* Probléma altípusa: **segítségre van szüksége az NW-vel**
* Adja meg a helyszíni tartomány tartománynevét a részletek ablaktáblán.
* Adja meg azoknak a helyszíni DNS-kiszolgálóknak a listáját, amelyekhez a rendszer továbbítja a keresést a privát felhőből a részleteket tartalmazó ablaktáblán.

## <a name="next-steps"></a>További lépések

* [További információ a helyszíni tűzfal konfigurálásáról](on-premises-firewall-configuration.md)
* [Helyszíni DNS-kiszolgáló konfigurációja](on-premises-dns-setup.md)
