---
title: Korlátozza az ügyfél IP-címek – az Azure App Service |} A Microsoft Docs
description: Az IP-korlátozások az Azure App Service használatával
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 7/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 01c948bbfd1883e24dc161d46ba53aef42af0f1d
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250695"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Statikus IP-korlátozások az Azure App Service-ben #

Az IP-korlátozások lehetővé teszik a rendezett lista engedélyezni vagy letiltani az IP-címek, amelyek jogosultak hozzáférni az alkalmazáshoz prioritást határozza meg. Az engedélyezési listán lehetnek IPv4 és IPv6 típusú címek. Ha egy vagy több bejegyzés, nincs, akkor egy implicit összes elutasítása, hogy a lista végén létezik. 

Az IP-korlátozások funkció működik az összes App Service-ben üzemeltetett vállalatokra, többek között; webalkalmazások, api-alkalmazások, a linux-alkalmazások, linux-tárolóalkalmazásokról és funkciók. 

Az alkalmazás egy kérelmet történik, amikor az az IP-címet az IP-korlátozások listája képest értékeli ki. Ha a cím nem engedélyezett a hozzáférés a listájának szabályai alapján, a szolgáltatás válaszol-e az egy [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot.

Az IP-korlátozások képesség a App Service-ben előtér-szerepkörök, amelyek a feldolgozó gazdagépeket, ahol a kódja fut, a felsőbb rétegbeli van megvalósítva. Ezért az IP-korlátozások lényegében hálózati hozzáférés-vezérlési listák.  

![IP-korlátozások folyamat](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Egy ideig az IP-korlátozások funkció a portálon volt a ipSecurity funkció az IIS-ben a felső szintű rétege. Az aktuális IP-korlátozások funkció nem egyezik. Az alkalmazás web.config belül ipSecurity továbbra is konfigurálhatja, de az előtér-alapú IP-korlátozások szabályok lépnek érvénybe, mielőtt forgalmat eléri az IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Hozzáadását és szerkesztését IP-korlátozási szabályok a portálon ##

Az IP-korlátozási szabály hozzáadása az alkalmazáshoz, a menü használatával nyissa meg a **hálózati**>**IP-korlátozások** , majd kattintson a **IP-korlátozások konfigurálása**

![Az App Service hálózatkezelési lehetőségekkel](media/app-service-ip-restrictions/ip-restrictions.png)  

Az IP-korlátozások felületről áttekintheti az alkalmazáshoz definiált IP-korlátozási szabályok listája.

![lista IP-korlátozások](media/app-service-ip-restrictions/ip-restrictions-browse.png)

A szabályok úgy lettek konfigurálva, ahogyan a képen is látható, ha az alkalmazás csak akkor fogadja el 131.107.159.0/24 érkező forgalmat, és bármely más IP-címről lenne elutasítja.

Kattintson a **[+] Hozzáadás** egy új IP-korlátozási szabály hozzáadása. Miután hozzáadja egy szabályt, akkor lépnek érvénybe azonnal. A szabályzatbeállítás a-tól prioritási sorrendben szabályok életbe lépnek. Van egy implicit tiltásával, amely érvényben van, akár egyetlen szabály felvétele után. 

![az IP-korlátozási szabály hozzáadása](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP-cím jelöléssel kell adni a CIDR-jelölésrendszerben az IPv4 és IPv6-címeket is. Egy pontos címet ad meg, ahol az első négy oktetben jelölik az IP-címe pedig tulajdonságot/32 a maszk 1.2.3.4/32 hasonló használhatja. Az összes cím IPv4 CIDR-jelölés 0.0.0.0/0. A CIDR-jelölés használatával kapcsolatos további információkért olvassa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Kattintson a minden sor egy meglévő IP-korlátozási szabály szerkesztését. Módosítások hatékony azonnal többek között a módosítások prioritási sorrendben.

![az IP-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/ip-restrictions-edit.png)

A szabály törléséhez kattintson a **...**  elemre a szabályt, majd kattintson **eltávolítása**. 

![IP-korlátozási szabály törlése](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Az IP-korlátozási szabályok szoftveres adatkezelési ##

Jelenleg nem áll fenn parancssori felület vagy PowerShell esetében az új IP-korlátozások funkciót, de az értékek állítható be manuálisan az alkalmazáskonfigurációt az erőforrás-kezelőben PUT művelet. Tegyük fel resources.azure.com használja, és szerkessze a ipSecurityRestrictions blokk hozzáadásához szükséges JSON. 

További információ a Resource Managerben a hely a következő:

Management.Azure.com/subscriptions/**előfizetés-azonosító**/resourceGroups/**erőforráscsoportok**/providers/Microsoft.Web/sites/**webalkalmazás neve**  /config/webes? API-version = 2018-02-01

A korábbi példa JSON szintaxisa:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],
