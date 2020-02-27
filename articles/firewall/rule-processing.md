---
title: Az Azure Firewall szabályfeldolgozási logikája
description: Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazási szabályokkal. A szabályok feldolgozása a szabály típusa szerint történik.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 69c0c13c7027707cdadb2f1f1de9cc1655c9c625
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621895"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure Firewall szabályfeldolgozási logikája
Megadhatja a NAT-szabályokat, a hálózati szabályokat és az alkalmazásokra vonatkozó szabályokat a Azure Firewall. A szabályok feldolgozása a szabály típusa szerint történik. 

> [!NOTE]
> Ha engedélyezi a fenyegetést jelentő intelligencia-alapú szűrést, ezek a szabályok a legmagasabb prioritást jelentik, és a rendszer mindig feldolgozza azokat. A fenyegetés-intelligencia szűrése megtagadhatja a forgalmat a konfigurált szabályok feldolgozása előtt. További információ: [Azure Firewall Threat Intelligence-based Filtering](threat-intel.md).

## <a name="outbound"></a>Kimenő

### <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai

A hálózati szabályok és az alkalmazás szabályainak konfigurálásakor a hálózati szabályok prioritási sorrendben lesznek alkalmazva az alkalmazás szabályai előtt. A szabályok leállnak. Így ha a rendszer egyezést talál egy hálózati szabályban, a rendszer nem dolgozza fel más szabályokat.  Ha nincs megfelelő hálózati szabály, és ha a protokoll HTTP, HTTPS vagy MSSQL, akkor a rendszer az alkalmazás-szabályok alapján értékeli ki a csomagot a prioritási sorrendben. Ha még mindig nem található egyezés, akkor a rendszer kiértékeli a csomagot az [infrastruktúra-szabálygyűjtemény](infrastructure-fqdns.md)alapján. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="inbound"></a>Bejövő

### <a name="nat-rules"></a>NAT-szabályok

A bejövő internetkapcsolatot a célként megadott hálózati címfordítás (DNAT) konfigurálásával engedélyezheti [az oktatóanyag: a bejövő forgalom szűrése Azure Firewall DNAT a Azure Portal használatával](tutorial-firewall-dnat.md). A NAT-szabályok prioritásban lesznek alkalmazva a hálózati szabályok előtt. Ha talál egyezést, egy implicit megfelelő hálózati szabályt ad hozzá a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak.

A bejövő kapcsolatok esetében nem alkalmazhatók az alkalmazási szabályok. Ha tehát a bejövő HTTP/S forgalmat szeretné szűrni, használja a webalkalmazási tűzfalat (WAF). További információ: [Mi az az Azure webalkalmazási tűzfal?](../web-application-firewall/overview.md)

## <a name="examples"></a>Példák

A következő példák a szabályok néhány kombinációjának eredményét mutatják be.

### <a name="example-1"></a>1\. példa

A google.com való kapcsolódás a megfelelő hálózati szabály miatt engedélyezett.

**Hálózati szabály**

- Művelet: Engedélyezés


|név  |Protokoll  |Forrás típusa  |Forrás  |Cél típusa  |Cél címe  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Engedélyezés – web     |TCP|IP-cím|*|IP-cím|*|80,443

**Alkalmazási szabály**

- Művelet: megtagadás

|név  |Forrás típusa  |Forrás  |Protokoll: Port|Cél teljes tartománynevek|
|---------|---------|---------|---------|----------|----------|
|Megtagadás – Google     |IP-cím|*|http: 80, https: 443|google.com

**Találat**

A google.com-kapcsolat engedélyezve van, mert a csomag megfelel az *Allow-web* Network szabálynak. Ezen a ponton a szabályok feldolgozása leáll.

### <a name="example-2"></a>2\. példa

Az SSH-forgalmat a rendszer megtagadja, mert egy magasabb prioritású hálózati szabálygyűjtemény *tiltja* azt.

**Hálózati szabálygyűjtemény 1. gyűjteménye**

- Név: Allow-Collection
- Prioritás: 200
- Művelet: Engedélyezés

|név  |Protokoll  |Forrás típusa  |Forrás  |Cél típusa  |Cél címe  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Engedélyezés – SSH     |TCP|IP-cím|*|IP-cím|*|22

**Hálózati szabályok gyűjteménye 2**

- Név: megtagadás – gyűjtemény
- Prioritás: 100
- Művelet: megtagadás

|név  |Protokoll  |Forrás típusa  |Forrás  |Cél típusa  |Cél címe  |Célportok|
|---------|---------|---------|---------|----------|----------|--------|
|Megtagadás – SSH     |TCP|IP-cím|*|IP-cím|*|22

**Találat**

Az SSH-kapcsolatok megtagadva, mert egy magasabb prioritású hálózati szabálygyűjtemény blokkolja azt. Ezen a ponton a szabályok feldolgozása leáll.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).