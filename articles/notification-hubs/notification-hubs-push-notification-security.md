---
title: Notification Hubs biztonsági
description: Ez a témakör ismerteti a biztonság az Azure notification hubs szolgáltatásban.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bd9df12cbe941b868c769daccd02c1d81b39f7bd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465360"
---
# <a name="security-model-of-azure-notification-hubs"></a>Biztonsági modell az Azure Notification hubs használatával

## <a name="overview"></a>Áttekintés

Ez a témakör ismerteti a biztonsági modell az Azure Notification hubs használatával. Mivel a Notification Hubs egy Service Bus entitásban, mint a Service Bus ugyanazt a biztonsági modellt valósítják meg. További információkért lásd: a [Service Bus-hitelesítés](https://msdn.microsoft.com/library/azure/dn155925.aspx) témaköröket.

## <a name="shared-access-signature-security-sas"></a>Közös hozzáférésű Jogosultságkód biztonsági (SAS)

A Notification Hubs valósít meg egy entitás-szintű biztonsági rendszer SAS (közös hozzáférésű Jogosultságkód) néven. Ez a séma lehetővé teszi, hogy a leírás legfeljebb 12 olyan engedélyezési szabályok, amelyek az adott entitástól jogosultságokat deklarálnia üzenetküldési entitások.

Minden szabály nevét, a kulcs értékét (közös titkos kódot) és olyan jogokkal, tartalmazza a "Biztonsági jogcímeinek." szakaszban leírtak szerint Egy értesítési központ létrehozása esetén a rendszer automatikusan létrehozza két szabályt: egy figyelési jogosultsággal (az ügyfél alkalmazás által használt) és egy, az összes jogosultságokkal (azaz a háttéralkalmazás használja).

Végrehajtásakor regisztrációkezelés ügyfél alkalmazásokból, ha az adatokat küldött értesítések nem érzékeny (például az időjárás frissítések), gyakori módja egy értesítési központ eléréséhez a kulcs értékét a szabály-figyelési hozzáférés biztosítása az ügyfélalkalmazás számára és a kulcs értékét a szabály teljes hozzáférés biztosítása a háttéralkalmazás számára.

Nem javasoljuk, hogy a kulcs értékét a Windows Store ügyfélalkalmazások ágyazhat be. Beágyazás a kulcsérték elkerülése érdekében úgy, hogy az ügyfélalkalmazás beolvasni a rendszerindításkor alkalmazási háttérrendszerből rendelkezzen.

Fontos megérteni, hogy a figyelési hozzáféréssel rendelkező kulcs lehetővé teszi, hogy egy ügyfélalkalmazás bármely címke regisztrálhat. Ha az alkalmazás regisztrációk kell korlátozni meghatározott címkéket, hogy bizonyos ügyfelek (például, ha a címkék képviselik a felhasználói azonosítók), majd az alkalmazás háttérrendszere a regisztrációk kell elvégeznie. További információkért lásd: Regisztrációkezelés. Vegye figyelembe, hogy így az ügyfél alkalmazás nem tartalmaz a Notification Hubs közvetlen hozzáférést.

## <a name="security-claims"></a>Biztonsági jogcímek

Más entitásokhoz hasonlóan Notification Hub-műveletek engedélyezett három biztonsági jogcímek: Figyeljen, küldése és kezelése.

| Jogcím   | Leírás                                          | Engedélyezett műveletek |
| ------- | ---------------------------------------------------- | ------------------ |
| Figyelés  | Létrehozni vagy frissíteni, olvassa el, és egyetlen regisztrációkat törölhet | Regisztrációs létrehozása vagy frissítése<br><br>Regisztráció olvasása<br><br>Olvassa el a vonatkozó leírót az összes regisztrációk<br><br>Regisztráció törlése |
| Küldés    | Üzenetek küldése az értesítési központhoz                | Üzenet küldése |
| Kezelés  | A Notification Hubs (beleértve a PNS hitelesítő adatainak és a biztonsági kulcsok frissítése) és a címkék alapján olvasási regisztrációk cRUDs |Létrehozása/frissítése/olvasása/törlése a notification hubs<br><br>Olvassa el a regisztrációk címke szerint |

A Notification Hubs nyújtani a Microsoft Azure hozzáférés-vezérlés tokeneket, és hozza létre az értesítési központ közvetlenül a konfigurált megosztott kulcsokat jogosultságkódok jogkivonataival jogcímeket fogadjon el.
