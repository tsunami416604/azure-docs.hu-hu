---
title: Az Azure Table |} A Microsoft Docs
description: Az Azure Table érdeklődő felügyeletének konfigurálásához.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810892"
---
<a name="lead-management-instructions-for-azure-table"></a>Az Azure Table utasítások felügyeleti vezethet
============================================

Ez a cikk ismerteti a konfigurálása az Azure Table potenciális vásárlók tárolásához. Az Azure Table lehetővé teszi, hogy tárolhatja, és testre szabhatja a vásárlói adatokat.

## <a name="to-configure-azure-table"></a>Az Azure Table konfigurálása

1.  Ha nem rendelkezik Azure-fiókkal, akkor [hozzon létre egy ingyenes próbafiókot](https://azure.microsoft.com/pricing/free-trial/).

2.  Miután az Azure-fiókjával aktív, jelentkezzen be a [az Azure portal](https://portal.azure.com).
3.  Az Azure Portalon hozzon létre egy tárfiókot. A következő képernyőfelvétel bemutatja, hogyan hozzon létre egy tárfiókot. További információ a storage szolgáltatás díjszabása: [storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

    ![Az Azure storage-fiók létrehozásának lépései](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  A kulcs a tárfiók kapcsolati sztringje másolja és illessze be azt a **Tárfiók kapcsolati Sztringje** mezőjében a Cloud Partner portálra. A kapcsolati karakterlánc például `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Azure storage-kulcs](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Használhat [az Azure storage explorer](http://azurestorageexplorer.codeplex.com/) vagy más eszköz a storage-táblába az adatok megtekintéséhez. Az Azure-táblában lévő adatokat exportálhatja is.
adatok.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Nem kötelező)**  Azure Functions használata az Azure-tábla

Ha hogyan azért küldtük Önnek, használja az érdeklődők testreszabni kívánt [Azure Functions](https://azure.microsoft.com/services/functions/) az Azure-tábla. Az Azure Functions szolgáltatás lehetővé teszi az Érdeklődők generálása folyamat automatizálása.

A következő lépések bemutatják, hogyan hozhat létre Azure-függvény, amely egy időzítő. 5 percenként a függvény az új rekordokat az Azure-tábla és e-mail-értesítés küldése a SendGrid szolgáltatás használja.


1.  [Hozzon létre](https://portal.azure.com/#create/SendGrid.SendGrid) SendGrid ingyenes szolgáltatásfiók az Azure-előfizetésében.

    ![A SendGrid létrehozása](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  A SendGrid API-kulcs létrehozása 
    - Válassza ki **kezelés** , nyissa meg a SendGrid felhasználói felület
    - Válassza ki **beállítások**, **API-kulcsok**, majd hozzon létre egy kulcsot, amelynek a levelek küldése –\> teljes hozzáférés
    - Az API-kulcs mentése


    ![A SendGrid API-kulcs](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Hozzon létre](https://portal.azure.com/#create/Microsoft.FunctionApp) egy Azure-függvényalkalmazást a csomag lehetőséggel nevű, "Használatalapú csomagban".

    ![Azure-Függvényalkalmazás létrehozása](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Hozzon létre egy új függvény definíciója.

    ![Azure-függvény definíciójának létrehozása](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  A függvény, amely egy adott időben frissítést küld lekéréséhez válassza ki a **TimerTrigger-CSharp** alapszintű paramétert.

     ![Az Azure függvény idő eseményindító lehetőség](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Cserélje le a "Fejlesztés" kódot az alábbi kódmintát. A küldő és fogadó használni kívánt címekkel e-mail címek módosítása.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Az Azure függvény kódrészlet](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Válassza ki **integráció** és **bemenetek** meghatározásához az Azure Table-kapcsolat.

    ![Azure-függvény integrálása](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Adja meg a táblázat nevét, és állítsa be a kapcsolati karakterlánc megfelelő **új**.


    ![Azure-függvény táblát kapcsolat](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Most a kimeneti definiálhatja a SendGrid, és hagyja változatlanul az alapértelmezett beállításokat.

    ![A SendGrid-kimenet](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![A SendGrid kimeneti alapértelmezései](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Függvényalkalmazás-beállításokat a neve "SendGridApiKey" és az API-kulcsokat a SendGrid felhasználói felületen kapott értéket a SendGrid API-kulcs hozzáadása

    ![A SendGrid kezelése](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid kulcs kezelése](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Ha végzett a függvény konfigurálása, a kód az integrálás szakaszban a következő példához hasonlóan kell kinéznie.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. Az utolsó lépés az, hogy keresse meg a függvény fejlesztése felhasználói felületén, majd **futtatása** az időzítő indítására. Most már értesítést fog kapni minden alkalommal, amikor új érdeklődő érhető el.
