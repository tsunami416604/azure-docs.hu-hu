---
title: "Logic Apps-összekötők áttekintése |} Microsoft Docs"
description: "A logikai alkalmazás használható összekötők áttekintése"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 6f7d8f99bfa09847c01831a06efa8b94c1c0a89a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="using-connectors-in-a-logic-app"></a>A logikai alkalmazás összekötők használata
Összekötők különböző szolgáltatások, a protokollok és a platformok események, az adatok és a műveletek gyors hozzáférést biztosítanak.  Az összekötők Logic Apps alkalmazásokat támogató teljes listáját is [itt található](apis-list.md).  Összekötők változtatás nélkül használhatók egy eseményindító vagy a logikai alkalmazás egy műveletet, és előfordulhat, hogy a konfigurált *kapcsolat* használni (például: eléréséhez, vagy az Ön nevében utáni Twitter-fiók engedélyezése).

## <a name="basics"></a>Alapvető beállítások
Összekötők a legtöbb esetben érheti el a Dynamics, az Azure, a Salesforce, például más szolgáltatásokkal való integrációjához logikai alkalmazás részeként üzemeltetett szolgáltatások [és további](apis-list.md).  Ezek telepített és Microsoft által felügyelt, a méretezés, a teljesítményt és a biztonsági áll integrációs munkafolyamatok hozhat létre.  Összekötő hozzáadása a logikai alkalmazás keresése, majd válasszon egy összekötő művelet, vagy az Indítás **megjelenítése Microsoft felügyelt API-k**.

![Az eseményindító kiválasztásáról művelet menü][1]

Minden egyes összekötőt művelet vagy az eseményindító fog rendelkeznek a tulajdonságok konfigurálása.  Kattintson az művelettel kapcsolatos további információ gombokra, vagy a dokumentáció [további](apis-list.md).

Ha szeretné integrálni egy szolgáltatás vagy API-t, amely nem még egy összekötőt, a logic apps segítségével is kiterjesztheti a [egyéni összekötő](../logic-apps/logic-apps-create-api-app.md) vagy csak hívása közvetlenül a szolgáltatás egy például a HTTP protokollon keresztül.

## <a name="triggers"></a>Eseményindítók
Összekötők rendelkezik egy eseményindítót, ami azt jelenti, hogy-összekötőről származó esemény lesz logikai alkalmazás tűz- és az eseményindító részeként adjon át adatokat.  Egy eseményindító nem mindig az első lépés egy logikai alkalmazást.  Népszerű eseményindítók olyan műveleteket, például:

* Ismétlődés - minden órában futtatva
* Amikor egy HTTP-kérelem érkezik.
* Amikor egy elem hozzá van adva egy üzenetsorba
* Amikor egy e-mailt érkezik.

Néhány eseményindítók esemény történik, a logikai alkalmazás értesítést keresztül azonnali fog érvényesítést, és mások egy ismétlési időköz, milyen gyakran a logikai alkalmazást az esemény (legfeljebb 15 másodpercenként) a szolgáltatás ellenőrizni fogja konfigurálni kell.  

Miután egy esemény érkezik, a logikai alkalmazást futtatni fogja érvényesítést, és a műveletek a munkafolyamat elindul.  Akkor is érhessék el az adatokat (például az "On egy új tweetet" eseményindító fogja továbbítani a tweetet be a Futtatás) a munkafolyamaton keresztül eseményindítóval.

## <a name="actions"></a>Műveletek
A legtöbb összekötők a munkafolyamat részeként hajt végre egy vagy több műveletet kell végrehajtani.  A műveletek olyan fordulhat elő, miután a Futtatás elindult egy műveleteket.  Egy művelet kattintson hozzáadása a **új lépés** gombra és keresse meg a használni kívánt összekötőt.  Egyszer kiválasztva (és bármely konfigurálása után [kapcsolatok](#connections) , amely segítségére lesz szüksége) jelenik meg a művelet kártya is beállíthat.  Előző lépéseiből adatok bármely a jogkivonatokat a kimenetek kattintva válassza ki, vagy igény szerint adjon meg más beállításokat.

![Egy összekötő művelet konfigurálása][2]

## <a name="connections"></a>Kapcsolatok
A legtöbb összekötők kell beállítani egy *kapcsolat* az összekötő használatához.  A *kapcsolat* minden bejelentkezés vagy kapcsolat konfigurációja, az összekötő eléréséhez szükséges.  Az összekötők, amely OAuth használja, hozza létre a kapcsolat azt jelenti, hogy a szolgáltatásba (például Office 365, a Salesforce vagy a Githubon) Ha titkosított és biztonságosan tárolhatók a Azure titkos tárolóban a a hozzáférési jogkivonat.  Más összekötők (például FTP- és SQL) konfigurációt, például a kiszolgáló címét, a felhasználónevet és jelszót tartalmazó kapcsolat szükséges.  Ezen kapcsolat konfigurációs adatok is titkosítást kapnak, és biztonságosan tárolhatók.  Kapcsolatok tudnak mindaddig, amíg a szolgáltatás lehetővé teszi, hogy a szolgáltatás eléréséhez.  Az Azure Active Directory OAuth-kapcsolatok (például Office 365 és a Dynamics) is továbbra is a hozzáférési jogkivonat határozatlan ideig frissítése.  Egyéb szolgáltatások korlátok elhelyezése lehetséges, hogy mennyi ideig használhatjuk jogkivonat frissítése nélkül.  Általában bizonyos műveletek, például a jelszó módosítása az összes hozzáférési jogkivonatok érvényteleníti.  

Kapcsolatok tekinthetők meg és kattintson az Azure-ban kezelt **Tallózás** választja **API kapcsolatok**.  Az API-kapcsolatok erőforrásból megtekintése, szerkesztése, frissíteni vagy újra engedélyezni, a létrehozott kapcsolatokat.

## <a name="next-steps"></a>További lépések
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Ismerje meg a közös használ és példákat a logic Apps alkalmazások](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Ismerkedés a vállalati integrációs eseményindítók és műveletek](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
