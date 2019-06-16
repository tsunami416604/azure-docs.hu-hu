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
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431257"
---
# <a name="notification-hubs-security"></a>Notification Hubs biztonsági

## <a name="overview"></a>Áttekintés

Ez a témakör ismerteti a biztonsági modell az Azure Notification hubs használatával.

## <a name="shared-access-signature-security-sas"></a>Közös hozzáférésű Jogosultságkód biztonsági (SAS)

A Notification Hubs valósít meg egy entitás-szintű biztonsági rendszer SAS (közös hozzáférésű Jogosultságkód) néven. Ez a séma lehetővé teszi, hogy a leírás legfeljebb 12 olyan engedélyezési szabályok, amelyek az adott entitástól jogosultságokat deklarálnia üzenetküldési entitások.

Minden szabály nevét, a kulcs értékét (közös titkos kódot) és olyan jogokkal, tartalmaz leírtak [biztonsági jogcímeinek](#security-claims). Egy értesítési központ létrehozása, amikor két szabály automatikusan jönnek létre: egyet a **figyelésére** jogokkal (vagyis az ügyfél-alkalmazás által használt) és a egy **összes** jogokkal (azaz a háttéralkalmazás használja).

Végrehajtásakor regisztrációkezelés ügyfél alkalmazásokból, ha az adatokat küldött értesítések nem érzékeny (például az időjárás frissítések), gyakori módja egy értesítési központ eléréséhez a kulcs értékét a szabály-figyelési hozzáférés biztosítása az ügyfélalkalmazás számára és a kulcs értékét a szabály teljes hozzáférés biztosítása a háttéralkalmazás számára.

Alkalmazások nem kell a kulcsérték beágyazása Windows Store ügyfélalkalmazások, inkább rendelkezik az indításkor alkalmazási háttérrendszerből lekérni az ügyfélalkalmazás.

A kulcs **figyelésére** hozzáférés lehetővé teszi, hogy egy ügyfélalkalmazás bármely címke regisztrálhat. Ha az alkalmazás regisztrációk kell korlátozni meghatározott címkéket, hogy bizonyos ügyfelek (például, ha a címkék képviselik a felhasználói azonosítók), az alkalmazás háttérrendszere végezze el a regisztrációkat. További információkért lásd: [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md). Vegye figyelembe, hogy így az ügyfél alkalmazás nem tartalmaz a Notification Hubs közvetlen hozzáférést.

## <a name="security-claims"></a>Biztonsági jogcímek

Más entitásokhoz hasonlóan Notification Hub-műveletek engedélyezett három biztonsági jogcímek: **Figyeljen**, **küldése**, és **kezelése**.

| Jogcím   | Leírás                                          | Engedélyezett műveletek |
| ------- | ---------------------------------------------------- | ------------------ |
| Figyelés  | Létrehozni vagy frissíteni, olvassa el, és egyetlen regisztrációkat törölhet | Regisztrációs létrehozása vagy frissítése<br><br>Regisztráció olvasása<br><br>Olvassa el a vonatkozó leírót az összes regisztrációk<br><br>Regisztráció törlése |
| Küldés    | Üzenetek küldése az értesítési központhoz                | Üzenet küldése |
| Kezelés  | A Notification Hubs (beleértve a PNS hitelesítő adatainak és a biztonsági kulcsok frissítése) és a címkék alapján olvasási regisztrációk cRUDs |Létrehozása/frissítése/olvasása/törlése a notification hubs<br><br>Olvassa el a regisztrációk címke szerint |

Notification Hubs fogad aláírás jogkivonatok jön létre a megosztott kulcsok közvetlenül az értesítési központ konfigurálva.

Nem alkalmas egynél több névteret értesítést küldeni. Névterek logikai tároló, a notification hubs szolgáltatásra, és nem vesz részt az értesítések küldéséhez.
A névtér-szintű hozzáférési szabályzatok (hitelesítő adatok) is használható névtérszintű műveletekhez, például: a notification hubs listázása, létrehozása vagy törlése a notification hubs, stb. Csak a hub szintű hozzáférést szeretne szabályzatokkal értesítéseket küldeni.
