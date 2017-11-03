---
title: "A Notification Hubs biztonsági"
description: "Ez a témakör ismerteti az Azure notification hubs használatával biztonságát."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7c3283799806135060bb8ca57ea398c93d1106bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="security"></a>Biztonság
## <a name="overview"></a>Áttekintés
Ez a témakör ismerteti a biztonsági modellt az Azure Notification Hubs. Mivel a Notification Hubs egy Szolgáltatásbusz-entitás, a Service Bus ugyanazt biztonsági modell valósítják meg. További információkért lásd: a [Service Bus hitelesítési](https://msdn.microsoft.com/library/azure/dn155925.aspx) témaköröket.

## <a name="shared-access-signature-security-sas"></a>Megosztott hozzáférési aláírást biztonsági (SAS)
A Notification Hubs megvalósítja az entitás szintű biztonsági rendszer SAS (közös hozzáférésű Jogosultságkód) néven ismert. Ez a séma lehetővé teszi, hogy az üzenetküldési entitások legfeljebb 12 felhatalmazási szabályokat a leírásában jogot adni az érintett entitásként deklarálnia.

Minden egyes szabály egy nevet, egy kulcs-érték (közös titkos kulcs) és olyan jogokkal, tartalmazza a "Biztonsági jogcímeinek." szakaszban leírtak szerint Ha létrehoz egy értesítési központot, két szabály automatikusan létrejönnek: egy figyelési jogokkal (használ az ügyfélalkalmazás) és egy, az összes jogosultságokkal (azaz a háttéralkalmazás használja).

Végrehajtásakor regisztrációs felügyeleti ügyfél alkalmazásokból, ha az adatokat keresztül küldött értesítések nem érzékeny (például időjárási frissítések), egy értesítési központot eléréséhez egyik elterjedt módja a kulcs értékét a szabály csak hozzáférést biztosítanak az ügyfélalkalmazás, és a kulcs értéke a szabály teljes hozzáférést biztosítanak a háttéralkalmazás.

Nem ajánlott, hogy az ügyfél a Windows Áruházbeli alkalmazások beágyazása a kulcs értékét. A kulcs értéke beágyazás elkerülése érdekében úgy, hogy az ügyfélalkalmazás azt lekérése a háttéralkalmazás indításkor.

Fontos megérteni, hogy a figyelési hozzáféréssel rendelkező kulcs lehetővé teszi, hogy egy ügyfél app bármely címke regisztrálásához. Ha az alkalmazás regisztrációk kell csak adott címkével adott ügyfelek (például, ha a címkék képviselő felhasználói azonosítók), majd a háttéralkalmazás kell elvégeznie a regisztráció. További információkért tekintse meg a regisztrációs felügyeleti. Ne feledje, hogy ezzel a módszerrel az ügyfélalkalmazás fog nem Notification Hubs közvetlen hozzáférést.

## <a name="security-claims"></a>Biztonsági jogcímek
Hasonló egyéb entitások, értesítési központ műveletek végezhetők a három biztonsági jogcímek: figyelésére, küldése és kezelését.

| Jogcím | Leírás | Engedélyezett műveletek |
| --- | --- | --- |
| Figyelés |Létrehozása/frissítése, olvassa el és egyetlen regisztráció törlése |Regisztrációs létrehozása/frissítése<br><br>Olvassa el a regisztrációs<br><br>Olvassa el az összes regisztrációját a számára egy.<br><br>Regisztráció törlése |
| Küldés |Üzenetek küldése az értesítési központ |Üzenet küldése |
| Kezelés |A Notification Hubs (beleértve a PNS hitelesítő adatokat, és a biztonsági kulcsok frissítése), és a címkék alapján olvasható regisztráció cRUDs |Létrehozás/frissítés/olvasás/törlés értesítési központok<br><br>Olvassa el a regisztrációk címke szerint |

Notification hubs használatával a Microsoft Azure Access Control jogkivonatokat, és közvetlenül az értesítési központ konfigurálva megosztott kulccsal rendelkező előállított aláírás jogkivonatokat kap jogcímeket elfogadni.

