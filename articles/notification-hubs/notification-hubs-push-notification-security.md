---
title: Értesítési központok biztonsági modellje
description: Ismerje meg az Azure Értesítési központok biztonsági modelljét.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263761"
---
# <a name="notification-hubs-security"></a>Értesítési központok biztonsága

## <a name="overview"></a>Áttekintés

Ez a témakör az Azure Notification Hubs biztonsági modelljét ismerteti.

## <a name="shared-access-signature-security"></a>Megosztott hozzáférésű aláírás biztonsága

Az értesítési központok egy *megosztott hozzáférésű aláírásnak* (SAS) nevezett entitásszintű biztonsági sémát valósítanak meg. Minden szabály tartalmaz egy nevet, egy kulcsértéket (közös titok) és egy jogosultságkészletet, ahogy azt a [Biztonsági jogcímek](#security-claims)című részben ismertetett. 

Hub létrehozásakor két szabály jön létre automatikusan: az egyik **a listen** jogosultságokkal (hogy az ügyfél alkalmazás használja) és egy **minden** jogosultsággal (hogy az alkalmazás háttérrendszer használ):

- **DefaultListenSharedAccessSignature**: csak **listen** engedélyt ad.
- **DefaultFullSharedAccessSignature**: **figyelő,** **kezelés**és **küldés** engedélyeket ad. Ez a szabályzat csak az alkalmazás háttérszabályában használható. Ne használja ügyfélalkalmazásokban; csak **figyelési** hozzáféréssel rendelkező házirendet használjon. Új egyéni hozzáférési szabályzat létrehozása egy új SAS-jogkivonattal, lásd: [SAS-jogkivonatok hozzáférési szabályzatok](#sas-tokens-for-access-policies) későbbi cikkben.

Ha az értesítéseken keresztül küldött adatok nem érzékenyek (például időjárás-frissítések), az értesítési központ elérésének gyakori módja a szabály csak figyelése az ügyfélalkalmazáshoz való hozzáférésének megadása, és a szabály kulcsértékének teljes hozzáférést biztosít az alkalmazás háttérszabályához.

Az alkalmazások nem ágyazhatják be a kulcsértéket a Windows Áruházbeli ügyfélalkalmazásokba; ehelyett az ügyfélalkalmazás lekéri azt az alkalmazás háttérrendszeréből indításkor.

A **Listen** access billentyű lehetővé teszi, hogy az ügyfélalkalmazás bármely címkéhez regisztráljon. Ha az alkalmazásnak a regisztrációkat adott ügyfelekre kell korlátoznia (például amikor a címkék felhasználói azonosítókat jelölnek), az alkalmazás háttérrendszerének végre kell hajtania a regisztrációkat. További információ: [Registration management](notification-hubs-push-notification-registration-management.md). Vegye figyelembe, hogy ily módon az ügyfélalkalmazás nem rendelkezik közvetlen hozzáféréssel az értesítési központokhoz.

## <a name="security-claims"></a>Biztonsági jogcímek

A többi entitáshoz hasonlóan az Értesítési központ műveletei is három biztonsági jogcímhez engedélyezettek: **Figyelés**, **Küldés**és **Kezelés**.

| Jogcím   | Leírás                                          | Engedélyezett műveletek |
| ------- | ---------------------------------------------------- | ------------------ |
| Figyelés  | Egyetlen regisztráció létrehozása/frissítése, olvasása és törlése | Regisztráció létrehozása/frissítése<br><br>Regisztráció olvasása<br><br>A leíró összes regisztrációjának elolvasása<br><br>Regisztráció törlése |
| Küldés    | Üzenetek küldése az értesítési központba                | Üzenet küldése |
| Kezelés  | CRUD-k az értesítési központokon (beleértve a PNS-hitelesítő adatok és biztonsági kulcsok frissítését), és címkék alapján olvassák a regisztrációkat |Elosztók létrehozása/frissítése/olvasása/törlése<br><br>Regisztrációk olvasása címke szerint |

Az értesítési központok fogadja a közvetlenül a hubon konfigurált megosztott kulcsokkal létrehozott SAS-jogkivonatokat.

Nem lehet egynél több névtérbe értesítést küldeni. A névterek az értesítési központok logikai tárolói, és nem vesznek részt az értesítések küldésében.

Használja a névtérszintű hozzáférési házirendeket (hitelesítő adatokat) a névtérszintű műveletekhez; például: hubok listázása, hubok létrehozása vagy törlése stb. Csak a központi szintű hozzáférési szabályzatok teszik lehetővé az értesítések küldését.

### <a name="sas-tokens-for-access-policies"></a>SAS-jogkivonatok hozzáférési házirendekhez

Új biztonsági jogcím létrehozásához vagy meglévő SAS-kulcsok megtekintéséhez tegye a következőket:

1. Jelentkezzen be az Azure portálra.
2. Válassza az **Összes erőforrás** elemet.
3. Válassza ki annak az értesítési központnak a nevét, amelyhez létre szeretné hozni a jogcímet, vagy meg szeretné tekinteni a SAS-kulcsot.
4. A bal oldali menüben válassza a **Hozzáférési házirendek parancsot.**
5. Új biztonsági jogcím létrehozásához válassza az **Új házirend** lehetőséget. Adjon nevet a házirendnek, és válassza ki az megadni kívánt engedélyeket. Ezután kattintson az **OK** gombra.
6. A teljes kapcsolati karakterlánc (beleértve az új SAS-kulcsot is) megjelenik az Access Policies ablakban. Ezt a karakterláncot átmásolhatja a vágólapra későbbi használatra.

A SAS-kulcs kinyeréséhez egy adott házirendből, válassza a **Másolás gombot** a kívánt SAS-kulcsot tartalmazó házirend mellett. Illessze be ezt az értéket egy ideiglenes helyre, majd másolja a kapcsolati karakterlánc SAS-kulcsrészét. Ez a példa egy **mytestnamespace1**nevű értesítési központ névteret és egy policy2 nevű **házirendethasznál.** A SAS-kulcs a string vége közelében lévő, **SharedAccessKey**által megadott érték:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS-kulcsok bekésezése](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>További lépések

- [Értesítési központok – áttekintés](notification-hubs-push-notification-overview.md)
