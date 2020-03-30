---
title: Azure VMware-megoldás – DNS továbbítás magánfelhőből a helyszíni adatokra
description: Ez a témakör azt ismerteti, hogy miként engedélyezheti a CloudSimple private cloud DNS-kiszolgálószámára a helyszíni erőforrások keresési adatait
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961126"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>A CloudSimple magánfelhő-alapú DNS-kiszolgálóinak engedélyezése a helyszíni erőforrások DNS-keresési adatainak továbbítására a DNS-kiszolgálók számára

A magánfelhő-DNS-kiszolgálók a DNS-kiszolgálóknak továbbíthatják a DNS-erőforrások DNS-kereséseit.  A névkérés engedélyezése lehetővé teszi, hogy a Private Cloud vSphere-összetevők a helyszíni környezetben futó szolgáltatásokat megkeressék, és teljesen minősített tartománynevek (FQDN) használatával kommunikáljanak velük.

## <a name="scenarios"></a>Forgatókönyvek 

A helyszíni DNS-kiszolgáló DNS-keresése lehetővé teszi a magánfelhő használatát a következő esetekben:

* A Magánfelhő használata vész-helyreállítási beállításként a helyszíni VMware-megoldáshoz
* A helyszíni Active Directory használata a magánfelhő vSphere identitásforrásaként
* A HCX használata virtuális gépek helyszíni és magánfelhőbe való áttelepítéséhez

## <a name="before-you-begin"></a>Előkészületek

A DNS-továbbítás működéséhez a magánfelhő-hálózatról a helyszíni hálózatra hálózati kapcsolatnak kell lennie.  A hálózati kapcsolatot a következő konkra állíthatja be:

* [Csatlakozás a helyszíni és a CloudSimple szolgáltatáshoz az ExpressRoute használatával](on-premises-connection.md)
* [Helyek közötti VPN-átjáró beállítása](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

A DNS-továbbítás működéséhez tűzfalportokat kell megnyitni ezen a kapcsolaton.  A használt portok az 53-as TCP-port vagy az 53-as UDP-port.

> [!NOTE]
> Ha helyek közötti VPN-t használ, a helyszíni DNS-kiszolgáló alhálózatát a helyszíni előtagok részeként kell hozzáadni.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>DNS-továbbítás kérése a magánfelhőből a helyszíni környezetbe

A DNS-továbbítás engedélyezéséhez a magánfelhőből a helyszíni környezetbe, nyújtson be [támogatási kérelmet,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)amely a következő információkat tartalmazza.

* Probléma típusa: **Műszaki**
* Előfizetés: **Előfizetés, ahol a CloudSimple szolgáltatás telepítve van**
* Szolgáltatás: **VMware megoldás a CloudSimple-től**
* Probléma típusa: **Tanácsadó vagy Hogyan ...**
* Probléma altípus: **Segítségre van szüksége az NW-vel kapcsolatban**
* Adja meg a helyszíni tartomány tartománynevét a részletek ablaktáblán.
* Adja meg azoknak a helyszíni DNS-kiszolgálóknak a listáját, amelyekre a rendszer a privát felhőből továbbítja a keresési adatokat a részletek ablaktáblán.

## <a name="next-steps"></a>További lépések

* [További információ a helyszíni tűzfal-konfigurációról](on-premises-firewall-configuration.md)
* [Helyszíni DNS-kiszolgáló konfigurációja](on-premises-dns-setup.md)
