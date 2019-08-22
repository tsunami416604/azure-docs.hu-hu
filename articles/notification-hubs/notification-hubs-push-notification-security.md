---
title: Notification Hubs biztonság
description: Ez a témakör az Azure Notification hubok biztonságát ismerteti.
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
ms.openlocfilehash: 73a6d0eaab286dec9d02bb55eb75f0781bcffcc4
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891576"
---
# <a name="notification-hubs-security"></a>Notification Hubs biztonság

## <a name="overview"></a>Áttekintés

Ez a témakör az Azure Notification Hubs biztonsági modelljét ismerteti.

## <a name="shared-access-signature-security-sas"></a>Közös hozzáférésű aláírás biztonsága (SAS)

Notification Hubs megvalósítja a SAS (közös hozzáférési aláírás) nevű entitás szintű biztonsági sémát. Minden szabály tartalmaz egy nevet, egy kulcs értéket (közös titkos kulcsot) és egy jogosultsági készletet, a [biztonsági](#security-claims)jogcímek szerint. Az értesítési központ létrehozásakor a rendszer automatikusan két szabályt hoz létre: egyet a figyelési jogokkal (az ügyfélalkalmazás által használt), egyet pedig **minden** jogosultsággal (az alkalmazás hátterében).

Ha az ügyfélalkalmazások regisztrációs felügyeletet végeznek, ha az értesítéseken keresztül küldött információk nem érzékenyek (például időjárás-frissítések), az értesítési központ elérésének közös módja, ha a szabály kulcsfontosságú értékét figyeli, hogy csak az ügyfélalkalmazás férhessen hozzá. a szabály kulcsfontosságú értékeként pedig teljes hozzáférést biztosíthat az alkalmazási háttérhez.

Az alkalmazások nem ágyazzák be a kulcs értékét a Windows áruházbeli ügyfélalkalmazásokba, hanem az alkalmazás-háttérből az indításkor lekérik az alkalmazást.

A figyelési hozzáféréssel rendelkező kulcs lehetővé teszi, hogy az ügyfélalkalmazás bármely címkére regisztrálhat. Ha az alkalmazásnak meghatározott felhasználóknak kell korlátoznia a regisztrációkat adott címkékre (például ha a címkék felhasználói azonosítókat jelölnek), az alkalmazás-háttérnek el kell végeznie a regisztrációt. További információ: [regisztráció kezelése](notification-hubs-push-notification-registration-management.md). Vegye figyelembe, hogy az ügyfélalkalmazás így nem lesz közvetlen hozzáférése Notification Hubshoz.

## <a name="security-claims"></a>Biztonsági jogcímek

A többi entitáshoz hasonlóan a Notification hub műveletei három biztonsági jogcímet is használhatnak: **Figyelés**, **Küldés**és **kezelés**.

| Igénylés   | Leírás                                          | Engedélyezett műveletek |
| ------- | ---------------------------------------------------- | ------------------ |
| Figyelés  | Egyszeri regisztrációk létrehozása/frissítése, olvasása és törlése | Regisztráció létrehozása/frissítése<br><br>Regisztráció beolvasása<br><br>Egy leíró összes regisztrációjának olvasása<br><br>Regisztráció törlése |
| Küldés    | Üzenetek küldése az értesítési központnak                | Üzenet küldése |
| Felügyelet  | Notification Hubs (beleértve a PNS hitelesítő adatainak frissítését és a biztonsági kulcsokat), valamint a címkék alapján történő regisztrációk beolvasása |Értesítési központok létrehozása/frissítése/olvasása/törlése<br><br>Regisztrációk beolvasása címke szerint |

Notification Hubs fogadja az értesítési központban közvetlenül konfigurált megosztott kulcsokkal generált aláírási jogkivonatokat.

Nem lehet értesítést küldeni több névtérnek. A névterek az értesítési központok logikai tárolói, és nem vesznek részt az értesítések küldésében.
A névtér szintű hozzáférési házirendek (hitelesítő adatok) a névtér szintű műveletekhez használhatók, például: értesítési központok listázása, értesítési központok létrehozása vagy törlése stb. Csak a hub szintű hozzáférési házirendek küldhetnek értesítéseket.
