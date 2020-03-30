---
title: Azure VMware-megoldás a CloudSimple-től – Tűzfaltáblák és -szabályok beállítása
description: Ez a témakör azt ismerteti, hogy miként állíthatók be a magánfelhőbeli tűzfaltáblák és az alhálózatok és a VLAN-ok forgalmának korlátozására vonatkozó szabályok.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244666"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Tűzfaltáblák és szabályok beállítása a magánfelhőkhöz

A tűzfaltáblák és a kapcsolódó szabályok lehetővé teszik az adott alhálózatokra és VLAN-okra vonatkozó forgalomkorlátozásainak megadását.

* Az alhálózat egy tűzfaltáblához társítható.
* A tűzfaltábla több alhálózathoz is társítható.

## <a name="add-a-new-firewall-table"></a>Új tűzfaltábla hozzáadása

1. Az oldalsó menüben [elérheti a CloudSimple portált,](access-cloudsimple-portal.md) és válassza a **Hálózat** lehetőséget.
2. Válassza **a Tűzfaltáblák lehetőséget.**
3. Válassza **a Tűzfaltábla létrehozása lehetőséget.**

    ![VLAN/alhálózati lap](media/firewall-tables-page.png)

4. Adja meg a tábla nevét.
5. A táblázat alapértelmezett szabálya szerepel a listában. További szabály létrehozásához kattintson az **Új szabály létrehozása** gombra. A részleteket az alábbi eljárásban találja.
6. A tűzfaltábla mentéséhez kattintson a **Kész** gombra.

> [!IMPORTANT]
> Privát felhőnként legfeljebb két tűzfaltáblát hozhat létre.

## <a name="firewall-rules"></a>Tűzfalszabályok

A tűzfalszabályok határozzák meg, hogy a tűzfal hogyan kezelje az egyes típusú forgalmat. A kijelölt tűzfaltábla **Szabályok** lapja felsorolja az összes kapcsolódó szabályt.

![Tűzfalszabályok táblája](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Tűzfalszabály létrehozása

1. A beállítások megjelenítése a tűzfalszabály létrehozásához az alábbi módokon:
    * Tűzfaltábla létrehozásakor kattintson a **Szabály hozzáadása** gombra.
    * Jelöljön ki egy adott tűzfaltáblát a **Hálózati > tűzfaltáblák** lapon, és kattintson **az Új tűzfalszabály létrehozása gombra**.
2. Állítsa be a szabályt a következőképpen:
    * **Név**. Adjon nevet a szabálynak.
    * **Prioritás**. Rendeljen prioritást a szabályhoz. Először az alacsonyabb számú szabályok lesznek végrehajtva.
    * **Forgalom típusa**. Válassza ki, hogy a szabály a private cloud, internet, vagy VPN-forgalom (állapot nélküli) vagy egy nyilvános IP-cím (állapotalapú).
    * **protokollt**. Válassza ki a szabály hatálya alá tartozó protokollt (TCP, UDP vagy bármely protokoll).
    * **irány.** Adja meg, hogy a szabály bejövő vagy kimenő forgalomra van-e. A bejövő és a kimenő forgalomhoz külön szabályokat kell meghatároznia.
    * **Művelet**. Válassza ki azt a műveletet, amelyet akkor szeretne végrehajtani, ha a szabály egyezik (engedélyezés vagy megtagadás).
    * **Forrás**. Adja meg a szabály hatálya alá tartozó forrásokat (CIDR blokk, belső vagy bármilyen forrás).
    * **Forrásport tartománya**. Adja meg a szabály hatálya alá tartozó portok tartományát.
    * **irány.** Válassza ki a bejövő vagy kimenő lehetőséget.
    * **Rendeltetési hely**. Adja meg a szabály által lefedett célokat (CIDR blokk, belső vagy bármilyen forrás).
    * **Forrásport tartománya**. Adja meg a szabály hatálya alá tartozó portok tartományát.

    ![Tűzfaltábla hozzáadása szabály](media/firewall-rule-create.png)

3. Kattintson a **Kész** gombra a szabály mentéséhez és a tűzfaltábla szabályainak listájához való hozzáadásához.

> [!IMPORTANT]
> Minden tűzfaltábla legfeljebb 10 bejövő és 20 kimenő szabállyal rendelkezhet. Ezek a korlátok növelhetők a [támogatási szolgálathoz való felvételsel.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>VLAN-ok/alhálózatok csatolása

A tűzfaltábla definiálása után megadhatja azokat az alhálózatokat, amelyekre a táblában szereplő szabályok vonatkoznak.

1. A **Hálózati** > **tűzfal táblák** lapon jelöljön ki egy tűzfaltáblát.
2. Nyissa meg a **Csatolt VLAN-ok/Alhálózat** lapot.
3. Kattintson **a Csatolás VLAN/Alhálózathoz gombra.**
4. Válassza ki a privát felhő és a VLAN. A társított alhálózat neve és cidr blokkja látható.
5. Kattintson a **Küldés gombra.**
