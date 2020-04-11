---
title: Az Azure Firewall szabályfeldolgozási logikája
description: Az Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazásszabályokkal. A szabályok feldolgozása a szabálytípusnak megfelelően.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113435"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure Firewall szabályfeldolgozási logikája
NAT-szabályokat, hálózati szabályokat és alkalmazásokat konfigurálhat az Azure Firewall ben. A szabálygyűjtemények feldolgozása a szabálytípus szerint történik prioritási sorrendben, az alacsonyabb számok száma 100 és 65 000 között nagyobb. A szabálygyűjtemény neve csak betűket, számokat, aláhúzásokat, pontokat vagy kötőjeleket tartalmazhat. Betűvel vagy számmal kell kezdődnie, és betűvel, számmal vagy aláhúzásjellel kell végződnie. A név maximális hossza 80 karakter.

A legjobb, ha a szabálygyűjtemény prioritási számait kezdetben 100 lépésekben (100, 200, 300 stb.) helyben szeretné ellátni, így szükség esetén további szabálygyűjteményeket adhat hozzá.

> [!NOTE]
> Ha engedélyezi a fenyegetésfelderítésen alapuló szűrést, ezek a szabályok a legmagasabb prioritásúak, és mindig először kerülnek feldolgozásra. A fenyegetés-intelligencia szűrés megtagadhatja a forgalmat, mielőtt bármely konfigurált szabályok feldolgozása. További információ: [Azure Firewall fenyegetésintelligencia-alapú szűrés.](threat-intel.md)

## <a name="outbound-connectivity"></a>Kimenő kapcsolat

### <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai

Ha hálózati és alkalmazásszabályokat konfigurál, a hálózati szabályok prioritási sorrendben kerülnek alkalmazásra az alkalmazásszabályok előtt. A szabályok megszűnnek. Ha tehát egyezést talál egy hálózati szabályban, a rendszer nem dolgoz fel más szabályokat.  Ha nincs hálózati szabály egyezés, és ha a protokoll HTTP, HTTPS vagy MSSQL, akkor a csomagot az alkalmazásszabályok prioritási sorrendben értékelik ki. Ha a rendszer továbbra sem talál egyezést, a csomag kiértékelése az [infrastruktúraszabály-gyűjteményalapján](infrastructure-fqdns.md)történik. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="inbound-connectivity"></a>Bejövő kapcsolat

### <a name="nat-rules"></a>NAT-szabályok

A bejövő internetkapcsolat az oktatóanyagban leírtak szerint a célhálózati címfordítás (DNAT) konfigurálásával [engedélyezhető: Szűrje a bejövő forgalmat az Azure Firewall DNST szolgáltatással az Azure Portal használatával.](tutorial-firewall-dnat.md) A Hálózati etta-szabályok a hálózati szabályok előtt prioritásként kerülnek alkalmazásra. Ha egyezést talál, a fordítást engedélyező implicit megfelelő hálózati szabály hozzáadódik. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak.

Alkalmazásszabályok nem vonatkoznak a bejövő kapcsolatokra. Ezért ha szűrni szeretné a bejövő HTTP/S-forgalmat, használja a WebApplication Firewall (WAF) szolgáltatást. További információ: [Mi az Azure webalkalmazás-tűzfal?](../web-application-firewall/overview.md)

## <a name="examples"></a>Példák

A következő példák a szabálykombinációk némelyikének eredményeit mutatják be.

### <a name="example-1"></a>1. példa

A google.com való kapcsolat egy megfelelő hálózati szabály miatt engedélyezett.

**Hálózati szabály**

- Művelet: Engedélyezés


|név  |Protocol (Protokoll)  |Forrás típusa  |Forrás  |Cél típusa  |Rendeltetési hely  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Web engedélyezése     |TCP|IP-cím|*|IP-cím|*|80 443

**Alkalmazási szabály**

- Művelet: Megtagadás

|név  |Forrás típusa  |Forrás  |protokoll:port|Cél teljes tartományn-ok|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |IP-cím|*|http:80,https:443|google.com

**Eredmény**

A kapcsolat google.com engedélyezett, mert a csomag megfelel a *webhálózati engedélyezési* szabálynak. A szabály feldolgozása ezen a ponton leáll.

### <a name="example-2"></a>2. példa

Az SSH-forgalom megtagadva, mert egy magasabb prioritású Hálózati szabály *gyűjteményének megtagadása* blokkolja azt.

**Hálózati szabálygyűjtemény 1**

- Név: Engedélyezési gyűjtés
- Prioritás: 200
- Művelet: Engedélyezés

|név  |Protocol (Protokoll)  |Forrás típusa  |Forrás  |Cél típusa  |Rendeltetési hely  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Engedélyezés-SSH     |TCP|IP-cím|*|IP-cím|*|22

**Hálózati szabály gyűjtemény 2**

- Név: Megtagadás-gyűjtemény
- Prioritás: 100
- Művelet: Megtagadás

|név  |Protocol (Protokoll)  |Forrás típusa  |Forrás  |Cél típusa  |Rendeltetési hely  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Megtagadás-SSH     |TCP|IP-cím|*|IP-cím|*|22

**Eredmény**

Az SSH-kapcsolatok at a rendszer megtagadja, mert egy magasabb prioritású hálózati szabálygyűjtemény blokkolja azt. A szabály feldolgozása ezen a ponton leáll.

## <a name="rule-changes"></a>Szabályváltozások

Ha módosítja a szabályt, hogy megtagadja a korábban engedélyezett forgalmat, a vonatkozó meglévő munkamenetek elmennek.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [telepítheti és konfigurálhatja az Azure-tűzfalat.](tutorial-firewall-deploy-portal.md)