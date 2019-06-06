---
title: Korlátozza a hozzáférést – az Azure App Service |} A Microsoft Docs
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
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e408439c4868a9fadfd15ab8ae303b2d881c481e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494269"
---
# <a name="azure-app-service-access-restrictions"></a>Az Azure App Service-ben korlátozza a hozzáférést #

Korlátozza a hozzáférést az alkalmazáshoz való hálózati hozzáférés vezérlő prioritású rendezett engedélyezni vagy letiltani listájának definiálását teszi lehetővé. A lista IP-címek vagy az Azure virtuális hálózat alhálózataiban tartalmazhatnak. Ha egy vagy több bejegyzés, van egy implicit "összes elutasítása", amely létezik a lista végén.

A hozzáférési korlátozásokat funkció együttműködik az összes App Service-ben üzemeltetett munkahelyi betölti, beleértve a; webalkalmazások, API-alkalmazások, a Linux-alkalmazások, Linux-tárolóalkalmazásokról és funkciók.

Amikor kérelem érkezik, az alkalmazáshoz, a feladó címe az IP-cím szabályok a hozzáférési korlátozások listáján képest értékeli ki. Ha a feladó címe konfigurált szolgáltatásvégpontokkal Microsoft.Web alhálózatán, majd a forrásoldali alhálózat rendszer összehasonlítja a virtuális hálózati szabályok a hozzáférési korlátozások listáján. Ha a cím nem engedélyezett a hozzáférés a listájának szabályai alapján, a szolgáltatás válaszol-e az egy [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot.

A hozzáférési korlátozások funkciójával a App Service-ben előtér-szerepkörök, amelyek a feldolgozó gazdagépeket, ahol a kódja fut, a felsőbb rétegbeli van megvalósítva. Ezért korlátozza a hozzáférést lényegében hálózati hozzáférés-vezérlési listák.

Lehetővé teszi a webes alkalmazás való hozzáférés korlátozása egy Azure virtuális hálózat (VNet) nevezzük [szolgáltatásvégpontokat][serviceendpoints]. A Szolgáltatásvégpontok lehetővé teszi több-bérlős szolgáltatás való hozzáférés korlátozása a kiválasztott alhálózat. Engedélyezni kell a mind a hálózati oldalon, valamint a szolgáltatás, amely a rendszer engedélyez. Nem működik az App Service Environment-környezetben üzemeltetett alkalmazások korlátozzák a forgalmat.  Ha Ön App Service Environment-környezetben, szabályozhatja a hozzáférést az alkalmazás IP-cím szabályokkal.

![hozzáférési korlátozások folyamat](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Hozzáadását és szerkesztését a hozzáférés-korlátozási szabályok a portálon ##

Egy hozzáférés-korlátozási szabály hozzáadása az alkalmazáshoz, a menü használatával nyissa meg a **hálózati**>**korlátozza a hozzáférést** , majd kattintson a **hozzáférési korlátozások konfigurálása**

![Az App Service hálózatkezelési lehetőségekkel](media/app-service-ip-restrictions/access-restrictions.png)  

A hozzáférési korlátozások felhasználói felületen áttekintheti az alkalmazáshoz definiált hozzáférési korlátozási szabályok listája.

![lista korlátozza a hozzáférést](media/app-service-ip-restrictions/access-restrictions-browse.png)

A listában jelennek meg az összes, amelyek az alkalmazás a jelenlegi korlátozások. Ha az alkalmazás a virtuális hálózati korlátozások, a táblázatban jelennek meg, ha a Szolgáltatásvégpontok engedélyezve vannak-e a Microsoft.Web. Ha az alkalmazás nincs meghatározott korlátozás, az alkalmazás bárhonnan elérhető lesz.  

Kattintson a **[+] Hozzáadás** egy új hozzáférés-korlátozási szabály hozzáadása. Miután hozzáadja egy szabályt, akkor lépnek érvénybe azonnal. A szabályzatbeállítás a-tól prioritási sorrendben szabályok életbe lépnek. Van egy implicit tiltásával, amely érvényben van, akár egyetlen szabály felvétele után.

### <a name="adding-ip-address-rules"></a>IP-cím szabályok hozzáadása

![az IP-hozzáférés-korlátozási szabály hozzáadása](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Szabályok létrehozásakor ki kell választania, engedélyezni vagy letiltani, valamint a szabály típusát. Biztosít hozzáférést a prioritás értéke, és mi csoportkezelést is szükségesek.  Igény szerint hozzáadhat egy nevet és leírást a szabályhoz.  

Állítsa be az IP-cím-alapú szabály, válasszon ki egy IPv4- vagy IPv6. IP-cím jelöléssel kell adni a CIDR-jelölésrendszerben az IPv4 és IPv6-címeket is. Egy pontos címet ad meg, ahol az első négy oktetben jelölik az IP-címe pedig tulajdonságot/32 a maszk 1.2.3.4/32 hasonló használhatja. Az összes cím IPv4 CIDR-jelölés 0.0.0.0/0. A CIDR-jelölés használatával kapcsolatos további információkért olvassa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

![egy virtuális hálózati hozzáférés-korlátozási szabály hozzáadása](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

A hozzáférés korlátozása a kiválasztott alhálózat, válassza ki a fajta virtuális hálózatot. Alatta fogja adhatnak meg az előfizetés, virtuális hálózat és engedélyezi vagy megtagadja a kívánt alhálózatot. Szolgáltatásvégpontok nem már engedélyezettek a Microsoft.Web a kiválasztott alhálózat, ha azt automatikusan engedélyezve lesz, kivéve, ha nem szeretne ehhez jelölőnégyzetet. A helyzetet, ahol szeretné engedélyezni az alkalmazást, de nem az alhálózat nagymértékben kapcsolódik, ha az engedélyek az alhálózat szolgáltatásvégpontjainak engedélyezéséhez, vagy nem rendelkezik. Ha valaki más engedélyezéséhez az alhálózat első van szüksége, jelölje be a jelölőnégyzetet, és az alkalmazás várható az alhálózaton található később engedélyezve a Szolgáltatásvégpontok konfigurálva van. 

A Szolgáltatásvégpontok az App Service Environment-környezetben futó alkalmazásokhoz való hozzáférés korlátozása nem használható. Ha az alkalmazás az App Service-környezet, szabályozhatja a hozzáférést az alkalmazáshoz, az IP-hozzáférési szabályok. 

A Szolgáltatásvégpontok használatakor konfigurálhatja az alkalmazás az Application Gateway átjárók vagy más WAF-eszközökkel. Többrétegű alkalmazásokat is képes konfigurálni, a biztonságos háttérrendszerek. Néhány lehetőséget sorol fel további részletekért olvassa el a [hálózati szolgáltatásait, és az App Service-ben](networking-features.md).

### <a name="managing-access-restriction-rules"></a>Hozzáférési korlátozás szabályok kezelése

Kattintson a minden sor egy meglévő hozzáférés-korlátozási szabály szerkesztése. Módosítások hatékony azonnal többek között a módosítások prioritási sorrendben.

![egy hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Amikor szerkeszti egy szabályt, egy IP-cím szabályt és egy virtuális hálózati szabályt közötti típusa nem módosítható. 

![egy hozzáférés-korlátozási szabály szerkesztése](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

A szabály törléséhez kattintson a **...**  elemre a szabályt, majd kattintson **eltávolítása**.

![hozzáférés-korlátozási szabály törlése](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="blocking-a-single-ip-address"></a>Egyetlen IP-cím blokkolása ##

Az első IP-korlátozási szabály hozzáadása, ha a szolgáltatás hozzáadja explicit **tiltásával** szabály 2147483647 prioritással. A gyakorlatban az explicit **tiltásával** szabály végrehajtott utolsó szabály lesz, és letiltja a hozzáférést az IP-címeket, amelyek nem kifejezetten engedélyezett használatával egy **engedélyezése** szabály.

A forgatókönyvhöz, ahol felhasználókat szeretne letilthatja egy egyetlen IP-cím vagy IP-Címblokk, de engedélyezése minden más hozzáférést, szükség hozzá explicit **mindent engedélyező** szabály.

![egyetlen ip-cím blokkolása](media/app-service-ip-restrictions/block-single-address.png)

### <a name="scm-site"></a>SCM helyet 

Amellett, hogy az alkalmazás elérése, korlátozhatja is hozzáférést az scm helyet, az alkalmazás által használt. Az scm helyet, az a web deploy-végpontot, és a Kudu konzol. Az scm helyet korlátozza a hozzáférést az alkalmazás hozzárendelését külön-külön, vagy ugyanaz, mint az alkalmazás és az scm helyet is be. Ha bejelöli a jelölőnégyzetet, ugyanazok a korlátozások vonatkoznak, mint az alkalmazások, minden a iktatni ki. Törölje a jelet a jelölőnégyzetből, ha a program bármilyen már korábban az scm helyet a beállítások lesznek alkalmazva. 

![lista korlátozza a hozzáférést](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

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

Az IP-korlátozások mindkét Function Apps az App Service-csomagok ugyanazokkal a funkciókkal érhetők el. IP-korlátozások engedélyezése letiltja a bármely nem engedélyezett IP-címek a portál Kódszerkesztő.

[További információk itt](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
