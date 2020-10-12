---
title: Az Azure Firewall szabályfeldolgozási logikája
description: Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazási szabályokkal. A szabályok feldolgozása a szabály típusa szerint történik.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 84110e749dac9267e994385aa5f6d05e3ba224a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087543"
---
# <a name="configure-azure-firewall-rules"></a>Azure Firewall szabályok konfigurálása
Megadhatja a NAT-szabályokat, a hálózati szabályokat és az alkalmazásokra vonatkozó szabályokat a Azure Firewall. A szabályok gyűjteményeit a rendszer a szabály típusa szerint dolgozza fel a prioritási sorrendben, a számok számát pedig a 100 – 65 000 értéknél nagyobb számra csökkenti. A szabálygyűjtemény neve csak betűket, számokat, aláhúzásokat, pontokat és kötőjeleket tartalmazhat. Betűvel vagy számmal kell kezdődnie, és betűvel, számmal vagy aláhúzással kell végződnie. A név maximális hossza 80 karakter.

Először a szabályok gyűjtésének prioritási számát 100-os (100, 200, 300 stb.) értékre kell felvennie, így szükség esetén további szabálygyűjtemény hozzáadására van lehetőség.

> [!NOTE]
> Ha engedélyezi a fenyegetést jelentő intelligencia-alapú szűrést, ezek a szabályok a legmagasabb prioritást jelentik, és a rendszer mindig feldolgozza azokat. A fenyegetés-intelligencia szűrése megtagadhatja a forgalmat a konfigurált szabályok feldolgozása előtt. További információ: [Azure Firewall Threat Intelligence-based Filtering](threat-intel.md).

## <a name="outbound-connectivity"></a>Kimenő kapcsolat

### <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai

A hálózati szabályok és az alkalmazás szabályainak konfigurálásakor a hálózati szabályok prioritási sorrendben lesznek alkalmazva az alkalmazás szabályai előtt. A szabályok leállnak. Így ha a rendszer egyezést talál egy hálózati szabályban, a rendszer nem dolgozza fel más szabályokat.  Ha nincs megfelelő hálózati szabály, és ha a protokoll HTTP, HTTPS vagy MSSQL, akkor a rendszer a csomagot prioritási sorrendben értékeli ki az alkalmazás szabályai alapján. Ha még mindig nem található egyezés, akkor a rendszer kiértékeli a csomagot az [infrastruktúra-szabálygyűjtemény](infrastructure-fqdns.md)alapján. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="inbound-connectivity"></a>Bejövő kapcsolat

### <a name="nat-rules"></a>NAT-szabályok

A bejövő internetkapcsolatot a célként megadott hálózati címfordítás (DNAT) konfigurálásával engedélyezheti [az oktatóanyag: a bejövő forgalom szűrése Azure Firewall DNAT a Azure Portal használatával](tutorial-firewall-dnat.md). A NAT-szabályok prioritásban lesznek alkalmazva a hálózati szabályok előtt. Ha talál egyezést, egy implicit megfelelő hálózati szabályt ad hozzá a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak.

Az alkalmazás szabályai nem alkalmazhatók a bejövő kapcsolatokra. Ha tehát a bejövő HTTP/S forgalmat szeretné szűrni, használja a webalkalmazási tűzfalat (WAF). További információ: [Mi az az Azure webalkalmazási tűzfal?](../web-application-firewall/overview.md)

## <a name="examples"></a>Példák

A következő példák a szabályok néhány kombinációjának eredményét mutatják be.

### <a name="example-1"></a>1\. példa

A google.com való kapcsolódás a megfelelő hálózati szabály miatt engedélyezett.

**Hálózati szabály**

- Művelet: Engedélyezés


|name  |Protokoll  |Forrás típusa  |Forrás  |Cél típusa  |Cél címe  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Engedélyezés – web     |TCP|IP-cím|*|IP-cím|*|80 443

**Alkalmazási szabály**

- Művelet: Megtagadás

|name  |Forrás típusa  |Forrás  |Protokoll: Port|Cél teljes tartománynevek|
|---------|---------|---------|---------|----------|----------|
|Megtagadás – Google     |IP-cím|*|http: 80, https: 443|google.com

**Eredmény**

A google.com-kapcsolat engedélyezve van, mert a csomag megfelel az *Allow-web* Network szabálynak. Ezen a ponton a szabályok feldolgozása leáll.

### <a name="example-2"></a>2\. példa

Az SSH-forgalmat a rendszer megtagadja, mert egy magasabb prioritású hálózati szabálygyűjtemény *tiltja* azt.

**Hálózati szabálygyűjtemény 1. gyűjteménye**

- Név: Allow-Collection
- Prioritás: 200
- Művelet: Engedélyezés

|name  |Protokoll  |Forrás típusa  |Forrás  |Cél típusa  |Cél címe  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Engedélyezés – SSH     |TCP|IP-cím|*|IP-cím|*|22

**Hálózati szabályok gyűjteménye 2**

- Név: megtagadás – gyűjtemény
- Prioritás: 100
- Művelet: Megtagadás

|name  |Protokoll  |Forrás típusa  |Forrás  |Cél típusa  |Cél címe  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Megtagadás – SSH     |TCP|IP-cím|*|IP-cím|*|22

**Eredmény**

Az SSH-kapcsolatok megtagadva, mert egy magasabb prioritású hálózati szabálygyűjtemény blokkolja azt. Ezen a ponton a szabályok feldolgozása leáll.

## <a name="rule-changes"></a>Szabály módosításai

Ha módosít egy szabályt a korábban engedélyezett forgalom megtagadására, a rendszer minden kapcsolódó meglévő munkamenetet elvet.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).