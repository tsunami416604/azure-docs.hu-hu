---
title: "SendGrid használata az Azure Functions |} Microsoft Docs"
description: "Bemutatja, hogyan használható az Azure Functions SendGrid"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: rachelap
ms.openlocfilehash: 563bdac30176de131f6d8fd194713f482ceb75d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Az Azure Functions SendGrid használata

## <a name="sendgrid-overview"></a>SendGrid áttekintése

Az Azure Functions támogatja a SendGrid kimeneti kötések néhány sornyi kódot és egy SendGrid-fiókot az e-mailek küldése a funkciók engedélyezéséhez.

Egy Azure-függvényt a SendGrid API használatához rendelkeznie kell egy [SendGrid fiók](http://SendGrid.com). Emellett rendelkeznie kell egy SendGrid API-kulcsot. Jelentkezzen be a SendGrid-fiókját, majd kattintson a **beállítások** majd **API-kulcs** API-kulcs létrehozásához. Tartsa meg a rendelkezésre álló kulcs használata azt egy későbbi lépésben.

Most már készen áll az Azure-függvény alkalmazás létrehozásához.

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása 

Az Azure functions egy vagy több Azure függvény alkalmazások tárolói. Az Azure functions rendszer éppen ez - függvényt. Minden Azure függvény egy eseményindítót, a függvény futtatásához okozó esemény van kötve.
Minden függvény tartalmazhatnak tetszőleges számú bemeneti vagy kimeneti kötések. Kötések a szolgáltatások, hogy a függvényben használható. SendGrid egy e-mailek küldése segítségével kimeneti kötése. 

1. Jelentkezzen be az Azure-portálon és [hozzon létre egy Azure-függvény alkalmazást](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) vagy nyisson meg egy meglévő függvény alkalmazást. 
2. Egy Azure-függvény létrehozása. Legyen egyszerű, hogy válasszon ki egy kézi indítási és a C#. 

 ![Egy Azure-függvény létrehozása](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Egy Azure-függvény alkalmazása SendGrid konfigurálása

A SendGrid API-kulcs történő használatát a következő függvényt egy alkalmazás beállításként kell tárolnia. A ApiKey mező nem a tényleges SendGrid API-kulcsot, de egy alkalmazás, beállítás határozza meg, amely a tényleges API-kulcs jelöli. A biztonság érdekében a kulcs tárolása így azért, mert a kódok vagy a fájlokat, előfordulhat, hogy ellenőrizni kell a verziókövetési elkülönül.

- Hozzon létre egy **AppSettings** kulcs az függvény alkalmazás **Alkalmazásbeállítások**.

 ![Egy Azure-függvény létrehozása](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Konfigurálja a SendGrid kimeneti kötések

SendGrid áll rendelkezésre, mert az Azure függvény kimeneti kötése. A SendGrid létrehozásához kimeneti kötése:

1. Lépjen a **integráció** lapon, a függvény az Azure portálon.
2. Kattintson a **új kimeneti** létrehozása a SendGrid kimeneti kötése.
3. Töltse ki a **API-kulcs** és **üzenet paraméternév** tulajdonságait. Ha azt szeretné, adja meg a többi tulajdonság most, vagy inkább code őket. Ezek a beállítások alapértelmezett értéke használható.

 ![Konfigurálja a SendGrid kimeneti kötések](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

Egy olyan függvényt ad hozzá egy kötés létrehoz egy nevű **function.json** a függvény mappában. Ez a fájl tartalmazza ugyanazokat az Azure függvényben információt **integráció** lapon, de a Json formátumban. Beállítás a **ApiKey**, **üzenet**, és **a** mezők létrehozása a következő bejegyzések kerülhetnek a **function.json** fájlt: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Tetszés szerint módosíthatja a fájlt közvetlenül a saját maga.

Most, hogy létrehozta és konfigurálva, a függvény alkalmazás és a függvény, egy e-mailt küldhet a kódot is írhat.

## <a name="write-code-that-creates-and-sends-email"></a>Kód írása, amely létrehoz és e-mailt küld

A SendGrid API létrehozása és elküldése e-mailt kell minden parancsot tartalmazza.  

- Cserélje le a kódot, a függvény a következő kódot:

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Figyelje meg, az első sor tartalmazza a ```#r``` direktíva, amely a SendGrid szerelvényre hivatkozik. Ezután használhatja a ```using``` utasítás használatával egyszerűen hozzáférhessenek az objektumok adott névtérben. A kód létrehozása a példányai ```Mail```, ```Personalization```, és ```Content``` a SendGrid API az e-mailt alkotó objektumokat. Amikor visszatér az üzenetet, a SendGrid nyújt. 

A függvényaláíráshoz is tartalmaz egy plusz típusú paraméter out ```Mail``` nevű ```message```. Mindkét bemeneti és kimeneti kötések express maguk függvény paramétereiben kódot. 

2. Gombra kattintva tesztelheti a kódját **teszt** , majd gépelje be az üzenetet a **Request body** mezőben, majd kattintson a **futtatása** gombra.

 ![Tesztelheti a kódját](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Győződjön meg arról, hogy a SendGrid az e-mailben küldött e-mailek ellenőrzése. Azt kell nyissa meg a címet a kódban 1. lépésben, és a üzenete tartalmazza a **Request body**.

## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatta, hogyan használható a SendGrid szolgáltatás létrehozásához és e-mailek küldése. Az Azure Functions alkalmazásokban való használatáról a következő témakörökben talál további információt: 

- [Gyakorlati tanácsok az Azure Functions](functions-best-practices.md) néhány ajánlott eljárás az Azure Functions létrehozásakor használandó sorolja fel.

- [Az Azure Functions fejlesztői segédanyagai](functions-reference.md) programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.

- [Az Azure Functions tesztelése](functions-test-a-function.md) különböző eszközöket és technikákat a funkciók ismerteti.