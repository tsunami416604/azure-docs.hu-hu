---
title: 'Azure Active Directory Domain Services: Biztonságos LDAP-hibák megoldása | Microsoft Docs'
description: Hibaelhárítási Secure LDAP Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8a542f7927ddd834c7273f6ef8b251ddc35e8436
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234188"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services – Secure LDAP konfiguráció hibaelhárítása

Ez a cikk a [biztonságos LDAP](configure-ldaps.md) a Azure ad domain Serviceshoz való konfigurálásakor felmerülő gyakori problémák megoldásait ismerteti.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Secure LDAP hálózati biztonsági csoport konfigurálása

**Riasztási üzenet:**

*Az interneten keresztüli Secure LDAP engedélyezve van a felügyelt tartományhoz. Az 636-as porthoz való hozzáférés azonban nem áll le hálózati biztonsági csoport használatával. A felhasználók a felügyelt tartomány felhasználói fiókjait felhasználhatják az elkövetett jelszavakkal kapcsolatos támadásokra.*

### <a name="secure-ldap-port"></a>Secure LDAP port

Ha engedélyezve van a biztonságos LDAP, javasoljuk, hogy hozzon létre további szabályokat, amelyekkel a bejövő LDAP-hozzáférés csak bizonyos IP-címekről engedélyezett. Ezek a szabályok olyan találgatásos támadásokkal védik a tartományt, amelyek biztonsági fenyegetést jelenthetnek. A 636-es port lehetővé teszi a hozzáférést a felügyelt tartományhoz. Az alábbi módon frissítheti a NSG a Secure LDAP való hozzáférés engedélyezéséhez:

1. Navigáljon a [hálózati biztonsági csoportok lapra](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) a Azure Portal
2. Válassza ki a tartományhoz társított NSG a táblából.
3. Kattintson a **bejövő biztonsági szabályok** elemre.
4. A 636-as port létrehozása szabály
   1. Kattintson a felső navigációs sávon a **Hozzáadás** gombra.
   2. Válassza ki a forrás **IP-címeit** .
   3. A szabályhoz tartozó forrásport-tartományok megadása.
   4. "636" bemenet a célport tartományához.
   5. A protokoll **TCP**.
   6. Adjon a szabálynak megfelelő nevet, leírást és prioritást. A szabály prioritásának magasabbnak kell lennie, mint az "összes letiltása" szabály prioritása, ha rendelkezik ilyennel.
   7. Kattintson az **OK** gombra.
5. Ellenőrizze, hogy létrejött-e a szabály.
6. Ellenőrizze a tartomány állapotát két órán belül, és győződjön meg arról, hogy megfelelően végrehajtotta-e a lépéseket.

> [!TIP]
> A 636-es port nem az egyetlen olyan szabály, amely a Azure AD Domain Services zökkenőmentes futtatásához szükséges. További tudnivalókért tekintse meg a [hálózati irányelveket](network-considerations.md) , vagy hárítsa el a [NSG konfigurációs](alert-nsg.md) cikkeit.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP a tanúsítvány lejárata

**Riasztási üzenet:**

*A felügyelt tartomány biztonságos LDAP-tanúsítványa a következő időpontban lejár: [date]].*

**Megoldás:**

Hozzon létre egy új biztonságos LDAP-tanúsítványt a [biztonságos LDAP konfigurálása](configure-ldaps.md) című cikkben ismertetett lépéseket követve.

## <a name="contact-us"></a>Kapcsolat
A [visszajelzések megosztásához és](contact-us.md)a támogatáshoz forduljon a Azure Active Directory Domain Services termék csapatához.
