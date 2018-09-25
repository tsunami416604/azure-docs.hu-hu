---
title: Az Office 365 vezérlősík az Azure virtuális WAN
description: Ismerje meg a virtuális WAN Office 365 vezérlősík.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992676"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Az Office 365 vezérlősík a virtuális WAN

Virtuális WAN ügyfelek SDWAN jelölje ki az eszközöket az O365 Internet kiscsoportos szabályzatokat megbízható adatforgalomhoz konfigurálhat az Azure Portalon. Ez lehetővé teszi:
- Adja meg a Microsoft hálózati közeli felhasználói jogosultságot ad az optimális felhasználói élmény O365 forgalmat.
- Ezzel elkerülheti a forgalom vissza-behúzásának és haj pining, így a WAN költségeket mentése.
- Biztosítása az Office 365 kapcsolat elveit.

## <a name="faqs"></a>Gyakori kérdések
### <a name="what-is-the-customer-benefit"></a>Mi az az ügyfél előny?
Ezzel a funkcióval virtuális WAN az ügyfelek most már adhatja meg, amelyek megbíznak a közvetlen internet kiscsoportos Office 365-forgalom kategória. Megbízható Office 365 forgalom lesz hálózatukról proxyk és a legközelebbi Microsoft POP közvetlenül a felhasználó helyről útvonal. Ezzel elkerülhető a forgalom vissza-behúzásának és haj pining, így optimális felhasználói élmény biztosítása és mentése a WAN költségeket. 

### <a name="what-are-the-office-365-traffic-categories"></a>Mik azok az Office 365-forgalom kategória?
Az Office 365-végpontok hálózati címek és alhálózatok képviseli. Lehet, hogy végpontok URL-címeket, IP-cím vagy IP-címtartományokat. URL-címeket lehet például egy teljes *account.office.net*, vagy egy helyettesítő karaktert tartalmazó URL-címet, például **. office365.com*. Három kategóriába - végpontok elkülönítik **optimalizálása**, **engedélyezése**, és **alapértelmezett**azok kritikusság alapján. További részleteket a végpont kategóriák [Itt](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Mely Office 365-forgalom kategória közvetlen internet kiscsoportos a Microsoft által ajánlott?
A **optimalizálása** kategória a kritikus fontosságú hálózati végpontokat, és kötelező SSL break megkerülése, és vizsgálja meg, és más hálózati biztonsági eszközöket. Közvetlen Internet kimenő felhasználók közelében kell rendelkeznie. Ezeket a végpontokat, amelyek a leginkább bizalmas hálózati teljesítmény, a késés és a rendelkezésre állási Office 365-forgatókönyvek képviseli. Ebbe a kategóriába URL-címek és IP-alhálózatok egy meghatározott készletével dedikált Office 365 alapvető számítási feladatok, például az Exchange Online, SharePoint online-hoz, a Skype vállalati Online és a Microsoft Teams-kulcs (sorrendjéről ~ 10) kis készletét tartalmazza. 

A **engedélyezése** kategória ajánlott közvetlen Internet kimenő is. Lehetővé teszi a hálózati forgalmat, ha bizonyos hálózati késés működését. Az engedélyezési és optimalizálása kategóriákban végpontok összes Microsoft-adatközpontokban üzemeltetjük, és felügyelt Office 365 részeként. Az alapértelmezett kategória egy alapértelmezett internetes kimenő hely lehet irányítani és nem használatához szükséges közvetlen Internet kimenő forgalom vagy az SSL-break megkerülése, vizsgálja meg az eszközök.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Hogyan állíthatok be virtuális WAN saját Office 365 szabályzatokat?
Szabályzatok használatával engedélyezheti a **virtuális WAN** -> **beállítások** -> **konfigurációs** fülre. Itt adhatja meg az Office 365-adatforgalom közvetlen internet kiscsoportos előnyben részesített kategóriáit.

![Office 365 vezérlősík konfigurálása a virtuális WAN](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Hogyan működik ez?

1.  Office 365 forgalom belép a Microsoft hálózati közeli felhasználói jogosultságot ad az optimális működés érdekében.
2.  Útválasztási házirendek SDWAN használnak fel. Ezután megkerüli a megbízható kategóriák biztonsági-proxyt, és elvégzi a helyi közvetlen kiscsoportos ezekben a kategóriákban.
3.  Vissza behúzásának és a forgalom haj pining elkerülhetők a WAN költségeket mentése.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Melyik partner eszközök támogatják a virtuális WAN keresztül?
Jelenleg a Citrix támogatja ezeket a szabályzatokat, virtuális WAN keresztül.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Mi történik, a Fennmaradó kategóriák (nem megbízható) O365 forgalom?
Fennmaradó O365 forgalmat az ügyfelek internetes forgalom alapértelmezés szerint a követi.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Mi történik, ha rendelkezik már meg saját Office 365-házirendek saját SDWAN szolgáltató keresztül?
Ha megadja a SDWAN felhasználói felület és Azure virtuális WAN szabályzatokat, a virtuális WAN-ban beállított szabályzatoknak elsőbbséget élvez.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [virtuális WAN](virtual-wan-about.md).