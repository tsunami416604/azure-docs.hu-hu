---
title: Korlátozza az ügyfél IP-címek – az Azure App Service |} A Microsoft Docs
description: Korlátozza a hozzáférést az Azure App Service használatával
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357622"
---
# <a name="azure-app-service-static-access-restrictions"></a>Az Azure App Service statikus hozzáférési korlátozások #

Korlátozza a hozzáférést lehetővé teszik a rendezett lista engedélyezni vagy letiltani az IP-címek, amelyek jogosultak hozzáférni az alkalmazáshoz prioritást határozza meg. Az engedélyezési listán lehetnek IPv4 és IPv6 típusú címek. Ha egy vagy több bejegyzés, nincs, akkor egy implicit összes elutasítása, hogy a lista végén létezik.

A hozzáférési korlátozásokat funkció működik az összes App Service-ben üzemeltetett vállalatokra, többek között; webalkalmazások, API-alkalmazások, a Linux-alkalmazások, Linux-tárolóalkalmazásokról és funkciók.

Amikor kérelem érkezik, az alkalmazáshoz, az IP-címét a hozzáférési korlátozásokat lista alapján értékeli ki. Ha a cím nem engedélyezett a hozzáférés a listájának szabályai alapján, a szolgáltatás válaszol-e az egy [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot.

A hozzáférési korlátozásokat funkció a App Service-ben előtér-szerepkörök, amelyek a feldolgozó gazdagépeket, ahol a kódja fut, a felsőbb rétegbeli van megvalósítva. Ezért korlátozza a hozzáférést lényegében hálózati hozzáférés-vezérlési listák.  

![hozzáférési korlátozások folyamat](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Ideje a hozzáférési korlátozásokat funkció a portálon volt a ipSecurity funkció az IIS-ben a felső szintű rétege. A jelenlegi hozzáférési korlátozások funkció nem egyezik. Az alkalmazás web.config belül ipSecurity továbbra is konfigurálhatja, de az előtér-alapján korlátozza a hozzáférést szabályok lépnek érvénybe, mielőtt forgalmat eléri az IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Hozzáadását és szerkesztését a hozzáférés-korlátozási szabályok a portálon ##

Egy hozzáférés-korlátozási szabály hozzáadása az alkalmazáshoz, a menü használatával nyissa meg a **hálózati**>**korlátozza a hozzáférést** , majd kattintson a **hozzáférési korlátozások konfigurálása**

![Az App Service hálózatkezelési lehetőségekkel](media/app-service-ip-restrictions/ip-restrictions.png)  

A hozzáférési korlátozások felhasználói felületen áttekintheti az alkalmazáshoz definiált hozzáférési korlátozási szabályok listája.

![lista korlátozza a hozzáférést](media/app-service-ip-restrictions/ip-restrictions-browse.png)

A szabályok úgy lettek konfigurálva, ahogyan a képen is látható, ha az alkalmazás csak akkor fogadja el 131.107.159.0/24 érkező forgalmat, és bármely más IP-címről lenne elutasítja.

Kattintson a **[+] Hozzáadás** egy új hozzáférés-korlátozási szabály hozzáadása. Miután hozzáadja egy szabályt, akkor lépnek érvénybe azonnal. A szabályzatbeállítás a-tól prioritási sorrendben szabályok életbe lépnek. Van egy implicit tiltásával, amely érvényben van, akár egyetlen szabály felvétele után.

![egy hozzáférés-korlátozási szabály hozzáadása](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP-cím jelöléssel kell adni a CIDR-jelölésrendszerben az IPv4 és IPv6-címeket is. Egy pontos címet ad meg, ahol az első négy oktetben jelölik az IP-címe pedig tulajdonságot/32 a maszk 1.2.3.4/32 hasonló használhatja. Az összes cím IPv4 CIDR-jelölés 0.0.0.0/0. A CIDR-jelölés használatával kapcsolatos további információkért olvassa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Kattintson a minden sor egy meglévő hozzáférés-korlátozási szabály szerkesztése. Módosítások hatékony azonnal többek között a módosítások prioritási sorrendben.

![egy hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/ip-restrictions-edit.png)

A szabály törléséhez kattintson a **...**  elemre a szabályt, majd kattintson **eltávolítása**.

![hozzáférés-korlátozási szabály törlése](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Üzembe helyezés hozzáférést a következő lapon is korlátozhatja. Adja hozzá, szerkeszteni vagy törölni, az egyes szabályok, kövesse ugyanazt a fenti.

![lista korlátozza a hozzáférést](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Hozzáférési korlátozás szabályok szoftveres kezelése ##

Jelenleg nem áll fenn parancssori felület vagy PowerShell esetében az új hozzáférési korlátozások funkciót, de az értékek állítható be manuálisan az alkalmazáskonfigurációt az erőforrás-kezelőben PUT művelet. Tegyük fel resources.azure.com használja, és szerkessze a ipSecurityRestrictions blokk hozzáadásához szükséges JSON.

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

## <a name="function-app-ip-restrictions"></a>Függvény alkalmazás IP-korlátozások

Az IP-korlátozások mindkét Function Apps az App Service-csomagok ugyanazokkal a funkciókkal érhetők el. Vegye figyelembe, hogy IP-engedélyezése korlátozások letiltja a bármely nem engedélyezett IP-címek a portál Kódszerkesztő.

[További információk itt](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)